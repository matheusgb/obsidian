SOLID é um acrônimo que representa cinco princípios no design de softwares orientados a objetos, sendo:

> **S**ingle Responsability  
> **O**pen-Closed  
> **L**iskov Substitution  
> **I**nterface Segregation  
> **D**ependency Inversion Principle

Embora Go não seja uma linguagem orientada a objetos, ainda assim conseguimos utilizar os princípios de SOLID. Abaixo, vamos ver com mais detalhes cada um dos princípios.

## Single Responsability

^92fdc5

Uma classe ou, como é no caso do Go, uma struct deve ter uma única responsabilidade. Ou seja, cada struct deve ser projetada para executar uma determinada ação.

Pensando em um CRUD, é claro que você não terá uma struct para cada uma das ações (Create, Read, Update e Delete). Mas também não terá somente uma struct para lidar com todas as responsabilidades do package.

O ideal é ter pelo menos uma struct que represente a entidade do package. Uma para lidar com as transações com banco de dados, comumente chamada de repository. E outra para tratar as requests, independente se forem via API ou em algum modelo MVC.

## Open-Closed

Uma classe deve estar aberta para extensões, mas fechada para modificações. Ou seja, sempre que for necessário adicionar funcionalidades, devemos estender a classe ao invés de modificá-la.

Embora seja possível através da estratégia de [[2 - Embedding (herança)|embedding]], esse princípio é um pouco mais difícil de ser aplicado em Go.

Isso por que, diferente de uma linguagem que implementa orientação a objetos, em Go, nem tudo estará atrelado a uma struct.

Mesmo assim, para um maior reaproveitamento das funções do sistema, é importante que elas tenham suas responsabilidades muito bem definidas.

## **L**iskov Substitution

Classes “filhas” podem ser utilizadas no lugar das classes “pais” sem que o programa quebre.

Esse é outro princípio que, para ser aplicado ao Go, talvez exija algumas mudanças que em outras linguagens, como, por exemplo, o PHP, não é necessário.

Como Go é uma linguagem fortemente tipada, não é possível passar struct do tipo `Bar`, onde só é aceito structs do tipo `Foo`.

Outro ponto que vale lembra é que, em Go, não existe o conceito de herança. Logo, mesmo que uma struct utilize-se da estratégia de embedding, ela não tem relação direta com a outra struct como acontece na herança de classe.

## **I**nterface Segregation

^2cd042

Uma classe não deve ser obrigada a implementar interfaces e métodos que não utilizará. Em outras palavras, é melhor ter 6 interfaces bem específicas, do que 2 mais genéricas.

Esse princípio, inclusive, é amplamente aplicado no core da linguagem Go. Se olharmos para o package `io`, temos, por exemplo, as interfaces:

- io.Reader
- io.Writer
- io.ReadWriter

Onde a terceira nada mais é do que um embedding das duas primeiras.

## **D**ependency Inversion Principle

Dependências devem ser abstraídas, para que os módulos de alto nível não dependam dos módulos de baixo nível.

> 🚨 ATENÇÃO! [[6 - Dependency Inversion|Dependency Inversion]] e [[6 - Dependency Inversion#^d80039|Dependency Injection]] são coisas distintas.

Uma forma de implementar esse conceito, é utilizar interfaces para definir atributos de structs e parâmetros de funções. Assim, ao invés de esperar um tipo concreto, ou seja, altamente acoplado, esperamos qualquer tipo que implemente a interface.