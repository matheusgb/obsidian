Ao contrário do [[2 - HTTP 0.9 - O One Liner (1991)|HTTP/0.9]], que foi projetado apenas para resposta HTML, o HTTP/1.0 pode lidar com outros formatos de resposta: imagens, arquivos de vídeo, texto simples ou qualquer outro tipo de conteúdo.

Ele adicionou mais métodos (POST e HEAD), formatos de solicitação/resposta foram alterados, cabeçalhos HTTP foram adicionados à solicitação e às respostas, códigos de status foram adicionados para identificar a resposta, suporte a conjunto de caracteres foi introduzido, tipos multi-part, autorização , cache, codificação de conteúdo e muito mais foram incluídos.

Esta é a aparência de um exemplo de solicitação e resposta HTTP/1.0:

```
GET / HTTP/1.0
Host: cs.fyi
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5)
Accept: */*
```

Junto com a solicitação o cliente também enviou suas informações pessoais, tipo de resposta necessária e etc. No HTTP/0.9 o cliente nunca poderia enviar essas informações porque não havia cabeçalhos.

Um exemplo de resposta à solicitação acima pode ter a aparência abaixo:

```
HTTP/1.0 200 OK 
Content-Type: text/plain
Content-Length: 137582
Expires: Thu, 05 Dec 1997 16:00:00 GMT
Last-Modified: Wed, 5 August 1996 15:55:28 GMT
Server: Apache 0.84

(response body)
(connection closed)
```

Bem no início da resposta há HTTP/1.0 (HTTP seguido pelo número da versão), depois há o código de status 200 seguido pela frase de motivo (ou descrição do código de status, se preferir).

Os cabeçalhos de solicitação e resposta ainda eram mantidos codificados em ASCII, mas o corpo da resposta poderia ser de qualquer tipo.

Uma das principais desvantagens do HTTP/1.0 era que não podia ter várias solicitações por conexão. Sempre que um cliente precisar de algo do servidor, ele terá que abrir uma nova conexão TCP e após essa única solicitação ser atendida, a conexão será encerrada. Vamos supor que você visite uma página web com 10 imagens, 5 folhas de estilo e 5 arquivos javascript, totalizando 20 itens que precisam ser buscados quando a solicitação para aquela página for feita. Como o servidor fecha a conexão assim que a solicitação for atendida, haverá uma série de 20 conexões separadas onde cada um dos itens será atendido um por um. Esse grande número de conexões resulta em um sério impacto no desempenho, pois a necessidade de uma nova conexão TCP impõe uma penalidade significativa no desempenho devido ao [[4 - Three-way Handshake|three-way handshake]] seguido de inicialização lenta.