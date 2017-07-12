1. Numa nova janela, inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, clique em **Novo**, clique em **Bases de dados** e clique em **Azure Cosmos DB**.
   
   ![Painel da Base de Dados do portal do Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. No painel **Nova conta**, especifique a configuração pretendida para a conta do Azure Cosmos DB. 

    Com o Azure Cosmos DB, pode escolher um de quatro modelos de programação: Gremlin (gráficos), MongoDB, SQL (DocumentDB) e Table (chave-valor).  
       
    Neste guia de início rápido, iremos programar com o Graph API, pelo que pode escolher o **Gremlin (gráfico)** quando preencher o formulário. Se tiver dados de documentos de aplicações de catálogos, dados de chaves/valores (tabela) ou dados migrados de aplicações MongoDB, tenha em conta que o Azure Cosmos DB pode proporcionar uma plataforma de serviço de bases de dados de elevada disponibilidade e distribuída globalmente para todas as aplicações críticas para a sua atividade.

    No painel **Nova conta**, preencha os campos com as informações na seguinte captura de ecrã apenas como um guia. Quando configurar a sua conta, confirme que escolhe valores exclusivos, pelo que esses valores não serão exatamente iguais aos da captura de ecrã. 
 
    ![O painel Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID|*Valor exclusivo*|Um nome exclusivo que irá escolher para identificar a conta do Azure Cosmos DB. Uma vez que *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, utilize um ID exclusivo, mas identificável. O ID pode conter apenas minúsculas, letras, números, o caráter hífen (-) e tem de ter entre 3 e 50 carateres.
    API|Gremlin (gráfico)|Mais adiante neste artigo, vamos programar com o [Graph API](../articles/cosmos-db/graph-introduction.md).|
    Subscrição|*A sua subscrição*|A subscrição do Azure que quer utilizar para a conta do Azure Cosmos DB. 
    Grupo de Recursos|*O mesmo valor que o ID*|O nome do grupo de recursos novo para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID. 
    Localização|*A região mais próxima dos seus utilizadores*|A localização geográfica na qual vai alojar a sua conta do Azure Cosmos DB. Escolha a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

4. Clique em **Criar** para criar a conta.
5. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

    ![Notificação de implementação iniciada](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Quando a implementação estiver concluída, abra a nova conta no mosaico **Todos os Recursos**. 

    ![Conta do DocumentDB no mosaico Todos os Recursos](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)