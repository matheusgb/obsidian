Por padrão, o mecanismo de renderização pode exibir documentos e imagens HTML e XML. Ele pode exibir outros tipos de dados por meio de plug-ins ou extensões; por exemplo, exibindo documentos PDF usando um plug-in de visualizador de PDF. Porém, vamos nos concentrar no caso de uso principal: exibir HTML e imagens formatadas com CSS.

Navegadores diferentes usam mecanismos de renderização diferentes: o Internet Explorer usa o Trident, o Firefox usa o Gecko, o Safari usa o WebKit. O Chrome e o Opera (a partir da versão 15) usam o Blink, uma ramificação do WebKit.

O WebKit é um mecanismo de renderização de código aberto que começou como um mecanismo para a plataforma Linux e foi modificado pela Apple para ser compatível com Mac e Windows.[webkit.org](http://webkit.org/) 

O mecanismo de renderização começa a obter o conteúdo do documento solicitado a partir da camada de rede. Isso geralmente será feito em partes de 8 kB.

Depois disso, este é o fluxo básico do mecanismo de renderização:
![[Pasted image 20231228200500.png]]

O mecanismo de renderização inicia a partir do documento HTML e converte elementos em nós DOM em uma árvore chamada "árvore de conteúdo". O mecanismo analisará os dados de estilo em arquivos CSS externos e em elementos de estilo. As informações de estilo junto com as instruções visuais no HTML são usadas para criar outra árvore: a árvore de renderização.

A árvore de renderização contém retângulos com atributos visuais, como cor e dimensões. Os retângulos estão na ordem correta para serem exibidos na tela.

Após a construção da árvore de renderização, ela passa por um processo de layout. Isso significa fornecer a cada nó as coordenadas exatas de onde ele deve aparecer na tela. A próxima etapa é a pintura. A árvore de renderização será percorrida e cada nó será pintado usando a camada de back-end da IU.

É importante entender que esse é um processo gradual. Para uma melhor experiência do usuário, o mecanismo de renderização tenta exibir o conteúdo na tela o mais rápido possível. Ele não espera até que todo o HTML seja analisado para começar a criar e definir o layout da árvore de renderização. Partes do conteúdo serão analisadas e exibidas, enquanto o processo continua com o restante do conteúdo que vem da rede.

![[Pasted image 20231228201158.png]]
fluxo webkit 👆

![[Pasted image 20231228201216.png]]
fluxo gecko 👆

Nas figuras, é possível notar que, embora WebKit e Gecko usem terminologias diferentes, o fluxo é basicamente o mesmo.

O Gecko chama a árvore de elementos formatados visualmente de "árvore de frames". Cada elemento é um frame. O WebKit usa o termo "Árvore de renderização" e consiste em "Objetos de renderização". O WebKit usa o termo "layout" para a colocação dos elementos, enquanto o Gecko o chama de "Reflow". "Anexo" é o termo do WebKit para conectar nós DOM e informações visuais para criar a árvore de renderização. Uma pequena diferença não semântica é que o Gecko tem uma camada extra entre o HTML e a árvore DOM. Ela é chamada de "coletor de conteúdo" e é uma fábrica para a criação de elementos DOM.