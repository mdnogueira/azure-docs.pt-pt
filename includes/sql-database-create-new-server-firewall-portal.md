
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. No painel do servidor SQL, em Definições, clique em **Firewall** para abrir o painel Firewall do servidor SQL.

    <!-- ![sql server firewall](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png) -->

2. Reveja o endereço IP de cliente apresentado e confirme que o mesmo é o seu endereço IP na Internet através do browser da sua preferência (perguntar "qual é o meu endereço IP"). Ocasionalmente, os endereços IP não correspondem devido a vários motivos.

    <!-- ![your IP address](../articles/sql-database/media/sql-database-get-started/your-ip-address.png) -->

3. Partindo do princípio de que os endereços IP correspondem, clique em **Adicionar IP de cliente** na barra de ferramentas.

    ![adicionar IP de cliente](../articles/sql-data-warehouse/media/sql-data-warehouse-get-started-provision/add-client-ip.png)

    > [!NOTE]
    > Pode abrir a firewall da Base de Dados SQL no servidor para um único endereço IP ou um conjunto de endereços. Abrir a firewall permite aos utilizadores e administradores SQL iniciar sessão em qualquer base de dados no servidor para o qual tenham credenciais válidas.
    >

4. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor e, em seguida, clique em **OK**.

    ![adicionar IP de cliente](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png)

> [!Tip]
> Para obter um tutorial, veja o artigo [Tutorial da Base de Dados SQL: Criar um servidor, uma regra de firewall ao nível do servidor, uma base de dados de exemplo, uma regra de firewall ao nível da base de dados e ligar com o SQL Server](../articles/sql-database/sql-database-get-started.md).    
>
