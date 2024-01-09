Definição:
> Dependências devem ser abstraídas, para que os módulos de alto nível não dependam dos módulos de baixo nível.

## Sem Dependency Inversion:

```go
// package database, arquivo connect.go
func Connect() (*sql.DB, error) {
    // faz conexão com banco de dados
}

// package category, arquivo repository.go
type Category struct {
    ID int64
    Name string
}

func List() ([]Category, error) {
    db, err := database.Connect()
    if err != nil {
        panic(err)
    }
  defer db.Close()

    // implementa select com variável db

    return c, err
}

// package api, arquivo categories.go
func ListCategories(rw http.ResponseWrite, r *http.Request) {
    categories, err := category.List()
    // ...
}

// package main, arquivo main.go
func main() {
    r := chi.NewRouter()

    r.Get("/category", api.ListCategories)
    // ...
}
```

Como diz o conceito, as dependências entre módulos de alto nível (package api) e módulos de baixo nível (package category) devem ser abstraídas.

Sendo assim, para não violar esse princípio, a melhor forma é utilizar [[1 - Trabalhando com interfaces|interfaces]] em conjunto com a estratégia de Dependency Injection (DI).

Se você não está familiarizado com essa estratégia, ela consiste em injetar dependências em funções através de parâmetros.

No entanto, injetar a dependência de conexão em cada uma das funções que realizam transação com banco de dados pode tornar o código muito complexo e difícil de manter.

## Adicionando repository

Para adicionar o Dependency Inversion Principle ao exemplo acima, começaremos refatorando o arquivo _repository.go_ do package **category***.* Nele, vamos adicionar uma interface do repository, uma struct que implemente a interface e “converter” a função `List` para um método da struct.

```go
// package category, arquivo repository.go
type Repository interface {
    List() ([]Category, error)
}

type SQLRepository struct {
    db *sql.DB
}

func NewSQLRepository(db *sql.DB) *SQLRepository {
    return &SQLRepository{db}
}

type Category struct {
    ID int64
    Name string
}

func (r SQLRepository) List() ([]Category, error) {
    // implementa select com variável r.db

    return c, err
}
```

Com essas mudanças, como podem ver, a conexão com o banco de dados deixou de ser iniciada na função List. Agora, ela é passada para o repository via parâmetro, na chamada da função `NewSQLRepository`.

Dessa forma, todos os métodos do repository conseguirão executar transações com o banco de dados através do atributo `db`.

## Dependency Injection

^d80039

Dando continuidade a refatoração, no package **api**, vamos adicionar um novo arquivo, o **routes.go.** Nesse arquivo ficará a struct responsável por implementar os métodos que tratarão as requests HTTP.

```go
// package api
// arquivo routes.go
type handler struct {
    cr category.Repository
}

func SetRoutes(r chi.Router, repository category.Repository) {
    h := handler{
        cr: repository,
    }

    r.Get("/category", h.ListCategories)
}

// arquivo categories.go
func (h *handler) ListCategories(rw http.ResponseWrite, r *http.Request) {
    categories, err := h.cr.List()
    // ...
}
```

Se você reparar bem, verá que a struct `handler` tem um atributo do tipo `category.Repository`. Isso faz com que, todos os métodos acessem o repository indiretamente.

Assim, caso seja feita alguma mudança a nível de repository, por exemplo mudar o banco de dados, nenhuma alteração seria necessária na implementação dos métodos que lidam com requests [[1 - Se aprofundando em HTTP|HTTP]].

## Injetando dependências

Para finalizar, vamos refatorar o package **main**, para que ele injete as dependências necessárias na chamada da função `SetRoutes`.

```go
// package main, arquivo main.go
func main() {
    r := chi.NewRouter()

    db, err := database.Connect()
    if err != nil {
        panic(err)
    }
    defer db.Close()

    api.SetRoutes(r, category.NewSQLRepository(db))
    // ...
}
```


