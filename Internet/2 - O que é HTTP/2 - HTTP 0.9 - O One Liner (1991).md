A primeira versão documentada do [[1 - Se aprofundando em HTTP#^64574c|HTTP]] foi o HTTP/0.9 , apresentado em 1991. Tendo um único método chamado GET. 

Se um cliente tivesse que acessar alguma página da web no servidor, ele teria feito uma solicitação simples como abaixo:

```
GET /index.html
```

E a resposta do servidor seria a seguinte:

```
(response body)
(connection closed)
```

O servidor receberia a solicitação, responderia com o HTML e assim que o conteúdo fosse transferido a conexão seria encerrada.

- Sem cabeçalhos
- `GET`era o único método permitido
- A resposta tinha que ser HTML