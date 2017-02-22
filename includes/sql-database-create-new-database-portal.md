
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>Criar uma nova base de dados SQL do Azure
Utilize os seguintes passos no portal do Azure para criar uma nova base de dados SQL do Azure num servidor lógico da SQL Database do Azure, novo ou existente.

1. Se não estiver atualmente ligado, ligue-se ao [Portal do Azure](http://portal.azure.com).
2. Clique em **Novo**, escreva **Base de Dados SQL** e, em seguida, clique em ** Base de Dados SQL (nova base de dados)**.
   
     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)
3. Clique em **Base de Dados SQL (nova base de dados)**.
   
     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
4. Clique em **Criar** para criar uma nova base de dados no serviço SQL Database.
   
     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)
5. Forneça os valores para as seguintes propriedades do servidor:
   
   * Nome da base de dados
   * Subscrição: isto apenas se aplica se tiver várias subscrições.
   * Grupo de recursos: se estiver apenas a começar, utilize o grupo de recursos do servidor lógico.
   * Selecione a origem: pode escolher uma base de dados em branco, dados de exemplo ou uma cópia de segurança de uma base de dados do Azure. Para migrar uma base de dados do SQL Server no local ou carregar dados utilizando a ferramenta da linha de comandos BCP, consulte as hiperligações no final deste artigo.
   * Servidor: um servidor lógico novo ou existente.
   * Início de sessão de administrador do servidor
   * Palavra-passe
   * Escalão de preço: se estiver a começar, utilize o valor predefinido S0.
   * Agrupamento: isto apenas se aplica no caso de ser escolhida uma base de dados em branco.
     
        ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)
6. Clique em **Criar**. Na área de notificação, pode ver que a implementação foi iniciada.
   
    ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)
7. Aguarde o fim da implementação antes avançar para o passo seguinte.
   
     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)



<!--HONumber=Jan17_HO3-->


