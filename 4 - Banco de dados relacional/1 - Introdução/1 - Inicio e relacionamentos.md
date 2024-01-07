Um banco de dados relacional é um conjunto de informações organizas em relações predefinidas, em que os dados são armazenados em uma ou mais tabelas (ou "relações") de colunas e linhas, facilitando a visualização e a compreensão de como as diferentes estruturas de dados se relacionam.

Os relacionamentos são uma conexão lógica entre diferentes tabelas, estabelecidas com base na interação entre essas tabelas.

## Relacionamentos

Os relacionamentos de banco de dados são associações entre tabelas que são criadas usando instruções de junção para recuperar dados.

#### Um para um:
Este tipo de relacionamento se dá, de forma direta entre duas tabelas, quando a chave primária do registro de uma determinada tabela pode ser utilizada uma única vez em um dos registros da outra tabela. No exemplo que segue, tem-se duas tabelas, uma para cadastro de **funcionários** e outra para cadastro de **cônjuges** (esposa ou marido), sendo este, um típico exemplo de relacionamento um para um, pois neste caso, o código de cada cônjuge poderá ser especificado uma única vez na tabela de funcionários, visto que para cada funcionário existirá apenas um cônjuge.

![[Pasted image 20240107142638.png]]

#### Um para vários:

Tipo de relacionamento que também acontece de forma direta entre duas tabelas sempre que a chave primária do registro de uma determinada tabela é utilizada várias vezes em outra tabela, sendo este, o tipo de relacionamento mais comum entre tabelas de um banco de dados relacional. O próximo exemplo mostra a relação entre uma tabela para cadastro de **produtos** e uma tabela para cadastro de **fornecedores**, onde um mesmo fornecedor vende vários produtos, podendo o seu código ser informado várias vezes em diferentes registros da tabela de **produtos**.

![[Pasted image 20240107142840.png]]

####  Vários para vários:

É um tipo de relacionamento que acontece de forma indireta entre duas tabelas, pois para que ele possa ser concebido é necessário a geração de uma terceira tabela. Na prática o relacionamento vários para vários não existe de fato, o que existe é dois ou mais relacionamentos um para vários, que ganha o sentido de vários para vários. Ocorre sempre que surge a necessidade de se relacionar duas chaves primárias de registros de diferentes tabelas em vários registros de uma terceira tabela. O exemplo mostrado a seguir considera um sistema em que o cliente de uma empresa possa fazer reserva de produtos para serem comprados. Neste caso, tem-se uma tabela para cadastro de produtos, uma tabela para cadastro de clientes e uma tabela para registro de reservas. Observe que na tabela para registro de reservas, um mesmo cliente pode fazer reserva de vários produtos e um mesmo produto pode ser reservado por vários clientes. Com isso, surgem duas relações um para vários, que ganha o sentido de vários para vários.

![[Pasted image 20240107143211.png]]