
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## <a name="create-a-new-azure-sql-server-level-firewall"></a>Criar uma nova firewall ao nível do servidor do Azure SQL
Utilize os seguintes passos no portal do Azure para criar uma regra de firewall ao nível do servidor para permitir ligações a partir de um endereço IP individual (o computador cliente) ou de todo um intervalo de endereços IP a um servidor lógico da Base de Dados SQL.

1. Se não estiver atualmente ligado, ligue-se ao [Portal do Azure](http://portal.azure.com).
2. No painel predefinido, clique em **SQL servers**.
   
      ![Nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)
3. No painel **SQL servers**, clique no servidor em que pretende criar a regra de firewall.
   
     ![Nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)
4. Reveja as propriedades do servidor e, em seguida, clique em **Firewall**.
   
     ![Nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)
   
   > [!NOTE]
   > Pode ainda aceder ao painel **Definições da firewall** ao nível do servidor a partir da barra de ferramentas do painel **Base de dados**.
    
    
6. Clique em **Adicionar IP de cliente** para que o Azure preencha automaticamente o seu endereço IP para as caixas de regra.
   
      ![Nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)
7. Opcionalmente, para adicionar acesso a um intervalo de endereços IP, clique no endereço IP que foi adicionado ao editar o endereço de firewall.
   
      ![Nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)
8. Clique em **Guardar** para criar a regra de firewall ao nível do servidor.
   
     ![Nova firewall do servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)
   
   > [!IMPORTANT]
   > O endereço IP do cliente poderá ser alterado de tempos a tempos e poderá não conseguir aceder ao servidor até criar uma nova regra de firewall. Pode verificar o seu endereço IP utilizando o [Bing](http://www.bing.com/search?q=my%20ip%20address). Em seguida, adicione um único endereço IP ou um intervalo de endereços IP. Veja [Gerir as definições da firewall](../articles/sql-database/sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal) para obter detalhes.
   > 
   > 



<!--HONumber=Nov16_HO5-->


