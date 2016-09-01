

## Ligar à Base de dados SQL do Azure utilizando a Autenticação do SQL Server

Os passos seguintes mostram como ligar a um servidor SQL do Azure e a base de dados com SSMS. Se não tiver um servidor e uma base de dados, consulte o artigo [Criar uma base de dados SQL em minutos](../articles/sql-database/sql-database-get-started.md) para criar uma.


1. Inicie SSMS introduzindo **Microsoft SQL Server Management Studio** na caixa de pesquisa do Windows e clique na aplicação de ambiente de trabalho.

2. Na janela **Ligar ao servidor**, introduza as seguintes informações (se SSMS já está em execução, clique em **Ligar > Motor de base de dados** para abrir a janela **Ligar ao Servidor**):

 - **Tipo de servidor**: o valor predefinido é motor de base de dados; não o altere.
 - **Nome do servidor**: introduza o nome totalmente qualificado do servidor da Base de Dados SQL Azure no seguinte formato: *&lt;servername>*.**database.windows.net**
 - **Tipo de autenticação**: este artigo mostra-lhe como ligar através de **Autenticação do SQL Server**. Para obter detalhes sobre a ligação ao Azure Active Directory, veja o artigo [Estabelecer ligação utilizando a autenticação integrada do Active Directory](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-integrated-authentication), [Ligar através de autenticação de palavra-passe do Active Directory](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-password-authentication) e [Ligar utilizando o Active Directory Universal Authentication](../articles/sql-database/sql-database-ssms-mfa-authentication.md).
 - **Nome de utilizador**: introduza o nome de um utilizador com acesso a uma base de dados no servidor (por exemplo, o *administrador do servidor* que configurou ao criar o servidor). 
 - **Palavra-passe**: introduza a palavra-passe do utilizador especificado (por exemplo, a *palavra-passe* que configurou ao criar o servidor).
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect.png)

3. Clique em **Ligar**.
 
4. Por predefinição, os novos servidores não têm [regras de firewall](../articles/sql-database/sql-database-firewall-configure.md) definidas, por isso os clientes são impedidos de estabelecer ligação. Se o servidor ainda não tiver uma regra de firewall que permite a ligação do seu endereço IP, SSMS solicita-lhe para criar uma regra de firewall ao nível do servidor.

    Clique em **Iniciar sessão** e crie uma regra de firewall ao nível do servidor. Deve ser administrador do Azure para criar uma regra de firewall ao nível do servidor.
 
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/newfirewallrule.png)
 

5. Depois de ligar com êxito à base de dados SQL do Azure, o **Object Explorer** é aberto e pode agora aceder à base de dados para [desempenhar tarefas administrativas ou consultar dados](../articles/sql-database/sql-database-manage-azure-ssms.md).
 
     ![nova firewall ao nível do servidor](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)
 
     
## Resolver problemas relacionados com falhas de ligação

As razões mais comuns de falhas de ligação são erros no nome do servidor e problemas de conetividade de rede. Tenha em atenção que <*servername*> é o nome do servidor, não da base de dados, e terá de fornecer o nome de servidor totalmente qualificado: `<servername>.database.windows.net`

Além disso, verifique se o nome de utilizador e a palavra-passe não contêm quaisquer erros de digitação ou espaços adicionais (os nomes de utilizador não são sensíveis a maiúsculas e minúsculas, mas as palavras-passe são). 

Pode também definir explicitamente o número do protocolo e da porta com o nome do servidor, como o seguinte: `tcp:servername.database.windows.net,1433`

Os problemas de conetividade de rede também podem causar erros de ligação e tempos limite. Repetir a ligação (quando souber que o nome do servidor, as credenciais e as regras de firewall estão corretas) é suficiente para ser bem sucedido.






<!--HONumber=ago16_HO4-->


