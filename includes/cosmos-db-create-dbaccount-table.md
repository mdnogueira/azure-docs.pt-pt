1. Numa nova janela do browser, inicie sessão para o [portal do Azure](https://portal.azure.com/).
2. No menu esquerdo, clique em **Novo**, clique em **Bases de Dados**e, em **Azure Cosmos DB**, clique em **Criar**. 
   
   ![Captura de ecrã do portal do Azure, com destaque para Mais Serviços e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. No **nova conta** página, introduza as definições para a nova conta de base de dados do Azure Cosmos. 
 
    Definição|Valor sugerido|Descrição
    ---|---|---
    ID|*Introduza um nome exclusivo*|Introduza um nome exclusivo para identificar esta conta de base de dados do Azure Cosmos. Uma vez que *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, utilize um ID exclusivo, mas identificável.<br><br>O ID pode conter apenas minúsculas, números, o caráter hífen (-) e tem de ter entre 3 e 50 carateres.
    API|Tabela do Azure|A API determina o tipo de conta para criar. BD do Azure do Cosmos fornece cinco APIs para se adequa às necessidades da sua aplicação: SQL Server (base de dados do documento), Gremlin (base de dados do gráfico), MongoDB (base de dados do documento), tabelas do Azure e Cassandra, cada qual atualmente necessitam de uma conta separada.<br><br>Selecione **Azure Table** porque neste guia de introdução que está a criar uma tabela que funciona com a API de tabela.<br><br>[Saiba mais sobre a API de tabela](../articles/cosmos-db/table-introduction.md) |
    Subscrição|*Introduzir o mesmo nome exclusivo, conforme indicado acima ID*|Selecione a subscrição do Azure que pretende utilizar para esta conta de base de dados do Azure Cosmos. 
    Grupo de Recursos|*O mesmo valor que o ID*|Introduza um novo nome de grupo de recursos para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID. 
    Localização|*Selecione a região mais próxima aos seus utilizadores*|Selecione uma localização geográfica na qual vai alojar a sua conta de base de dados do Azure Cosmos. Utilize a localização que esteja mais próxima para os seus utilizadores para conceder-lhes o acesso mais rápido aos dados.
    Ativar a redundância geográfica| Deixar em branco | Esta ação cria uma versão replicada da base de dados numa região segundo (emparelhada). Deixe em branco.  
    Afixar ao dashboard | Selecione | Selecione esta caixa para que a sua nova conta de base de dados é adicionada ao seu dashboard do portal para facilitar o acesso.

    Em seguida, clique em **Criar**.  

    ![Captura de ecrã do painel Novo Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-2.png)

4. A criação de conta demora alguns minutos. Durante a conta criação o portal apresenta o **implementar BD do Azure Cosmos** mosaico.

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount-table/deploying-cosmos-db.png)

    Assim que a conta for criada, o **Parabéns! Foi criada a sua conta de base de dados do Azure Cosmos** é apresentada a página.
