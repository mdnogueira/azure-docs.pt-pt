
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Inicie sessão no [portal do Azure](https://portal.azure.com/) em http://portal.azure.com/.
2. Na faixa da esquerda, clique em **Procurar tudo**. O **procurar** é apresentado o painel.
3. Desloque-se e clique em **servidores SQL**. O **servidores SQL** é apresentado o painel.
   
    ![Localizar o servidor da SQL Database do Azure no portal][b21-FindServerInPortal]
4. Para sua comodidade, clique no controlo de minimizar o anteriores no **procurar** painel.
5. Na caixa de texto de filtro, comece a escrever o nome do seu servidor. A linha é apresentada.
6. Clique na linha para o servidor. É apresentado um painel para o servidor.
7. No painel do servidor, clique em **definições**. O **definições** é apresentado o painel.
8. Clique em **Firewall**. O **as definições da Firewall** é apresentado o painel.
   
    ![Clique em Definições > Firewall][b31-SettingsFirewallNavig]
9. Clique em **Adicionar cliente IP**. Escreva um nome para a nova regra na primeira caixa de texto.
10. Escreva os valores de endereço IP baixos e alto para o intervalo de que pretende ativar.
    
    * Pode ser útil ter end valor baixo com **.0** e alta com **.255**.
    
    ![Adicionar um intervalo de endereços IP para permitir][b41-AddRange]
11. Clique em **Guardar**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
