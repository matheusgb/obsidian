Após 3 anos de [[3 - HTTP 1.0 - 1996|HTTP/1.0]], a próxima versão, HTTP/1.1, foi lançada em 1999. Fez muitas melhorias em relação ao seu antecessor. As principais melhorias em relação ao HTTP/1.0 incluem:

- Novos métodos [[0 - Terminologia e conceitos básicos.#^59b812|HTTP]] foram adicionados: PUT, PATCH, OPTIONS, DELETE.
- Cabeçalho do host obrigatório.
- Conexões persistentes, permitindo múltiplas solicitações sequenciais. Para fechar as conexões, o cabeçalho `Connection: close` ser passado na solicitação. Os clientes geralmente enviam esse cabeçalho na última solicitação para fechar a conexão com segurança.
- Também introduziu o suporte ao pipelining, onde o cliente poderia enviar múltiplas solicitações sem esperar a resposta do servidor, e o servidor teria que enviar a resposta na mesma sequência em que as solicitações foram recebidas.

Deve-se notar que para se beneficiar de conexões persistentes ou pipelines, o cabeçalho `Content-Length` deve estar disponível na resposta, pois isso permitiria ao cliente saber quando a transmissão for concluída e ele poderá enviar a próxima solicitação (na forma sequencial normal de enviando solicitações) ou começar a aguardar a próxima resposta (quando o pipeline estiver habilitado).

Mas ainda havia um problema com essa abordagem. E se os dados forem dinâmicos e o servidor não conseguir encontrar o comprimento do conteúdo com antecedência? Para resolver isso, o HTTP/1.1 introduziu o chunked encoding. Nesses casos, o servidor pode omitir o `Content-Length` em favor do chunked encoding. Porém, se nenhum deles estiver disponível, a conexão deverá ser encerrada ao final da solicitação.

- Chunked encoding no caso de conteúdo dinâmico, quando o servidor não consegue realmente descobrir o comprimento do conteúdo quando a transmissão começa, ele pode começar a enviar o conteúdo em pedaços (pedaço por pedaço) e adicionar o comprimento do conteúdo para cada pedaço quando ele for enviado. E quando todos os pedaços são enviados, ele envia um pedaço vazio, aquele com `Content-Length` definido como zero para identificar ao cliente que a transmissão foi concluída. Para notificar ao cliente sobre a transferência em partes, o servidor inclui o cabeçalho `Transfer-Encoding: chunked`
- Ao contrário do [[3 - HTTP 1.0 - 1996|HTTP/1.0]] que tinha apenas autenticação básica, o HTTP/1.1 incluía autenticação digest e proxy
- Cache
-  Byte Ranges   
- Character sets
- Language negotiation
- Cookies de cliente
- Suporte de compressão aprimorado
- Novos status codes
- ... e mais

Embora tenha melhorado muito em relação ao seu antecessor; com a web mudando todos os dias, ela começou a mostrar sua idade:
* Carregar uma página da web hoje em dia consome mais recursos do que nunca. Uma simples página hoje em dia precisa abrir mais de 30 conexões. 
* No HTTP/1.1 ele só pode ter uma conexão pendente. Foi tentado corrigir isso introduzindo a pipeline, mas não resolveu completamente o problema por causa do bloqueio inicial, onde uma solicitação lenta ou pesada pode bloquear as solicitações anteriores e, uma vez que uma solicitação fica presa em um pipeline, ele terá que aguardar o atendimento das próximas solicitações. 
* Para superar essas deficiências, os desenvolvedores começaram a implementar soluções alternativas: uso de spritesheets, imagens codificadas em CSS, arquivos CSS/Javascript gigantescos, fragmentação de domínio, etc.

