Definição:
>Classes “filhas” podem ser utilizadas no lugar das classes “pais” sem que o programa quebre. Em outras palavras, uma classe filha, mesmo com suas especificidades, deve manter o mesmo comportamento da classe pai.

Aqui, antes de continuar, vale fazer duas observações.

1. Não existe orientação a objetos em Go, logo não temos classes.
2. Como não temos classes, não temos herança.

Onde o princípio diz classe, pense em structs. Já onde diz herança, pense na [[2 - Embedding (herança)|técnica de embedding]].

## Assinatura do método

Embora esse princípio possa soar um tanto quanto estranho (ainda mais pelo fato do Go ser uma linguagem de tipo estática), sua definição não deve ser considerada na literalidade. Na verdade, Liskov Substitution nos diz que, em caso de uma classe filha sobrescrever o método de uma classe pai, os parâmetros e retorno – incluindo seus tipos – deverão ser mantidos iguais ao da classe pai.

Exemplo:
```go
// struct PAI
type Transporte struct {
    Modelo string
    Tanque uint8
}

func (t *Transporte) Ligar() error {
    if t.Tanque == 0 {
        return errors.New("Tanque está vazio")
    }

    return nil
}

// struct FILHA
type CarroHibrido struct {
    Transporte
    Bateria uint8
}

func (c *CarroHibrido) Ligar() error {
    if c.Bateria == 0 && c.Tanque == 0 {
        return errors.New("Bateria descarregada e tanque vazio")
    }

    return nil
}
```

Como podemos ver, ambas structs implementam o método `Ligar` com os mesmos parâmetros, ou seja, nenhum, e o mesmo retorno.

Embora não se aplique ao Go, vale salientar que, em caso de linguagens com orientação a objeto, as exceções lançadas pelo método da classe pai também deverão ser mantidas. Caso a classe filha necessite de alguma outra, essa deverá extender de algumas das exceções existentes na classe pai.

## Comportamento

Antes de entrar em detalhes, vamos adicionar outra struct filha ao exemplo acima.

```go
type Bicicleta struct {
    Transporte
}

func (b *Bicicleta) Ligar() error {
    return nil
}
```

Agora vamos entender como ao adicionar essa simples struct, violamos o princípio de Liskov Substitution.
### Invariante

Embora uma bicicleta seja um tipo de transporte, quando olhamos para a definição de transporte do sistema, vemos que é necessário um tanque – coisa que bicicleta não tem -, e ser possível ligar, o que também não é possível em uma bicicleta (pelo menos não nas convencionais).

Sendo assim, ela não pode ser uma classe (struct) filha de Transporte.
### Pré e pós condições

Dentro do princípio de Liskov Substitution, não é permitido o fortalecimento de pré-condições, assim como o enfraquecimento de pós-condições.

Em outras palavras, fortalecer uma pré-condição se dá quando, se no método da classe pai, o valor esperado de um input do tipo numérico seja menor do que 18, não podemos, na classe filha, modificar essa regra para que atenda as condições da classe.

Já em relação as pós-condições, um método sobrescrito nunca pode fazer menos do que o da classe pai. Exemplos disso seriam, deixar de salvar um dado, não fechar uma conexão, não enviar uma notificação e etc…