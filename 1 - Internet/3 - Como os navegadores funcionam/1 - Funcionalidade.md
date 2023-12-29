A principal função de um navegador é apresentar o recurso da Web escolhido por você, solicitando-o ao servidor e exibindo-o na janela do navegador.

A forma como o navegador interpreta e exibe arquivos HTML é definida nas especificações de HTML e CSS. Essas especificações são mantidas pelo W3C (World Wide Web Consortium), que é a organização que estabelece padrões para a Web.

Os principais componentes do navegador são:

1. **A interface do usuário**
2. **O mecanismo de navegador**
3. [[2 - Renderização|Mecanismo de renderização]]: responsável por exibir o conteúdo solicitado. Por exemplo, se o conteúdo solicitado for HTML, o mecanismo de renderização analisará HTML e CSS e exibirá o conteúdo na tela.
4. **Rede**: solicitações [[1 - Se aprofundando em HTTP#^64574c|HTTP]], usando implementações diferentes para plataformas distintas por trás de uma interface independente de plataforma.
5. **Back-end da interface**: usado para desenhar widgets básicos, como caixas de combinação e janelas. Esse back-end expõe uma interface genérica que não é específica à plataforma. Abaixo dela, usa métodos da interface do usuário do sistema operacional.
6. **Intérprete JavaScript**. Usado para analisar e executar código JavaScript.
7. **Armazenamento de dados**. Essa é uma camada de persistência. O navegador pode precisar salvar todos os tipos de dados localmente, como cookies. Os navegadores também oferecem suporte a mecanismos de armazenamento como localStorage, IndexedDB, WebSQL e FileSystem.
![[Pasted image 20231228194917.png]]

