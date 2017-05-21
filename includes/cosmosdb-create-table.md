Agora, pode utilizar o Data Explorer para criar um contentor de gráficos e adicionar dados à sua base de dados. 

1. No portal do Azure, no menu de navegação, em **Tabelas**, clique em **Data Explorer**. 
2. No painel Data Explorer, clique em **Coleção Nova** e preencha a página com as informações seguintes.

    ![Data Explorer no portal do Azure](./media/cosmosdb-create-graph/azure-cosmosdb-data-explorer.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    Id da base de dados|base de dados de exemplo|O ID da base de dados nova. Os nomes das bases de dados têm de ter entre um e 255 carateres e não podem conter `/ \ # ?` nem espaços à direita.
    ID da coleção|tabela de exemplo|O ID da coleção nova. Os nomes de coleções têm os mesmos requisitos que os ids das bases de dados.
    Capacidade de Armazenamento| 10 GB|Deixe o valor predefinido. Esta é a capacidade de armazenamento da base de dados.
    Débito|400 RUs|Deixe o valor predefinido. Pode aumentar verticalmente o débito mais tarde, se quiser reduzir a latência.
    Chave de partição|/databases|Uma chave de partição que irá distribuir uniformemente os dados para cada partição. É importante selecionar a chave de partição correta para criar coleções com bom desempenho. Leia mais em [Designing for partitioning](../articles/cosmos-db/partition-data.md#designing-for-partitioning) (Conceber a criação de partições).

3. Assim que o formulário estiver preenchido, clique em **OK**.