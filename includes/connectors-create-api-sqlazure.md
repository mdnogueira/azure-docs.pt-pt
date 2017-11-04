### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [SQL Database do Azure](../articles/sql-database/sql-database-get-started.md) com as informações da ligação, incluindo o nome do servidor, o nome de base de dados e o nome de utilizador/palavra-passe. Estas informações estão incluídas na cadeia de ligação de base de dados SQL:
  
    Servidor = tcp:*yoursqlservername*. database.windows.net,1433;Initial catálogo =*yourqldbname*; Manter informações de segurança = False; ID de utilizador = {your_username}; Palavra-passe = {your_password}; MultipleActiveResultSets = False; Encriptar = True; TrustServerCertificate = False; Tempo limite da ligação = 30;
  
    Leia mais sobre [bases de dados do Azure SQL](https://azure.microsoft.com/services/sql-database).

> [!NOTE]
> Quando cria uma base de dados do SQL do Azure, também pode criar as bases de dados de exemplo incluídos com o SQL Server. 
> 
> 

Antes de utilizar a SQL Database do Azure numa aplicação lógica, ligar à base de dados SQL. Pode fazê-facilmente na sua aplicação lógica no portal do Azure.  

Ligar à SQL Database do Azure utilizando os seguintes passos:  

1. Crie uma aplicação lógica. No designer Logic Apps, adicione um acionador e, em seguida, adicionar uma ação. Selecione **Mostrar Microsoft APIs geridas** na lista pendente lista e, em seguida, introduza "sql" na caixa de pesquisa. Selecione uma das ações:  
   
    ![Passo de criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)
2. Se ainda não criou anteriormente todas as ligações à base de dados do SQL Server, é-lhe pedida os detalhes da ligação:  
   
    ![Passo de criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 
3. Introduza os detalhes da base de dados SQL. Propriedades com um asterisco são necessárias.
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Ligar através do Gateway |Deixe desmarcada. Isto é utilizado ao estabelecer ligação a um servidor de SQL no local. |
   | Nome da ligação * |Introduza um nome para a sua ligação. |
   | Nome do servidor SQL * |Introduza o nome do servidor; qual é semelhante ao seguinte *servername.database.windows.net*. O nome do servidor é apresentado nas propriedades da base de dados SQL no portal do Azure e também é apresentado na cadeia de ligação. |
   | Nome da base de dados SQL * |Introduza o nome que deu a sua base de dados do SQL Server. Este é listado nas propriedades da base de dados do SQL Server na cadeia de ligação: Initial Catalog =*yoursqldbname*. |
   | Nome de utilizador * |Introduza o nome de utilizador que criou quando a base de dados do SQL Server foi criado. Este é listado nas propriedades da base de dados SQL no portal do Azure. |
   | Palavra-passe * |Introduza a palavra-passe que criou quando a base de dados do SQL Server foi criado. |
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para estabelecer ligação e aceder aos seus dados do SQL Server. Depois de concluído, os detalhes de ligação semelhante ao seguinte:  
   
    ![Passo de criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. Selecione **Criar**. 
5. Tenha em atenção de que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica: 
   
    ![Passo de criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/table.png)

