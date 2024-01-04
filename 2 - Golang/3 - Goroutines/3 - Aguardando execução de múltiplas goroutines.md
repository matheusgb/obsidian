Utilizar um channel para controlar a quantidade de [[1 - O que são e como funcionam as Goroutines#^165c0f|goroutines]] que já finalizaram a execução pode ser muito trabalhoso e em alguns casos até impossível já que, para um channel simples, teríamos que esperar a execução de cada goroutine antes de iniciar a próxima.

Se resolvemos usar um channel com buffer, teremos que especificar sua capacidade durante sua criação, o que também pode nos levar a criar um buffer muito pequeno, onde não conseguimos iniciar todas as goroutines que queremos de uma só vez.

Mas se gerir a execução das goroutines com [[1 - O que são e como utilizar channels|channels]] pode ser trabalhoso ou em alguns casos até mesmo inviável, como podemos fazer?

Simples, utilizando um package nativo do Go que chama `sync`. Para ser mais específico, uma struct chamada [[2 - Quando utilizar goroutines#^3a15de|WaitGroup]]. Essa struct contém 3 métodos para nos auxiliar nessa tarefa.

- **Add:** Incrementa o número de goroutines em execução;
- **Done:** Decrementa o número de goroutines em execução;
- **Wait:** Aguarda que o número de goroutines em execução chegue a zero.

Vamos escrever um pequeno programa para executar um número aleatório de goroutines, entre 0 e 100, onde cada goroutine terá um Sleep entre 0s e 10s.

```go
package main

import (
    "fmt"
    "math/rand"
    "sync"
    "time"
)

func process(num int, wg *sync.WaitGroup) {
    fmt.Printf("iniciando goroutine num %d \n", num)
    time.Sleep(time.Second * time.Duration(rand.Intn(10)))
    fmt.Printf("finalizando goroutine num %d \n", num)

    wg.Done()
}

func main() {
    limit := rand.Intn(100)
    
    var wg sync.WaitGroup
    for i := 0; i <= limit; i++ {
        wg.Add(1)
        go process(i, &wg)
    }

    wg.Wait()

    fmt.Printf("%d goroutines foram finalizadas com sucesso! \n", limit)
}
```

Nesse exemplo, podemos ver os 3 métodos sendo aplicados.

Antes de iniciar cada uma das goroutines, chamamos o métodos `Add` para incrementar o número de goroutines em execução.

Passamos o ponteiro da struct para a função `process`, onde ao final da sua execução chama o método `Done` da struct `WaitGroup`, para que seja feito um decremento no número de goroutines.

Fora do nosso _for_, chamamos o método `Wait`, para que controle principal do programa fique bloqueado até que todas as goroutines sejam finalizadas.