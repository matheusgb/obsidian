Podemos pensar em channels como sendo uma espécie de túnel de comunicação entre [[1 - O que são e como funcionam as Goroutines#^165c0f|goroutines]], onde uma goroutine consegue enviar informações para outra antes mesmo de terminar sua execução. Nesse mesmo cenário, a goroutine que recebe a informação, ficaria pausada até as informações chegarem.

Quando vamos declarar um channel ou inicializa-lo, precisamos associar um tipo de dado a ele. Esse tipo de dado é o tipo que ele estará apto à transportar entre goroutines.

Ao declarar uma channel, seu valor inicial será `nil`. Para inicia-lo, devemos usar a função `make` do Go.

```go
var c chan int
fmt.Printf("c é nil: %v\n", c == nil)

c = make(chan int)
fmt.Printf("c é nil: %v", c == nil)
```

Normalmente channels são declarados e inicializados ao mesmo tempo.

```go
c := make(chan int)
```

Para escrever ou ler informações de um channel utilizamos o sinal `<-` que serve como uma seta indicadora. Se ela estiver apontando para o channel estamos escrevendo, do contrário estamos lendo.

```go
data := <- c // lendo
c <- pessoa // escrevendo
```

Por padrão, sempre que há uma escrita ou leitura em um channel, ambas goroutines ficam bloqueadas. No caso, quando uma goroutine escreve algo em um channel, ela fica bloqueada até que outra goroutine leia. Já para goroutines que fazem leitura de um channel, essas ficam bloqueadas até que outra goroutine escreva algo no channel.

Caso uma goroutine escreva em um channel mas nenhuma outra faça a leitura, ou uma goroutine faça a leitura mas nenhuma outra escreva, o programa irá parar com um erro de **deadlock**.

```
fatal error: all goroutines are asleep - deadlock! goroutine 1 [chan send]: main.main() /tmp/sandbox046150166/main.go:12 +0x50
```

Agora vamos fazer do jeito certo aquele [[1 - O que são e como funcionam as Goroutines#^fbe0e4|fix do exemplo.]]

```go
package main

import "fmt"

func hello(done chan bool) {
    fmt.Println("eu sou uma nova goroutine")
    done <- true
}

func main() {
    done := make(chan bool)
    go hello(done)
    <- done
    fmt.Println("eu sou a goroutine principal")
}
```

^aa659e

Removemos o `time.Sleep` e modificamos a assinatura da função `hello` para que ela pudesse receber o channel que criamos dentro da execução principal.

E por falar em assinatura de função, no exemplo anterior estamos passando um channel bidirecional para a função `hello`, ou seja, ela pode ler e escrever no channel. No caso dessa função, ela poderia receber um channel direcional, ou seja, que ela possa somente ler ou escrever.

Para fazer essa definição, tudo o que precisamos fazer é adicionar uma seta na assinatura da função, onde `<-chan type` diz que o channel é somente leitura e `chan<- type` somente escrita.

```go
package main

import "fmt"

func hello(done chan <- bool) {
    fmt.Println("eu sou uma nova goroutine")
    done <- true
}

func main() {
    done := make(chan bool)
    go hello(done)
    <- done
    fmt.Println("eu sou a goroutine principal")
}
```

Com essa pequena mudança, garantimos que a função `hello` irá somente escrever no channel **done**.

Quando fazemos leitura de um channel, podemos adicionar uma variável extra que nos diz que estamos lendo de um channel aberto ou fechado.

```go
v, ok := <- c
```
`ok` será **true** quando o channel estiver aberto e **false** quando estiver fechado.

```go
package main

import "fmt"

func numeros(v chan<- int) {
    for i := 0; i < 10; i++ {
        v <- i
    }
    close(v)
}

func main() {
    c := make(chan int)
    go numeros(c)

    for {
        v, ok := <-c
        if ok == false {
            break
        }
        fmt.Println("valor:", v)
    }
}
```

Uma outra forma mais simples para escrever esse mesmo código seria utilizando `for range`.

```go
package main

import "fmt"

func numeros(v chan<- int) {
    for i := 0; i < 10; i++ {
        v <- i
    }
    close(v)
}

func main() {
    c := make(chan int)
    go numeros(c)

    for v := range c {
        fmt.Println("valor:", v)
    }
}
```

^e590b4

Loops do tipo `for range` ficam lendo dados de um channel até que o channel seja fechado.