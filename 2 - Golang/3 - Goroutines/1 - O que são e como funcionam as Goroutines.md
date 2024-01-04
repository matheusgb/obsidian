Goroutines são funções ou métodos executados em [[0 - A diferença entre concorrência e paralelismo#^cd219e|concorrência]]. Podemos pensar nelas como uma espécie de lightweight thread que são gerenciadas pelo runtime do Go. ^165c0f

Chamamos de lightweight thread pois o custo para sua criação é muito menor quando comparada com um thread de verdade. Outro ponto positivo é que o runtime consegue aumentar ou diminuir a quantidade de goroutines de acordo com a necessidade da aplicação, enquanto o número de thread normalmente é fixo.

É comum uma única thread conter milhares de goroutines sendo executadas. Caso alguma dessas goroutines bloqueie a execução das outras por estar aguardando algum tipo de input do usuário, por exemplo, o runtime do Go irá criar uma nova thread e mover as outras goroutines para essa thread.

Goroutines rodam no mesmo espaço de memória, o que facilita para compartilhar objetos em memória, mas também devemos tomar cuidado para manter sincronizado. Os [[1 - O que são e como utilizar channels|channels]] ajudam nesse processo de comunicação entre goroutines, além de evitar race condition.

Sempre que queremos iniciar uma nova goroutine, tudo o que precisamos fazer é adicionar a palavra `go` antes da chamada da função/método.

```go
go foo(a, b, c)
```

_foo_, _a_, _b_ e _c_ são interpretadas na goroutine em que a chamada ocorreu, porém sua execução acontecerá em uma nova goroutine.

Basicamente tudo em Go funciona com goroutines, inclusive a execução principal do programa. Por isso, qualquer chamada de uma nova goroutine acontece dentro de uma goroutine.

```go
package main

import "fmt"

func hello() {
    fmt.Println("eu sou uma nova goroutine")
}

func main() {
    go hello()
    fmt.Println("eu sou a goroutine principal")
}
```
Resposta:
```
eu sou a goroutine principal
```

 Após a nova goroutine ser iniciada, o programa já executa a próxima linha, que no nosso caso é o `fmt.Println("eu sou a goroutine principal")` .

Para fazer um “fix” no nosso código, vamos adicionar um `time.Sleep(time.Second * 1)` logo após o `fmt` da função main. ^fbe0e4

Agora sim conseguimos ver a execução da goroutine acontecendo.

A mudança foi chamada de “fix” pois essa definitivamente não é a forma ideal de se esperar a execução de uma goroutine. Para isso existem os channels.

```go
package main

import (
    "fmt"
    "time"
)

func numeros() {
    for i := 0; i < 10; i++ {
        fmt.Printf("%d ", i)
        time.Sleep(time.Millisecond * 200)
    }
}

func letras() {
    for l := 'a'; l < 'j'; l++ {
        fmt.Printf("%c ", l)
        time.Sleep(time.Millisecond * 300)
    }
}

func main() {
    go numeros()
    go letras()
    time.Sleep(5 * time.Second)
    fmt.Printf("fim da execução!\n")
}
```
Resposta:
```
0 a 1 b 2 c 3 4 d 5 e 6 7 f 8 g 9 h i fim da execução!
```