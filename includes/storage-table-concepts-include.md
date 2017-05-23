## <a name="what-is-the-table-service"></a>O que é o Serviço Tabela?
O serviço de armazenamento de Tabelas do Azure armazena grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Utilizações comuns do serviço Tabela:

* Armazenamento de TBs de dados estruturados com capacidade para servirem aplicações de dimensionamento da Web
* Armazenamento de conjuntos de dados que não precisam de associações complexas, chaves externas ou procedimentos armazenados e podem ser desnormalizadas para acesso rápido
* Consulta rápida de dados com um índice em cluster
* Acesso a dados com o protocolo OData e consultas LINQ com Bibliotecas de .NET do Serviço de Dados WCF

Pode utilizar o serviço Tabela para armazenar e consultar conjuntos enormes de dados estruturados não relacionais. As suas tabelas serão dimensionadas à medida que aumenta a procura.

## <a name="table-service-concepts"></a>Conceitos do Serviço Tabela
O serviço Tabela contém os seguintes componentes:

![Diagrama de componente do serviço Tabela][Table1]

* **Formato do URL:** tabelas de códigos de endereços numa conta com este formato de endereço:   
  http://`<storage account>`.table.core.windows.net/`<table>`  
  
  Pode também endereçar diretamente as tabelas do Azure utilizando este endereço com o protocolo OData. Para obter mais informações, veja [OData.org][OData.org]
* **Conta de Armazenamento:** todos os acessos ao Storage do Azure são efetuados através de uma conta de armazenamento. Veja [Metas de Desempenho e Escalabilidade do Storage do Azure](../articles/storage/storage-scalability-targets.md) para obter detalhes acerca da capacidade das contas de armazenamento.
* **Tabela**: uma tabela é uma coleção de entidades. As tabelas não impõem um esquema a entidades, o que significa que uma única tabela pode conter entidades que tenham conjuntos diferentes de propriedades. O número de tabelas que uma conta de armazenamento pode conter só está limitado pelo limite da capacidade da conta de armazenamento.
* **Entidade**: uma entidade é um conjunto de propriedades, semelhantes a uma linha de base de dados. Uma entidade pode ter até 1 MB de tamanho.
* **Propriedades**: uma propriedade é um par nome/valor. Cada entidade pode incluir até 252 propriedades para armazenar dados. Cada entidade tem também três propriedades do sistema que especificam uma chave de partição, uma chave de linha e um carimbo de data/hora. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente e inseridas/atualizadas em operações atómicas. A chave de linha de uma entidade é o seu identificador exclusivo dentro de uma partição.

Para obter detalhes sobre a nomenclatura das tabelas e das propriedades, veja [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
