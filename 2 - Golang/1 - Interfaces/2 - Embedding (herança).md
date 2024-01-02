Embora o Golang não implemente orientação a objetos, de forma superficial, sua estratégia de embedding é bastante utilizada e muito parecida com a famosa herança.

Exemplo:
Vamos criar 3 [[1 - Trabalhando com interfaces|interfaces]]. _Writer_, _Reader_ e _Closer_.:

```go
type Writer interface {
    Write(p []byte) (n int, err error)
}

type Reader interface {
    Read(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}
```

Com essas 3 interfaces criadas, já podemos criar funções e métodos que esperem structs que implementem uma dessas interfaces.

Agora, e se em determinado método eu precisar de uma struct que implemente o método `Write` e o método `Read`? Como não podemos definir 2 tipos – afinal nem faria sentido fazer isso – para um mesmo parâmetro da função, só nos resta utilizar a estratégia de embedding.

Para resolver o problema citado acima, e reaproveitar as interfaces que já foram declaradas, vamos criar uma nova interface que faça o embedding das interfaces **Writer** e _**Reader.**_

```go
type ReadWriter interface {
    Reader
	Writer
}
```

Exemplo de aplicação:
```go
type Animal interface {
	Onomatopeia() string
}

type Lugar interface {
	SomAmbiente() string
}

type AnimalLugar interface {
	Animal
	Lugar
}

type Urso struct{}

func (u Urso) Onomatopeia() string {
	return "Roarrrrr"
}

func (u Urso) SomAmbiente() string {
	return "*vento*"
}

type Tucano struct{}

func (t Tucano) Onomatopeia() string {
	return "Tri tri tri"
}

func (t Tucano) SomAmbiente() string {
	return "*som de cachoeira*"
}

func Play(animal AnimalLugar) {
	fmt.Println(animal.Onomatopeia())
}

func main() {
	Play(Urso{})
	Play(Tucano{})
}
```
