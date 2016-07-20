

## Ligar à SQL Database do Azure com um início de sessão principal ao nível do servidor

Utilize os passos seguintes para ligar à SQL Database do Azure com o SSMS utilizando um início de sessão principal ao nível do servidor.

1. Escreva “Microsoft SQL Server Management Studio” na caixa de pesquisa do Windows e clique na aplicação de ambiente de trabalho para iniciar o SSMS.

2. Na janela Ligar ao Servidor, introduza as seguintes informações:

 - **Tipo de servidor**: o valor predefinido é motor de base de dados; não o altere.
 - **Nome do servidor**: introduza o nome do servidor que aloja a base de dados SQL no seguinte formato: *&lt;nomedoservidor>*.**database.windows.net**
 - **Tipo de autenticação**: se estiver a começar, selecione Autenticação SQL. Se tiver ativado o Active Directory para o servidor lógico da SQL Database, pode selecionar Autenticação por Palavra-passe do Active Directory ou Autenticação Integrada do Active Directory.
 - **Nome de utilizador**: se tiver selecionado Autenticação SQL ou Autenticação por Palavra-passe do Active Directory, introduza o nome de um utilizador com acesso a uma base de dados no servidor.
 - **Palavra-passe**: se tiver selecionado Autenticação SQL ou Autenticação por Palavra-passe do Active Directory, introduza a palavra-passe para o utilizador especificado.
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-1.png)

3. Clique em **Ligar**.
 
4. Se o endereço IP do cliente não tiver acesso ao servidor lógico da SQL Database, ser-lhe-á pedido que inicie sessão numa conta do Azure e que crie uma regra de firewall ao nível do servidor. Se for um administrador de subscrição do Azure, clique em **Iniciar sessão** para criar uma regra de firewall ao nível do servidor. Caso contrário, solicite a um administrador do Azure que crie uma regra de firewall ao nível do servidor.
 
      ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-2.png)
 
1. Se for um administrador de subscrição do Azure e precisar de iniciar sessão, quando for apresentada a página de início de sessão, forneça as credenciais para a sua subscrição e inicie sessão.

      ![iniciar sessão](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-3.png)
 
1. Após iniciar sessão no Azure, reveja a regra de firewall ao nível do servidor proposta (pode modificá-la para permitir um intervalo de endereços IP) e clique em **OK** para criar a regra da firewall e concluir a ligação à SQL Database.
 
      ![nova firewall ao nível do servidor](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-4.png)
 
5. Se as suas credenciais lhe derem acesso, o Object Explorer será apresentado e poderá realizar tarefas administrativas ou consultar dados. 
 
     ![nova firewall ao nível do servidor](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)
 
     
## Resolver problemas relacionados com falhas de ligação

A razão mais comum para as falhas de ligação prende-se com erros no nome do servidor (não se esqueça, <*nomedoservidor*> é o nome do servidor lógico, não da base de dados), no nome de utilizador ou na palavra-passe, bem como o servidor não permitir ligações por motivos de segurança. 





<!--HONumber=Jun16_HO2-->


