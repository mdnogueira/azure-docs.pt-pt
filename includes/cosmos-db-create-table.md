Agora, pode utilizar a ferramenta Explorador de dados no portal do Azure para criar uma base de dados e a tabela. 

1. Clique em **Explorador de dados** > **nova tabela**. 
    
    O **Adicionar tabela** área é apresentada na extremidade direita, poderá ter de se deslocar para a direita para vê-lo.

    ![Data Explorer no portal do Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. No **Adicionar tabela** página, introduza as definições para a tabela de novo.

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID da tabela|tabela de exemplo|O ID da sua nova tabela. Os nomes das tabelas têm os mesmos requisitos de carateres que os IDs das bases de dados. Os nomes das bases de dados têm de ter entre um e 255 carateres e não podem conter `/ \ # ?` nem espaços à direita.
    Capacidade de armazenamento| Fixa (10 GB)|Altere o valor para **fixo (10 GB)**. Este valor é a capacidade de armazenamento da base de dados.
    Débito|400 RUs|Altere o débito para 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.

    Clique em **OK**.

    Explorador de dados apresenta a nova base de dados e a tabela.

    ![O Explorador de dados, que mostra a nova base de dados e a coleção de portal do Azure](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)