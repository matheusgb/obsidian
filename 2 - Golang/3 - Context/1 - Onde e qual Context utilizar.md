### Background context

```
ctx := context.Background()
```
Esse tipo de context não pode ser cancelado, ter valores ou deadline. No entanto ele pode ser passado para outras funções onde pode ser extendido para adicionar a habilidade de cancelamento, deadlines e valores.

Contexts vazios normalmente são utilizados na primeira camada da hierarquia de funções que utilizam context ou em testes.


### TODO context

```
ctx := context.TODO()
```
Embora essa função retorne EXATAMENTE o mesmo tipo de context que a função anterior, o TODO é utilizado quando não for claro qual tipo de context utilizar ou a utilização do context ainda não estiver disponível.

Um exemplo disso seria um package que tem uma API pública onde em futuras releases aceitará context, mas que ainda não faz nada com ele. Assim, para não causar incompatibilidade futura com a API pública, suas primeiras versões recebem um context do tipo TODO.


### Context WithCancel

```
ctx, cancelFunc := context.WithCancel(context.Background())
```
Essa função retorna uma cópia do context que foi passado para ela com um novo channel no atributo `Done`, para controlar o cancelamento do context atual ou do seu context pai.

Além do context, essa função também retorna uma função de cancelamento. Essa função pode ser executada a qualquer momento, cancelando assim a execução da função que recebeu esse novo context.

Quando executada, o context irá fechar o channel que foi criado no atributo `Done` e liberará todo recurso associado a execução.

Um exemplo de utilização, seria para lidar com grandes processamentos, onde o cliente precisa ficar conectado ao servidor. Caso essa conexão caia, podemos chamar a função de cancelamento para finalizar a execução.


### Context WithDeadline

```
ctx, cancelFunc := context.WithDeadline(context.Background(), time.Now().Add(5 * time.Second))
```
Similar ao context que vimos anteriormente, nesse caso também teremos o retorno de um novo context, tendo a base passada como context pai, e uma função para cancelamento.

A diferença aqui é que, mesmo que a função de cancelamento não seja chamada, caso a execução da função demore mais do que `d`(segundo parâmetro da função), o channel será fechado por ter excedido o tempo limite de execução.

Um bom exemplo de utilização desse tipo de context, é para quando precisamos executar requests a APIs externas ao nosso software.


### Context WithTimeout

```
ctx, cancelFunc := context.WithTimeout(context.Background(), 5 * time.Second)
```
Se olharmos o código fonte do Go, veremos que esse tipo de context é literalmente um wrapper para simplificar a chamada do tipo anterior.
```
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc) {
    return WithDeadline(parent, time.Now().Add(timeout))
}
```



### Context WithValue

```
ctx := context.WithValue(context.Background(), "user_id", 4510)
```
Nessa chamada, um novo context será retornado contendo a chave e valor informados. Assim como nos anteriores, a “base” passada ficará como context pai do que foi retornado.

Como nós NUNCA devemos modificar uma request HTTP, esse tipo de context é normalmente utilizado para passar dados extras de request para outras funções, como por exemplo o id do usuário que está autenticado.

Dois pequenos avisos:

1. Embora tanto `key` quanto `value` sejam do tipo `any`, existe uma validação interna nessa função que verifica se a key é do tipo comparable, ou seja, ela precisa ser de um tipo que podemos utilizar == ou ≠.
2. **NUNCA** utilize esse tipo de context para passar parâmetros opcionais para uma função. Isso não sou eu falando, mas sim o **TIME** de desenvolvimento do Go.