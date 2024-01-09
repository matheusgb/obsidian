Definição:
>💡 Uma classe deve estar aberta para extensões, mas fechada para modificações. Ou seja, sempre que for necessário adicionar funcionalidades, devemos estender a classe ao invés de modificá-la

No Go não há classes e, embora o uso de [[2 - Embedding (herança)|embedding]] possa ser a solução em alguns casos, o fato de, em alguns momentos usarmos a relação struct → classe, não quer dizer que ela sempre será utilizada ou válida. Em outras palavras, sempre fazer o paralelo entre structs e classes pode nos levar a alguns erros graves.

Como Go não tem orientação a objetos, aplicar o princípio Open-Closed na linguagem pode ser um pouco diferente.

## Sem Open-Closed

No exemplo abaixo, temos uma função que recebe um source, de onde deverá ler uma lista de cidades e retorná-las em forma de um slice de City.

```go
func GetCities(sourceType string, source string) ([]City, error) {
    var data []byte
    var err error

    if sourceType == "file" {
        data, err = ioutil.ReadFile(source)
        if err != nil {
            return nil, err
        }
    } else if sourceType == "link" {
        resp, err := http.Get(source)
        if err != nil {
            return nil, err
        }

        data, err = ioutil.ReadAll(resp.Body)
        if err != nil {
            return nil, err
        }
        defer resp.Body.Close()
    }

    var cities []City
    err = yaml.Unmarshal(data, &cities)
    if err != nil {
        return nil, err
    }

    return cities, nil
}
```

Bem simples, certo? Porém, o que vai acontecer com essa função caso eu queira adicionar outro source? Vou ter que modificá-la, certo? Isso é uma violação do princípio, mais especificamente da parte que diz “…mas fechada para modificações.”.

Embora o inicio do princípio diga “uma classe”, na verdade ele pode ser aplicado em **classes, funções e métodos**.

## Com Open-Closed

Agora, na função refatorada abaixo, repare como ganhamos extensibilidade sem ter mais que fazer modificações na função em si.

```go
// declara um novo tipo
type DataReader func(source string) ([]byte, error)

// ex bloco de if sourceType == "file"
func ReadFromFile(fileName string) ([]byte, error) {
    data, err := ioutil.ReadFile(fileName)
    if err != nil {
        return nil, err
    }

    return data, nil
}

// ex bloco de if sourceType == "link"
func ReadFromLink(link string) ([]byte, error) {
    resp, err := http.Get(link)
    if err != nil {
        return nil, err
    }

    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        return nil, err
    }
    defer resp.Body.Close()

    return data, nil
}

// Função passa receber a função de leitura como parâmetro
func GetCities(reader DataReader, source string) ([]City, error) {
    data, err := reader(source)
    if err != nil {
        return nil, err
    }

    var cities []City
    err = yaml.Unmarshal(data, &cities)
    if err != nil {
        return nil, err
    }

    return cities, nil
}

// depois só passar a função que tem q ser executada como parâmetro (reader)
```

Como a função `GetCities` agora recebe um `DataReader`, ou seja, uma função com a assinatura `func(source string) ([]byte, error)` como primeiro parâmetro, além das duas funções já existentes, podemos criar novas funções para ler de outros sources.

Em outras palavras, nossa função agora respeita 100% o princípio Open-Closed, pois ela está **aberta para extensões e fechada para modificações**.

## Exemplo do package `io`

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

func ReadAll(r Reader) ([]byte, error) {
    b := make([]byte, 0, 512)
    for {
        n, err := r.Read(b[len(b):cap(b)])
        b = b[:len(b)+n]
        if err != nil {
            if err == EOF {
                err = nil
            }
            return b, err
        }

        if len(b) == cap(b) {
            // Add more capacity (let append pick how much).
            b = append(b, 0)[:len(b)]
        }
    }
}
```

Nesse exemplo, a função aguarda por uma interface do tipo Reader, ou seja, uma struct que implemente a função Read.

Dessa forma, assim como no nosso primeiro exemplo, ela se mantém extensível sem a necessidade de modificação.