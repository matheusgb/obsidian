Ao escrever uma mensagem no [[1 - O que são e como utilizar channels|channel]], a [[1 - O que são e como funcionam as Goroutines#^165c0f|goroutine]] que escreveu fica aguardando até que outra goroutine faça a leitura daquela informação. Somente após a leitura é que a goroutine de escrita consegue voltar a escrever algo no channel.

Utilizar buffer de mensagens faz com que a goroutine de escrita seja bloqueado de escrever no channel somente quando o buffer estiver cheio. Já a de leitura só ficará bloqueada quando o buffer estiver vazio. Logo, utilizar um buffer de mensagens pode ajudar com que o trabalho das goroutines seja mais fluido.

Para criar um channel com buffer, tudo que precisamos fazer é passar um parâmetro adicional na sua criação para definir a capacidade máxima.

```go
ch := make(chan type, capacidade)
```

Para que o channel seja criado com buffer, o valor da capacidade deve ser superior a 0.

Pode parecer um pouco estranho o segundo parâmetro ser a capacidade já que, para arrays, o segundo parâmetro define o tamanho e o terceiro parâmetro a capacidade. A explicação para isso é que, para channels, o tamanho é definido conforme o buffer é utilizado.

Para exemplificar o impacto de se ter um channel com buffer, vamos fazer umas pequenas mudanças no [[1 - O que são e como utilizar channels#^e590b4|último código que escrevemos anteriormente]]:

```go
package main

import (
    "fmt"
    "time"
)

func numeros(v chan<- int) {
    for i := 0; i < 10; i++ {
        v <- i
        fmt.Printf("número %d escrito no channel\n", i)
    }
    close(v)
}

func main() {
    c := make(chan int, 3)
    go numeros(c)

    time.Sleep(time.Second * 2)
    for v := range c {
        fmt.Printf("número %d lido do channel\n", v)
        time.Sleep(time.Second * 2)
    }
}
```

Básicamente nós fizemos 3 mudanças no código:

- Criamos o channel com buffer de 3 mensagens;
- Adicionamos log na escrita e na leitura
- Adicionamos um sleep de 2s antes de iniciar a leitura e entre cada leitura

Ao fazer o `go run` devemos ver o seguinte output.

```
número 0 escrito no channel
número 1 escrito no channel
número 2 escrito no channel
número 0 lido do channel
número 3 escrito no channel
número 1 lido do channel
número 4 escrito no channel
número 2 lido do channel
número 5 escrito no channel
número 3 lido do channel
número 6 escrito no channel
número 4 lido do channel
número 7 escrito no channel
número 5 lido do channel
número 8 escrito no channel
número 6 lido do channel
número 9 escrito no channel
número 7 lido do channel
número 8 lido do channel
número 9 lido do channel
```

Como podemos ver, a goroutine de escrita foi capaz de escrever mensagens no channel até que ele estivesse completamente cheio, ou seja, com tamanho 3.

Após a primeira leitura, ambas goroutines foram intercalando leitura e escrita, até que a goroutine de escrita não tivesse mais nada para escrever. Desse ponto em diante, vemos a goroutine de leitura lendo os 3 últimos valores do channel, finalizando nosso programa com sucesso.

Vale ressaltar que, após a goroutine de escrita finalizar suas tarefas, o channel foi fechado, e que isso não impediu que a goroutine de leitura continuasse trabalhando até que o buffer fosse completamente esvaziado.