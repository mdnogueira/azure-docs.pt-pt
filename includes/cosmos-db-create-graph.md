Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados de gráfico. 

1. No portal do Azure, no menu de navegação à esquerda, clique em **Data Explorer (Pré-visualização)**. 
2. No painel **Data Explorer (Pré-visualização)**, clique em **Novo Gráfico** e preencha a página com as informações seguintes.

    ![Data Explorer no portal do Azure](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    Id da base de dados|base de dados de exemplo|O ID da base de dados nova. Os nomes das bases de dados têm de ter entre um e 255 carateres e não podem conter `/ \ # ?` nem espaços à direita.
    ID do gráfico|gráfico de exemplo|O ID do seu novo gráfico. Os nomes dos gráficos têm os mesmos requisitos de carateres que os IDs das bases de dados.
    Capacidade de Armazenamento| 10 GB|Deixe o valor predefinido. Esta é a capacidade de armazenamento da base de dados.
    Débito|400 RUs|Deixe o valor predefinido. Pode aumentar verticalmente o débito mais tarde, se quiser reduzir a latência.
    RU/m|Desativado|Deixe o valor predefinido. 
    Chave de partição|/userid|Uma chave de partição que irá distribuir uniformemente os dados para cada partição. É importante selecionar a chave de partição correta para criar gráficos com bom desempenho. Leia mais em [Designing for partitioning](../articles/cosmos-db/partition-data.md#designing-for-partitioning) (Criação de partições).

3. Assim que o formulário estiver preenchido, clique em **OK**.