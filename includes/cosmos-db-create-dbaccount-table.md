1. Numa nova janela, inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu esquerdo, clique em **Novo**, clique em **Bases de Dados**e, em **Azure Cosmos DB**, clique em **Criar**.
   
   ![Captura de ecrã do portal do Azure, com destaque para Mais Serviços e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. No painel **Nova conta**, especifique a configuração pretendida para a conta do Azure Cosmos DB. 

    Com o Azure Cosmos DB, pode escolher um de quatro modelos de programação: Gremlin (gráficos), MongoDB, SQL (DocumentDB) e Table (chave-valor). 
    
    Neste guia de introdução, vamos programar com a API Table, pelo que vai escolher **Table (chave-valor)** quando preencher o formulário. Contudo, se tiver dados de gráficos para aplicações de redes sociais, dados de documentos de aplicações de catálogos ou dados migrados de aplicações MongoDB, tenha em conta que o Azure Cosmos DB pode proporcionar uma plataforma de serviço de bases de dados de elevada disponibilidade e distribuída globalmente para todas as aplicações críticas para a sua atividade.

    Preencha o painel Nova conta com as informações que estão na captura de ecrã como guia. Quando configurar a sua conta, vai escolher valores exclusivos, pelo que esses valores não serão exatamente iguais aos da captura de ecrã. 
 
    ![Captura de ecrã do painel Novo Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-2.png)

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID|*Valor exclusivo*|Um nome exclusivo que vai escolher para identificar a conta do Azure Cosmos DB. *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, pelo que deve utilizar um ID exclusivo, mas identificável. O ID pode conter apenas minúsculas, números, o caráter “-” e tem de ter entre três e 50 carateres.
    API|Table (chave-valor)|Mais adiante neste artigo, vamos programar com a [API Table](../articles/cosmos-db/table-introduction.md).|
    Subscrição|*A sua subscrição*|A subscrição do Azure que quer utilizar para a conta do Azure Cosmos DB. 
    Grupo de Recursos|*O mesmo valor que o ID*|O nome do grupo de recursos novo para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID. 
    Localização|*A região mais próxima dos seus utilizadores*|A localização geográfica na qual vai alojar a sua conta do Azure Cosmos DB. Escolha a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.   

4. Clique em **Criar** para criar a conta.
5. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

    ![Notificação de implementação iniciada](./media/cosmos-db-create-dbaccount-table/notification.png)

6.  Quando a implementação estiver concluída, abra a conta nova no mosaico Todos os Recursos. 

    ![Conta de base de dados do Cosmos do Azure no mosaico todos os recursos](./media/cosmos-db-create-dbaccount-table/all-resources.png)
