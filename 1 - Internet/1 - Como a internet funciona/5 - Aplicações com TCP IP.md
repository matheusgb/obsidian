Aplicações com [[1 - Rede, internet e visão geral.#^6106c9|TCP/IP]] utilizam alguns conceitos:

* Portas: cada aplicativo recebe um número de porta exclusivo, isso serve para identificar o aplicativo ou serviço em execução no dispositivo. Com isso, os dados são enviados para o destino certo.

* Soquetes: é a combinação de um [[0 - Terminologia e conceitos básicos.#^034454|endereço de IP]] e um número de porta, representa um ponto final de comunicação. São usados para estabelecer conexão entre dispositivos e transferir dados a aplicativos.

* Conexões: uma conexão é estabelecida entre dois soquetes quando dois dispositivos desejam se comunicar entre si.

* Transferência de dados: quando estabelecido a conexão, os dados podem ser transferidos entre os aplicativos executados em cada dispositivo. Os dados são normalmente transmitidos em segmentos, contendo número de sequencia e outros metadados que garantem uma entrega confiável.

