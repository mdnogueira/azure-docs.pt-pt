1. Numa nova janela, inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No painel esquerdo, clique em **Novo**, clique em **Bases de Dados**e, em **Azure Cosmos DB**, clique em **Criar**.
   
   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. No painel **Nova conta**, especifique a configuração que quer para a conta do Azure Cosmos DB. 

    Com o Azure Cosmos DB, pode escolher um de quatro modelos de programação: Gremlin (gráficos), MongoDB, SQL (DocumentDB) e Table (chave-valor), em que cada um requer uma conta separada.
    
    Neste guia de início rápido, iremos programar com o DocumentDB API, pelo que irá escolher **SQL (DocumentDB)** quando preencher o formulário. Se tiver dados de gráficos para aplicações de redes sociais, dados de chaves/valores (tabela) ou dados migrados de aplicações MongoDB, tenha em conta que o Azure Cosmos DB pode proporcionar uma plataforma de serviço de bases de dados de elevada disponibilidade e distribuída globalmente para todas as aplicações críticas para a sua atividade.

    No painel **Nova conta**, preencha os campos com as informações na seguinte captura de ecrã apenas como um guia. Os valores podem ser diferentes dos valores na captura de ecrã.
 
    ![O painel da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID|*Valor exclusivo*|Um nome exclusivo que identifica esta conta do Azure Cosmos DB. Uma vez que *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, utilize um ID exclusivo, mas identificável. O ID pode conter apenas minúsculas, números, o caráter hífen (-) e tem de ter entre 3 e 50 carateres.
    API|SQL (DocumentDB)|Mais adiante neste artigo, iremos programar com [DocumentDB API](../articles/documentdb/documentdb-introduction.md).|
    Subscrição|*A sua subscrição*|A subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|*O mesmo valor que o ID*|O novo nome do grupo de recursos para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID. 
    Localização|*A região mais próxima dos seus utilizadores*|A localização geográfica na qual vai alojar a sua conta do Azure Cosmos DB. Escolha a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.
4. Clique em **Criar** para criar a conta.
5. Na barra de ferramentas superior, clique no ícone **Notificações** ![Ícone de notificação](./media/cosmos-db-create-dbaccount/notification-icon.png) para monitorizar o processo de implementação.

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Quando a janela Notificações indica que a implementação foi concluída com êxito, feche a janela de notificação e abra a nova conta no mosaico **Todos os Recursos** no Dashboard. 

    ![A conta DocumentDB no mosaico Todos os Recursos](./media/cosmos-db-create-dbaccount/all-resources.png)
 
