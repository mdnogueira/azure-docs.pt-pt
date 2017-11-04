Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados e uma coleção. 

1. Clique em **Explorador de dados** > **nova coleção**. 
    
    O **adicionar coleção** área é apresentada na extremidade direita, poderá ter de se deslocar para a direita para vê-lo.

    ![O portal do Azure Explorador de dados, o painel Adicionar coleção](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. No **adicionar coleção** página, introduza as definições para a nova coleção.

    Definição|Valor sugerido|Descrição
    ---|---|---
    Id da base de dados|Tarefas|Introduza *tarefas* como o nome para a nova base de dados. Os nomes das bases de dados devem conter de 1 a 255 carateres e não podem conter /, \\, #, ?, ou um espaço à direita.
    ID da coleção|Itens|Introduza *itens* como o nome para a nova coleção. Ids de colecção têm os mesmos requisitos de caráter como nomes de base de dados.
    Capacidade de armazenamento| Fixa (10 GB)|Altere o valor para **fixo (10 GB)**. Este valor é a capacidade de armazenamento da base de dados.
    Débito|400 RU|Altere o débito para 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.
    Chave de partição|/categoria|Uma chave de partição que distribui uniformemente os dados para cada partição. É importante selecionar a chave de partição correta para criar coleções com bom desempenho. Para obter mais informações, consulte [Designing for partitioning](../articles/cosmos-db/partition-data.md#designing-for-partitioning) (Criação de partições).

    Clique em **OK**.

    Explorador de dados apresenta a nova base de dados e a coleção.

    ![O Explorador de dados, que mostra a nova base de dados e a coleção de portal do Azure](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)