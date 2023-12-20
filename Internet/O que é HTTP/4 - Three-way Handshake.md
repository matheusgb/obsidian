Todas as conexões [[5 - Aplicações com TCP IP|TCP]] começam com um three-way handshake no qual o cliente e o servidor compartilham uma série de pacotes antes de começar a compartilhar os dados do aplicativo.

- SYN - O cliente pega um número aleatório, digamos x, e o envia para o servidor.
- SYN ACK - O servidor reconhece a solicitação enviando um pacote ACK de volta ao cliente que é composto por um número aleatório, digamos y obtido pelo servidor e o número x+1 onde x é o número que foi enviado pelo cliente.
- ACK - O cliente incrementa o número y recebido do servidor e envia de volta um pacote ACK com o número y+1.

Quando concluído, o compartilhamento de dados entre o cliente e o servidor pode começar. Deve-se observar que o cliente pode começar a enviar os dados da aplicação assim que despachar o último pacote ACK, mas o servidor ainda terá que aguardar o recebimento do pacote ACK para atender à solicitação.

![[Pasted image 20231219222421.png]]

Algumas implementações de [[3 - HTTP 1.0 - 1996|HTTP/1.0]] tentaram superar esse problema introduzindo um novo cabeçalho chamado Connection: keep-alive que pretendia dizer ao servidor “Ei servidor, não feche esta conexão, preciso dela novamente”. Mesmo assim, não foi tão amplamente apoiado e o problema ainda persistia.

Além de não ter conexão, o HTTP também é um protocolo sem estado, ou seja, o servidor não mantém as informações sobre o cliente e, portanto, cada uma das solicitações deve ter as informações necessárias para que o servidor atenda a solicitação por conta própria, sem qualquer associação com qualquer antigo. Além do grande número de conexões que o cliente tinha que abrir, ele também tem que enviar alguns dados redundantes no fio, causando aumento no uso da largura de banda.