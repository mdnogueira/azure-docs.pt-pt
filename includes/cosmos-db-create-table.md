Agora, pode utilizar o Data Explorer para criar uma tabela e adicionar dados à sua base de dados. 

1. No portal do Azure, no menu de navegação, clique em **Data Explorer (Pré-visualização)**. 
2. No painel Data Explorer, clique em **Nova Tabela** e preencha a página com as informações seguintes.

    ![Data Explorer no portal do Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID da tabela|tabela de exemplo|O ID da sua nova tabela. Os nomes das tabelas têm os mesmos requisitos de carateres que os IDs das bases de dados. Os nomes das bases de dados têm de ter entre um e 255 carateres e não podem conter `/ \ # ?` nem espaços à direita.
    Capacidade de armazenamento| 10 GB|Deixe o valor predefinido. Esta é a capacidade de armazenamento da base de dados.
    Débito|400 RUs|Deixe o valor predefinido. Se pretender reduzir a latência, pode aumentar o [débito](../articles/cosmos-db/request-units.md) mais tarde.
    RU/m|Desativado|Utilize o valor predefinido.

3. Assim que o formulário estiver preenchido, clique em **OK**.