## <a name="connect-to-azure-sql-database-as-a-user"></a>Ligar à SQL Database do Azure como um utilizador
Utilize os passos seguintes para ligar à SQL Database do Azure com o SSMS como um utilizador.

1. Escreva “Microsoft SQL Server Management Studio” na caixa de pesquisa do Windows e clique na aplicação de ambiente de trabalho para iniciar o SSMS.
2. Na janela Ligar ao Servidor, introduza as seguintes informações:

* **Tipo de servidor**: o valor predefinido é motor de base de dados; não o altere.
  
  * **Nome do servidor**: introduza o nome do servidor que aloja a base de dados SQL no seguinte formato: *&lt;nomedoservidor>*.**database.windows.net**
  * **Tipo de autenticação**: se estiver a começar, selecione Autenticação SQL. Se tiver ativado o Active Directory para o servidor lógico da SQL Database, pode selecionar Autenticação por Palavra-passe do Active Directory ou Autenticação Integrada do Active Directory.
  * **Nome de utilizador**: se tiver selecionado Autenticação SQL ou Autenticação por Palavra-passe do Active Directory, introduza o nome de um utilizador com acesso a uma base de dados no servidor.
  * **Palavra-passe**: se tiver selecionado Autenticação SQL ou Autenticação por Palavra-passe do Active Directory, introduza a palavra-passe para o utilizador especificado.
    
       ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-sql-server-management-studio-connect-user/connect-user-1.png)

1. Clique em **Opções** para especificar a base de dados à qual pretende ligar.
   
      ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-sql-server-management-studio-connect-user/connect-user-2.png)
2. Em **Ligar à Base de Dados**, selecione a base de dados à qual pretende ligar.
   
     ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-sql-server-management-studio-connect-user/connect-user-3.png)
3. Clique em **Ligar**.
4. Se o endereço IP do cliente não tiver acesso ao servidor lógico da SQL Database, ser-lhe-á pedido que inicie sessão numa conta do Azure e que crie uma regra de firewall ao nível do servidor. Se for um administrador de subscrição do Azure, clique em **Iniciar sessão** para criar uma regra de firewall ao nível do servidor. Caso contrário, solicite a um administrador que crie uma regra de firewall ao nível do servidor ou uma regra de firewall ao nível da base de dados na base de dados a qual está a tentar ligar.
   
      ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-sql-server-management-studio-connect-user/connect-user-4.png)
5. Se as suas credenciais lhe concederem acesso à base de dados especificada, o Object Explorer será apresentado e poderá realizar tarefas administrativas ou consultar dados, consoante as permissões de utilizador.
   
      ![SQL Server Management Studio: ligar ao servidor da SQL Database](./media/sql-database-sql-server-management-studio-connect-user/connect-user-5.png)

## <a name="troubleshoot-connection-failures"></a>Resolver problemas relacionados com falhas de ligação
A razão mais comum para as falhas de ligação prende-se com erros no nome do servidor (não se esqueça, <*nomedoservidor*> é o nome do servidor lógico, não da base de dados), no nome de utilizador ou na palavra-passe, bem como o servidor não permitir ligações por motivos de segurança. 



<!--HONumber=Nov16_HO2-->


