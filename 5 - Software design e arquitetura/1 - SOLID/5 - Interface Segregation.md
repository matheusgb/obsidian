Definição de [[1 - O que é SOLID#^2cd042|Interface Segregation]].

> 💡 Uma classe não deve ser obrigada a implementar interfaces e métodos que não utilizará. Em outras palavras, é melhor ter 6 interfaces bem específicas, do que 2 mais genéricas.

Embora Go não implemente orientação a objetos, **Interface Segregation** pode ser aplicado na linguagem sem problemas. Isso devido a forma como structs e [[1 - Trabalhando com interfaces|interfaces]] funcionam na linguagem.

Para ter uma idéia mais clara de como utilizar esse conceito, vamos nos aprofundar um pouco mais no package `io`. Explorando o repositório da linguagem Go, no arquivo `[src/io/io.go](<https://github.com/golang/go/blob/master/src/io/io.go>)`, a partir da linha 83, podemos ver uma grande quantidade de interfaces sendo definidas. No entanto, vamos focar em 3.

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}
```

Como podemos ver, cada uma das interfaces necessita somente 1 método para ser implementada, ou seja, são extremamente específicas.

Outro ponto interessante de ter interfaces tão específicas quanto as que vimos, é que podemos utilizar a técnica de [[2 - Embedding (herança)|embedding]] para gerar outras interfaces.

```go
type ReadWriter interface {
    Reader
    Writer
}

type ReadCloser interface {
    Reader
    Closer
}

type WriteCloser interface {
    Writer
    Closer
}

type ReadWriteCloser interface {
    Reader
    Writer
    Closer
}
```

Dessa forma, funções como `bufio.NewReaderSize` – que aceita um parâmetro do tipo `io.Reader`-, e atributos como o `http.Request.Body` – que é do tipo `io.ReadCloser`-, conseguem facilmente garantir que a struct não implemente um método que não será utilizado.