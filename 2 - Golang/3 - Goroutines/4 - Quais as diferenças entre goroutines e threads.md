Uma thread, nada mais é do que um espaço que o sistema operacional aloca na memória. Normalmente, uma thread ocupa um espaço de 2mb. ^bda1bb

Quando uma [[1 - O que são e como funcionam as Goroutines#^165c0f|goroutine]] é criada, ela normalmente ocupa 2kb de espaço em memória, o que dá aproximadamente 1% do tamanho de uma thread. Ou seja, para uma goroutine simples, ter uma thread inteira só para ela acaba sendo um grande desperdício de memória.

Embora modificar o tamanho das threads do sistema operacional pudesse resolver o problema de desperdício de memória em goroutines simples, isso acabaria gerando um grande problema para goroutines mais complexas, onde podemos ver seu consumo chegar na casa de gigas.
## Scheduler em threads vs goroutines

Outro ponto que vale ser falado sobre threads, é que elas são gerenciadas pelo kernel do sistema operacional. Dentro do kernel, existe um programa que é executado várias vezes por segundo, chamado de scheduler. Esse programa é responsável por:

- Salvar os registradores (estado) de uma thread em memória;
- Recuperar os registradores da próxima thread que será executada;
- Enviar essa nova thread para execução.

O “problema” é que esse fluxo de trabalho é custoso, o que acaba tornando-o um pouco lento.

Por incrível que pareça, o runtime do Go também tem um processo chamado de scheduler, e que funciona de forma muito similar ao scheduler do sistema operacional. Digo similar por que o scheduler do Go só precisa se preocupar com as goroutines de um programa, enquanto o scheduler do sistema operacional precisa gerenciar as threads de todos os programas que estão sendo executados pelo computador.

Outra diferença é que o scheduler do Go utiliza uma técnica chamada MN Scheduling.

Essa técnica basicamente diz que o scheduler do Go precisa se preocupar com **M** goroutines em **N** threads, ou seja, o scheduler do Go consegue colocar mais de uma goroutine por thread. Isso traz muita performance para o Go, já que ele consegue trocar a execução de uma goroutine para outra por um “custo” muito menor, pois tudo o que as goroutines precisam para serem executadas está na mesma thread do sistema operacional.
## Identidade em threads vs goroutines

Normalmente threads tem uma identidade, que pode ser um inteiro ou um ponteiro. Essa identidade traz para o desenvolvedor a capacidade de utilizar uma feature chamada de thread local storage, que basicamente é a capacidade de poder salvar dados dentro da thread.

Muitas vezes acabam abusando um pouco dessa feature, como por exemplo, ao receber uma request, alguma thread é invocada, e os dados salvos nela são utilizados para atender a request. Como não temos controle sobre a ordem de execução das threads, durante alguma troca de contexto, os valores podem ser alterados antes do que havíamos previsto, o que pode gerar comportamentos inesperados no programa.

Para evitar esse problema, as goroutines foram desenhadas para não ter nenhum tipo de identidade. Dessa forma, qualquer tipo de troca de valores entre as goroutines devem ser feitas por parâmetro (ao iniciar) ou por [[1 - O que são e como utilizar channels|channels]]. Em ambas as formas, o dado que será recebido para a execução de uma goroutine fica muito mais explicito do que nas threads.