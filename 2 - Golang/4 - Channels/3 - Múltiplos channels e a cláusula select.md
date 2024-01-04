A cláusula select é utilizada para que uma função consiga trabalhar com múltiplos [[1 - O que são e como utilizar channels|channels]]. Ela bloqueia a execução da função até que um dos channels esteja pronto para ser executado. Caso mais de um channel esteja pronto para ser executado, ela selecionará de forma aleatória qual executar.

Para tentar ficar um pouco mais claro, vamos escrever um pequeno programa para ilustrar o comportamento.

Primeiramente vamos criar uma função que vai ficar somando números aleatórios infinitamente. Essa função receberá 2 channels por parâmetro, sendo um para comunicar o valor atual da somatória e outro para receber o sinal de exit.

```go
func sum(total chan int, exit chan bool) {
    valor := rand.Intn(20)
    for {
        select {
            case total <- valor:
                valor += rand.Intn(20)

            case <-exit:
                fmt.Println("exit :)")
                return
        }
    }
}
```

Mesmo a função tendo um loop infinito, o channel `exit` irá controlar o fim da sua execução, já que quando ele receber um valor, o `return` será executado, matando assim a execução do loop.

Outro ponto que precisamos estar atentos ao escrever a função principal é que o channel `total` não terá [[2 - Buffer de mensagens em channels|buffer]]. Dessa forma, a soma do próximo número aleatório só irá acontecer após alguma outra rotina ler a última soma executada.

Agora vamos escrever a função principal para utilizar essa função de soma.
```go
func main() {
    total := make(chan int)
    exit := make(chan bool)

    go func() {
        for i := 0; i < 10; i++ {
            fmt.Println(<-total)
        }

        exit <- true
    }()

    sum(total, exit)
}
```

Na nossa função principal, criamos uma [[1 - O que são e como funcionam as Goroutines|goroutine]] anônima para controlar a execução da função sum. Como limitamos o loop em 10 voltas e a cláusula `select` bloqueia a execução até que um dos channels esteja pronto, o número de voltas do loop da função sum deve ser muito parecido com o da nossa função anônima.

Para testar nosso código é só executar um `go run main.go`. A saída será algo similar a isso:

```
1
8
15
34
35
53
58
58
74
74
exit :)
```
