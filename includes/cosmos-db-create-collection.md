Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados e uma coleção. 

1. No portal do Azure, no menu de navegação à esquerda, clique em **Data Explorer (Pré-visualização)**. 

2. No painel **Data Explorer (Pré-visualização)**, clique em **Nova Coleção** e forneça as informações seguintes:

    ![O painel Data Explorer no portal do Azure](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    Id da base de dados|Tarefas|O nome da base de dados nova. Os nomes das bases de dados devem conter de 1 a 255 carateres e não podem conter /, \\, #, ?, ou um espaço à direita.
    ID da coleção|Itens|O nome da coleção nova. Os nomes das coleções têm os mesmos requisitos em termos de carateres do que os ID das bases de dados.
    Capacidade de armazenamento| Fixa (10 GB)|Utilize o valor predefinido. Este valor é a capacidade de armazenamento da base de dados.
    Débito|400 RU|Utilize o valor predefinido. Se pretender reduzir a latência, pode aumentar o débito mais tarde.
    Chave de partição|/categoria|Uma chave de partição que distribui uniformemente os dados para cada partição. É importante selecionar a chave de partição correta para criar coleções com bom desempenho. Para obter mais informações, consulte [Designing for partitioning](../articles/cosmos-db/partition-data.md#designing-for-partitioning) (Criação de partições).    
3. Quando concluir o formulário, clique em **OK**.

O Data Explorer mostra a base de dados e a coleção novas. 