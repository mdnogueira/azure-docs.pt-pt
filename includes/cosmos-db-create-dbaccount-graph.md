1. Numa nova janela do browser, inicie sessão para o [portal do Azure](https://portal.azure.com/).

2. Clique em **novo** > **bases de dados** > **Azure Cosmos DB**.
   
   ![Painel de "Bases de dados" portal do Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. No **nova conta** página, introduza as definições para a nova conta de base de dados do Azure Cosmos. 

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID|*Introduza um nome exclusivo*|Introduza um nome exclusivo para identificar esta conta de base de dados do Azure Cosmos. Uma vez que *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, utilize um ID exclusivo, mas identificável.<br><br>O ID pode conter apenas minúsculas, números, o caráter hífen (-) e tem de ter entre 3 e 50 carateres.
    API|Gremlin (gráfico)|A API determina o tipo de conta para criar. BD do Azure do Cosmos fornece cinco APIs para se adequa às necessidades da sua aplicação: SQL Server (base de dados do documento), Gremlin (base de dados do gráfico), MongoDB (base de dados do documento), tabelas do Azure e Cassandra, cada qual atualmente necessitam de uma conta separada. <br><br>Selecione **Gremlin (gráfico)** porque neste guia de introdução que está a criar um gráfico que é consultável utilizando sintaxe Gremlin.<br><br>[Saiba mais sobre a Graph API](../articles/cosmos-db/graph-introduction.md)
    Subscrição|*A sua subscrição*|Selecione a subscrição do Azure que pretende utilizar para esta conta de base de dados do Azure Cosmos. 
    Grupo de recursos|*Introduzir o mesmo nome exclusivo, conforme indicado acima ID*|Introduza um novo nome de grupo de recursos para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID. 
    Localização|*Selecione a região mais próxima aos seus utilizadores*|Selecione uma localização geográfica na qual vai alojar a sua conta de base de dados do Azure Cosmos. Utilize a localização que esteja mais próxima para os seus utilizadores para conceder-lhes o acesso mais rápido aos dados.
    Ativar redundância geográfica| Deixar em branco | Esta ação cria uma versão replicada da base de dados numa região segundo (emparelhada). Deixe em branco.  
    Afixar ao dashboard | Selecione | Selecione esta caixa para que a sua nova conta de base de dados é adicionada ao seu dashboard do portal para facilitar o acesso.

    Em seguida, clique em **Criar**.

    ![O painel da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

4. A criação de conta demora alguns minutos. Durante a conta criação o portal apresenta o **implementar BD do Azure Cosmos** mosaico no lado direito, poderá ter de se deslocar para a direita no seu dashboard para ver o mosaico. Também é uma barra de progresso apresentada perto da parte superior do ecrã. Pode ver a área de progresso.

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount-graph/deploying-cosmos-db.png)

    Assim que a conta for criada, o **Parabéns! Foi criada a sua conta de base de dados do Azure Cosmos** é apresentada a página. 