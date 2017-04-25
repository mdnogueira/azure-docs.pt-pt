### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Criar um novo servidor SQL lógico no portal do Azure

1. Clique em **Novo**, procure **servidor lógico** e prima **ENTER**.

    ![procurar servidor lógico](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. Selecione **Servidor SQL (servidor lógico)** 

    ![selecionar servidor lógico](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. Clique em **Criar** para abrir o painel do novo SQL Server (servidor lógico).

   <kbd> ![painel “abrir servidor lógico”](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd>
    <kbd>![painel “servidor lógico”](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png) </kbd>
  
3. Na caixa de texto do nome do servidor do painel do SQL Server (servidor lógico), indique um nome válido para o servidor lógico novo. Uma marca de verificação verde indica que forneceu um nome válido.
    
    ![novo nome do servidor](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > O nome completamente qualificado do novo servidor será <o_seu_nome_de_servidor>.database.windows.net.
    >
    
4. Na caixa de texto de início de sessão de administrador do Servidor, forneça um nome de utilizador para o início de sessão de autenticação SQL para este servidor. Este início de sessão é conhecido como o início de sessão do principal do servidor. Uma marca de verificação verde indica que forneceu um nome válido.
    
    ![início de sessão de administrador SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. Nas caixas de texto **Palavra-passe** e **Confirmar palavra-passe**, forneça uma palavra-passe para a conta de início de sessão do principal do servidor. Uma marca de verificação verde indica que forneceu uma palavra-passe válida.
    
    ![palavra-passe de administrador SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. Selecione uma subscrição em que tenha permissão para criar objetos.

    ![subscrição](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. Na caixa de texto Grupo de recursos, selecione **Criar novo** e, em seguida, na caixa de texto do grupo de recursos, forneça um nome válido para o novo grupo de recursos (também pode utilizar um grupo de recursos existente se já tiver criado um para si). Uma marca de verificação verde indica que forneceu um nome válido.

    ![novo grupo de recursos](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. Na caixa de texto **Localização**, selecione um datacenter adequado à sua localização, como, por exemplo, "Leste da Austrália".
    
    ![localização do servidor](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > A caixa de verificação **Permitir que os serviços do azure acedam ao servidor** não pode ser alterada neste painel. Pode alterar esta definição no painel da firewall do servidor. Para obter mais informações, veja o artigo [Introdução à segurança](../articles/sql-database/sql-database-manage-servers-portal.md).
    >
    
9. Clique em **Criar**.

    ![botão criar](./media/sql-data-warehouse-create-logical-server/create.png)

