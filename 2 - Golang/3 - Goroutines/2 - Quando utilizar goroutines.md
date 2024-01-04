Antes de adicionar [[1 - O que são e como funcionam as Goroutines#^165c0f|goroutines]], é bom analisar as funções que você utiliza em seu código e se elas não fazem uso de goroutines, como por exemplo o tratamento de requests [[1 - Se aprofundando em HTTP#^64574c|HTTP]].

A implementação do package `net/http` abre uma nova goroutine para cada request recebida. Sendo assim, não faz sentido que a cada request recebida você abra outra goroutine para lidar com a request.

Agora, se você já fez um benchmark + profile e entendeu o que as funções ao redor do seu código fazem, e você precisa/pode ter o processamento de tarefas de forma concorrente, aí sim chegou a hora de realmente pensar em goroutines.

Pensar em goroutines é mudar o pensamento comum e sequencial para um pensamento concorrente, ou seja, onde múltiplas tarefas acontecerão ao mesmo tempo.

Como exemplo, vamos imaginar um endpoint que recebe o cadastro de uma pessoa. Esse cadastro, além das informações de texto, recebe 3 imagens (foto da pessoa, cpf e rg). Essas imagens são salvas em buckets na cloud onde a aplicação está hospedada.

```go
func upload(img []byte) {
    // FAZ UPLOAD
}

func HandleAdd(w http.ResponseWriter, r *http.Request) {
    // PARSE BODY

    upload(p.Image)
    upload(p.Cpf)
    upload(p.Rg)

    // continua
}
```

Nesse cenário, poderíamos adicionar uma goroutine para o upload de cada imagem. Porém, como disse anteriormente, esse caso não seria um simples `go func`, pois é necessário que nossa goroutine principal saiba se houve algum erro ao tentar fazer o upload de qualquer uma das imagens.

Para conseguir tratar a resposta de cada um dos uploads, podemos ter um [[1 - O que são e como utilizar channels|channel]] de tamanho 3, onde cada goroutine de upload vai escrever um erro caso não tenha conseguido fazer o upload da imagem.

É importante também que tenhamos criado um `*sync.WaitGroup` na goroutine principal para gerir a execução das goroutines de upload. ^3a15de

No final, para que o “é só adicionar um `go` na frente da chamada da função” funcionasse de forma correta, teríamos alterado nosso código para algo parecido com isso:

```go
func upload(img []byte, cerr chan<- error, wg *sync.WaitGroup) {
    // FAZ UPLOAD
    cerr <- err
    wg.Done()
}

func HandleAdd(w http.ResponseWriter, r *http.Request) {
    // PARSE BODY
       
    var (
        errs := make(chan error, 3)
        wg sync.WaitGroup
    )
    wg.Add(3)

    go upload(p.Image, errs, wg)
    go upload(p.Cpf, errs, wg)
    go upload(p.Rg, errs, wg)

    wg.Wait()

    close(errs)

    for err := errs {
        if err != nil {
           // trata erro
        }
    }

    // continua
}
```

Conselho final para saber se convém adicionar uma goroutine à sua aplicação, é pensar se aquela tarefa ou conjunto de tarefas pode ser executada de forma concorrente e sem aumentar incrivelmente a complexidade para manutenção do código.

Nunca se esqueça de criar mecanismos para validação e controle das goroutines, para que elas não “saiam de controle” e acabem prejudicando sua aplicação.
