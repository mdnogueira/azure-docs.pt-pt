
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## Criar uma nova firewall ao nível do servidor do Azure SQL

Utilize os seguintes passos no portal do Azure para criar uma regra de firewall ao nível do servidor para permitir ligações a partir de um endereço IP individual (o computador cliente) ou de todo um intervalo de endereços IP a um servidor lógico SQL. 

1. Se não estiver atualmente ligado, ligue-se ao [Portal do Azure](http://portal.azure.com).
2. No painel predefinido, clique em **SQL Server**.

    ![nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)

2. No painel do SQL Server, clique no SQL Server em que pretende criar a regra de firewall. 

    ![nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)
           
3. Reveja as propriedades do servidor.

    ![nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)
      
4. No painel Definições, clique em **Firewall**.

    ![nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)
    

    > [AZURE.IMPORTANT] Se não vir a opção para a **Firewall** no painel que está a visualizar, retroceda e verifique se está a visualizar o painel do servidor lógico da SQL Database e não o painel de uma base de dados SQL.

5. Clique em **Adicionar IP do Cliente** para que o Azure crie uma regra para o endereço IP do cliente.

      ![nova firewall do servidor]    (./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)

6. Opcionalmente, clique no endereço IP que foi adicionado ao editar o endereço de firewall para permitir o acesso a um intervalo de endereços IP.

      ![nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)
    
7. Clique em **Guardar** para criar a regra de firewall ao nível do servidor.

     ![nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)

    >[AZURE.IMPORTANT] O seu Endereço IP do cliente poderá ser alterado de tempos a tempos e poderá não conseguir aceder ao servidor até criar uma nova regra de firewall. Pode verificar o endereço IP com o [Bing](http://www.bing.com/search?q=my%20ip%20address) e, em seguida, adicionar um único endereço IP ou um intervalo de endereços IP. Veja [Gerir as definições da firewall](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal) para obter detalhes.



<!--HONumber=Jun16_HO2-->


