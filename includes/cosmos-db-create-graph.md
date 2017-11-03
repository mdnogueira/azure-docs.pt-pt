Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados de gráfico. 

1. No portal do Azure, no menu à esquerda, selecione **Explorador de dados (pré-visualização)**.

2. Em **Explorador de dados (pré-visualização)**, selecione **gráfico novo**. Em seguida, preencha a página utilizando as seguintes informações:

    ![Data Explorer no portal do Azure](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    Id da base de dados|base de dados de exemplo|O ID da base de dados nova. Os nomes de base de dados tem de estar entre 1 e 255 carateres e não pode conter `/ \ # ?` ou um espaço à direita.
    ID do gráfico|gráfico de exemplo|O ID do seu novo gráfico. Os nomes de gráfico têm os mesmos requisitos de caráter como IDs da base de dados.
    Capacidade de armazenamento| 10 GB|Deixe o valor predefinido. Esta é a capacidade de armazenamento da base de dados.
    Débito|400 RUs|Deixe o valor predefinido. Pode aumentar verticalmente o débito mais tarde, se quiser reduzir a latência.
    Chave de partição|/firstName|Uma chave de partição que distribui uniformemente os dados para cada partição. Selecionar a chave de partição correto é importante na criação de um gráfico de performant. Para obter mais informações, consulte [estruturar para criação de partições](../articles/cosmos-db/partition-data.md#designing-for-partitioning).

3. Depois do formulário é preenchido, selecione **OK**.
