No exemplo de chamada, será utilizado o context [[1 - Onde e qual Context utilizar#^91be6f|WithTimeout]]. Dessa forma, conseguiremos fazer a função ser cancelada automaticamente, caso o tempo de execução dela ultrapasse o tempo estipulado no context.

Vamos iniciar criando uma função com o nome `doSomeHeavyWork`.

```go
func doSomeHeavyWork(ctx context.Context, msg string) {
}
```

Para simular um processamento longo, vamos adicionar uma goroutine com um sleep de 2 segundos. Essa [[1 - O que são e como funcionam as Goroutines|goroutine]] irá receber um channel do tipo bool. Ele irá sinalizar que a goroutine foi finalizada.

```go
func doSomeHeavyWork(ctx context.Context, msg string) {
    c := make(chan bool)

    go func(c chan bool) {
        time.Sleep(2 * time.Second)
        c <- true
    }(c)
}
```

Na struct `context.Context`, temos um método chamado `Done()`. Esse método retorna um channel. Quando o context for finalizado, esse channel será fechado. Isso ocorrerá quando:
- [[1 - Onde e qual Context utilizar#^659e8e|WithCancel]] – quando a função cancel for executada;
- [[1 - Onde e qual Context utilizar#^845e55|WithDeadline]] – quando a deadline expirar;
- [[1 - Onde e qual Context utilizar#^91be6f|WithTimeout]] – quando timeout for atingido.

Como temos um channel de controle para o sleep e outro do context, precisamos adicionar um **select** para capturar o channel que foi acionado primeiro.

```go
func doSomeHeavyWork(ctx context.Context, msg string) {
    c := make(chan bool)

    go func(c chan bool) {
        time.Sleep(2 * time.Second)
        c <- true
    }(c)

    select {
    case <-ctx.Done():
        fmt.Println("context timeout!")
    case <-c:
        fmt.Println(msg)
    }
}
```

Para testar nossa função, vamos criar um context com timeout de 1 segundo.

```go
func main() {
    ctx, _ := context.WithTimeout(context.Background(), time.Second*1)

    doSomeHeavyWork(ctx, "work completed!")
}
```

Ao executar o programa com um `go run main.go`, teremos o seguinte resultado:

```
$ context timeout!
```
Alterando o tempo do nosso timeout para 3 segundos e, executando novamente o programa, o resultado será a mensagem de `work completed!`.