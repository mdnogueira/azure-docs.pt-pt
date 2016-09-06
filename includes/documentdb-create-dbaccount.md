1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  Na Barra de índice, clique em **Novo**, clique em **Dados + Armazenamento** e, em seguida, clique em **DocumentDB (NoSQL)**.

    ![Captura de ecrã do portal do Azure, com destaque para Mais Serviços e DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. No painel **Nova conta**, especifique a configuração pretendida para a conta do DocumentDB.

    ![Captura de ecrã do painel DocumentDB Novo](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


    - Na caixa **ID**, introduza um nome para identificar a conta DocumentDB.  Quando o **ID** é validado, é exibida uma marca de verificação verde na caixa **ID**. O valor **ID** torna-se o nome de anfitrião no URI. O **ID** pode conter apenas minúsculas, números, o caráter '-' e deve ter entre 3 e 50 carateres. Tenha em atenção que *documents.azure.com* é acrescentado ao nome do ponto final que escolher, o resultado final irá tornar-se o ponto de final da conta DocumentDB.

    - Para **Subscrição**, selecione a subscrição do Azure com a qual pretende utilizar a conta DocumentDB. Por predefinição, se a sua conta tiver apenas uma subscrição, essa conta é selecionada.

    - Em **Grupo de Recursos**, selecione ou crie um grupo de recursos para a sua conta DocumentDB.  Por predefinição, é criado um novo grupo de recursos. Para obter mais informações, veja [Utilizar o Portal do Azure para gerir os recursos do Azure](../articles/azure-portal/resource-group-portal.md).

    - Utilize **Localização** para especificar a localização geográfica na qual vai alojar a sua conta DocumentDB. 
    
    - Para fornecer acesso prático à conta e recursos que criar no futuro, selecione **Afixar ao dashboard**.  

4.  Assim que as novas opções da conta DocumentDB estejam configuradas, clique em **Criar**. Para verificar o estado da implementação, pode monitorizar o progresso no Startboard.  
    ![Captura de ecrã do mosaico Criar no Startboard – Criador de base de dados online](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)  

    Em alternativa, pode monitorizar o seu progresso a partir do Hub de notificações.  

    ![Criar bases de dados rapidamente – Captura de ecrã do hub de notificações, que mostra o processo de criação da conta DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Captura de ecrã do Hub de notificações, que mostra que a conta DocumentDB foi criada com êxito e implementada num grupo de recursos – Notificação do criador de base de dados online](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  Depois da criação da conta DocumentDB, esta está pronta para ser utilizada com as predefinições. Tenha em atenção que a consistência predefinida da conta DocumentDB está definida como **Sessão**.  Pode ajustar a consistência predefinida. Para tal, clique em **Consistência Predefinida** no menu do recurso. Para saber mais sobre os níveis de consistência que o DocumentDB oferece, veja [Consistency levels in DocumentDB (Níveis de consistência no DocumentDB)](../articles/azure-portal/resource-group-portal.md).

    ![Captura de ecrã do painel do Grupo de Recursos – iniciar o desenvolvimento de aplicações](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Captura de ecrã do painel Nível de Consistência – Consistência da Sessão](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[Como: criar uma conta DocumentDB.]: #Howto
[Passos seguintes]: #NextSteps
[documentdb-gerir]:../articles/documentdb/documentdb-manage.md



<!--HONumber=ago16_HO5-->


