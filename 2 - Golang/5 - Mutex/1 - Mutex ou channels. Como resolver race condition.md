O termo race condition é utilizado para descrever um pedaço do código que será executado por múltiplas goroutines e que, a cada execução, seu resultado pode variar devido a forma como o Go alterna a execução entre [[1 - O que são e como funcionam as Goroutines|goroutines]].

```go
package main

import (
    "fmt"
    "sync"
)

var total = 0

func count(wg *sync.WaitGroup) {
    total++
    wg.Done()
}

func main() {
    var wg sync.WaitGroup

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go count(&wg)
    }
    wg.Wait()
    fmt.Println("total: ", total)
}
```

Esse código vai iniciar 1000 goroutines. Cada goroutine vai incrementar a variável total em 1. Ao final o valor total será exibido.

Se esse código fosse executado sem goroutines, obviamente que no final o valor da variável `total` seria 1000. No entanto, como você pode ver no print abaixo, o fato de usar goroutines muda tudo.

![[Pasted image 20240103222000.png]]

Como dito no inicio, devido a forma como o Go alterna entre a execução das goroutines não ser idêntico a cada execução, o resultado final obtido nas execuções, além de incorreto foi diferente.

Isso acontece por que o acesso a variável `total` está sendo compartilhada entre as goroutines, o que acaba fazendo com que o resultado final seja diferente do esperado.

Para corrigir isso, precisamos fazer uma espécie de semáforo, para que, quando uma goroutine estiver acessando a variável `total` as outras goroutines aguardem “sua vez”.

No código abaixo, podemos ver a utilização de Mutex para corrigir o problema.

```go
package main

import (
    "fmt"
    "sync"
)

var total = 0

func count(wg *sync.WaitGroup, m *sync.Mutex) {
    m.Lock()
    total++
	m.Unlock()
    wg.Done()
}

func main() {
    var (
        wg sync.WaitGroup
        m  sync.Mutex
    )

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go count(&wg, &m)
    }
    wg.Wait()
    fmt.Println("total: ", total)
}
```

Quando utilizamos mutex, qualquer parte de código entre os métodos `Lock` e `Unlock` ficam bloqueados para outras goroutines. Somente depois que o método `Unlock` for executado é que uma segunda goroutine pode acessar as variáveis ali contidas.

Se executarmos o nosso programa novamente, vamos ver o seguinte resultado:
![[Pasted image 20240103222220.png]]

Outra forma para resolver o problema de race condition seria utilizando channels, como podemos ver no exemplo abaixo:

```go
package main

import (
    "fmt"
    "sync"
)

var total = 0

func count(wg *sync.WaitGroup, c chan bool) {
    c <- true
    total++
    <-c
    wg.Done()
}

func main() {
    var wg sync.WaitGroup
    c := make(chan bool, 1)

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go count(&wg, c)
    }
    wg.Wait()
    fmt.Println("total: ", total)
}
```

Como nosso channel só tem uma posição, ao escrever true nele, bloqueamos outras goroutines de continuarem sua execução.

**Ok, mas se os dois fazem a mesma coisa, qual é a melhor forma para evitar race condition?**

Embora muitos desenvolvedores, principalmente os que estão iniciando agora na linguagem, gostem muito de resolver tudo com channels, vou deixar uma pequena dica aqui.

Use channels somente quando uma goroutine precisar se comunicar com outra. Se o desejado for um semáforo para controlar acesso a partes suscetíveis a race condition dentro de uma mesma função, utilize mutex.