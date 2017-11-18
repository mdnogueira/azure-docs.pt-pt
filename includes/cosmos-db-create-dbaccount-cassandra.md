1. Numa nova janela do browser, inicie sessão para o [portal do Azure](https://portal.azure.com/).
2. Clique em **novo** > **bases de dados** > **Azure Cosmos DB**.
   
   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)

3. No **nova conta** página, introduza as definições para a nova conta de base de dados do Azure Cosmos. 
 
    Definição|Valor sugerido|Descrição
    ---|---|---
    ID|*Introduza um nome exclusivo*|Introduza um nome exclusivo para identificar esta conta de base de dados do Azure Cosmos. Porque *documents.azure.com* é acrescentada ao ID de fornecer para criar o ponto de contacto, utilize um ID exclusivo mas identificável.<br><br>O ID pode conter apenas minúsculas, números, o caráter hífen (-) e tem de ter entre 3 e 50 carateres.
    API|Cassandra|A API determina o tipo de conta para criar. BD do Azure do Cosmos fornece cinco APIs para se adequa às necessidades da sua aplicação: SQL Server (base de dados do documento), Gremlin (base de dados do gráfico), MongoDB (base de dados do documento), tabelas do Azure e Cassandra, cada qual atualmente necessitam de uma conta separada. <br><br>Selecione **Cassandra** porque neste guia de introdução que está a criar uma base de dados de toda a coluna é consultável utilizando sintaxe CQL.<br><br>Se Cassandra (wide coluna) não é apresentada na lista, terá então de [aplicam-se a associação](../articles/cosmos-db/cassandra-introduction.md#sign-up-now) o programa de pré-visualização Cassandra API.<br><br> [Saiba mais sobre a API de Cassandra](../articles/cosmos-db/cassandra-introduction.md)|
    Subscrição|*A sua subscrição*|Selecione a subscrição do Azure que pretende utilizar para esta conta de base de dados do Azure Cosmos. 
    Grupo de Recursos|*Introduzir o mesmo nome exclusivo, conforme indicado acima ID*|Introduza um novo nome de grupo de recursos para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID. 
    Localização|*Selecione a região mais próxima aos seus utilizadores*|Selecione uma localização geográfica na qual vai alojar a sua conta de base de dados do Azure Cosmos. Utilize a localização que esteja mais próxima para os seus utilizadores para conceder-lhes o acesso mais rápido aos dados.
    Afixar ao dashboard | Selecione | Selecione esta caixa para que a sua nova conta de base de dados é adicionada ao seu dashboard do portal para facilitar o acesso.

    Em seguida, clique em **Criar**.

    ![O painel da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-2.png)

4. A criação de conta demora alguns minutos. Durante a criação de contas do dashboard do portal apresenta o **implementar BD do Azure Cosmos** mosaico.

    ![O mosaico de notificações do portal do Azure](./media/cosmos-db-create-dbaccount-cassandra/deploying-cosmos-db.png)

    Assim que a conta for criada, o **Parabéns! Foi criada a sua conta de base de dados do Azure Cosmos** é apresentada a página. 

