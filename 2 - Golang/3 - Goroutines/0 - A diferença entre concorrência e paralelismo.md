_Concorrência_ não é _paralelismo_. _Paralelismo_ é quando duas ou mais [[4 - Quais as diferenças entre goroutines e threads#^bda1bb|threads]] executam o código simultaneamente através de diferentes cores do processador. ^cd219e

Concorrência executa o código _concorrentemente_ entre _threads_ em um único core do processador.

Imagine que você precisa lavar uma pilha de pratos e depois seca-los, você tem apenas uma pia para fazer esse trabalho, o que você provavelmente faria seria lavar os pratos e depois secar cada um deles, que seria uma analogia para _**Single Threaded**_.

Agora imagina que invés lavar e depois secar, você pede ajuda para um amigo ir secando os pratos enquanto você lava, isso seria a _**Concorrência**_, enquanto você está ocupado lavando os pratos, um outro agente está secando.

Agora em outro cenário, imagine que você tem duas pias, e você novamente pede ajuda a um amigo, vocês conseguem lavar e secar _**Paralelamente**_, sem interferir no trabalho do outro. Se cada um de vocês, pedir ajuda de outras pessoas, para secarem enquanto vocês lavam, em pias diferentes, vocês estão usando _**Paralelismo**_ e _**Concorrência**_!

Em um programa que utiliza concorrência, quando estivermos efetuando o download de um arquivo, o programa vai ficar alterando a execução desses dois componentes, para que consiga baixar um pedaço do arquivo e na sequencia renderizar essa informação no navegador.

![[Pasted image 20240102164422.png]]

Agora imagine o mesmo navegador funcionando em um computador multi core, onde ele consegue fazer download e renderizar a informação ao mesmo tempo. Nesse caso o programa estaria utilizando paralelismo.

![[Pasted image 20240102164513.png]]


