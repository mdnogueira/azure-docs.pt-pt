1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  Na Barra de índice, clique em **Novo**, clique em **Dados + Armazenamento** e, em seguida, clique em **DocumentDB (NoSQL)**.

    ![Captura de ecrã do portal do Azure, com destaque para Mais Serviços, e DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. No painel **Nova conta**, especifique a configuração pretendida para a conta do DocumentDB.

    ![Captura de ecrã do painel Novo DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    - Na caixa **ID**, introduza um nome para identificar a conta do DocumentDB.  Quando o **ID** é validado, é apresentada uma marca de verificação verde na caixa **ID**. O valor **ID** torna-se o nome de anfitrião no URI. O **ID** pode conter apenas minúsculas, números, o caráter '-' e deve ter entre 3 e 50 carateres. Tenha em atenção que *documents.azure.com* é acrescentado ao nome do ponto final que escolher, sendo que o resultado final irá tornar-se o ponto final da conta do DocumentDB.

    - Na caixa **API NoSQL**, selecione **DocumentDB**.  

    - Para **Subscrição**, selecione a subscrição do Azure que quer utilizar para a conta do DocumentDB. Se a sua conta tiver apenas uma subscrição, essa conta é selecionada por predefinição.

    - Em **Grupo de Recursos**, selecione ou crie um grupo de recursos para a sua conta do DocumentDB.  Por predefinição, é criado um novo grupo de recursos. Para obter mais informações, veja [Utilizar o portal do Azure para gerir os recursos do Azure](../articles/azure-portal/resource-group-portal.md).

    - Utilize a **Localização** para especificar a localização geográfica na qual vai alojar a sua conta do DocumentDB. 

4.  Assim que as novas opções da conta do DocumentDB estiverem configuradas, clique em **Criar**. Para verificar o estado da implementação, verifique o hub de Notificações.  

    ![Criar bases de dados rapidamente – Captura de ecrã do hub de Notificações, que mostra o processo de criação da conta do DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Captura de ecrã do hub de Notificações, que mostra que a conta do DocumentDB foi criada com êxito e implementada num grupo de recursos – Notificação do criador de base de dados online](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  Depois da criação da conta do DocumentDB, esta está pronta para ser utilizada com as predefinições. A consistência predefinida da conta do DocumentDB está definida como **Sessão**.  Pode ajustar a consistência predefinida. Para tal, clique em **Consistência Predefinida** no menu do recurso. Para saber mais sobre os níveis de consistência que o DocumentDB oferece, veja o artigo [Níveis de consistência no DocumentDB](../articles/documentdb/documentdb-consistency-levels.md).

    ![Captura de ecrã do painel Grupo de Recursos – iniciar a programação de aplicações](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Captura de ecrã do painel Nível de Consistência – Consistência da Sessão](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[Como: criar uma conta do DocumentDB]: #Howto
[Passos seguintes]: #NextSteps
[documentdb-gerir]:../articles/documentdb/documentdb-manage.md


<!--HONumber=Sep16_HO3-->


