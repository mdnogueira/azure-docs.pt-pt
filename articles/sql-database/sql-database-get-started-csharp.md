<properties
    pageTitle="Experimentar a Base de Dados SQL: utilizar C# para criar uma base de dados SQL | Microsoft Azure"
    description="Experimente a Base de Dados SQL para desenvolver aplicações SQL e C#, e crie uma Base de Dados SQL do Azure com C# utilizando a Biblioteca da Base de Dados SQL para .NET."
    keywords="experimentar sql, sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="05/26/2016"
   ms.author="sstein"/>

# Experimentar a Base de Dados SQL: utilizar C# para criar uma base de dados SQL com a Biblioteca da Base de Dados SQL para .NET


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Saiba como utilizar comandos C# para criar uma base de dados SQL do Azure com a [Biblioteca da Base de Dados SQL do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Irá experimentar a Base de Dados SQL criando uma única base de dados com SQL e C#. Para criar conjuntos de bases de dados elásticas, consulte o artigo [Criar um conjunto de bases de dados elásticas](sql-database-elastic-pool-create-portal.md). Fragmentos de código individuais são divididos para efeitos de clareza e um exemplo de aplicação da consola reúne todos os comandos na secção no final deste artigo.

A Biblioteca da Base de Dados SQL do Azure para .NET fornece uma API baseada no [Azure Resource Manager](../resource-group-overview.md) que encapsula num wrapper a [API REST da Base de Dados SQL baseada no Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx). Esta biblioteca de clientes segue o padrão comuns das bibliotecas de clientes baseados no Resource Manager. O Resource Manager requer grupos de recursos e a autenticação no [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] A Biblioteca da Base de Dados SQL para .NET encontra-se atualmente em pré-visualização.

<br>

Para executar os passos descritos neste artigo, é necessário o seguinte:

- Uma subscrição do Azure. Se precisar de uma subscrição do Azure, basta clicar em **AVALIAÇÃO GRATUITA** no início desta página e, em seguida, voltar para terminar este artigo.
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Instalar as bibliotecas necessárias

Para configurar uma base de dados SQL com C#, obtenha as bibliotecas de gestão necessárias, instalando os seguintes pacotes com a [consola do gestor de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) no Visual Studio (**Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.ResourceManager –Pre -Version 1.1.1-preview
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurar a autenticação no Azure Active Directory

Para aceder ao serviço Base de Dados SQL, tem de ativar primeiro a aplicação cliente configurando a autenticação necessária.

Para autenticar a sua aplicação cliente com base no utilizador atual, tem de registar primeiro a sua aplicação no domínio do AAD associado à subscrição com a qual os recursos do Azure foram criados. Se a sua subscrição do Azure tiver sido criada com uma conta Microsoft em vez de uma conta escolar ou profissional, já terá um domínio do AAD predefinido.

Para criar uma nova aplicação e registá-la no Active Directory correto, efetue o seguinte:

1. Aceda ao [Portal Clássico do Azure](https://manage.windowsazure.com/).
1. No lado esquerdo, selecione o serviço **Active Directory** e, em seguida, selecione o diretório para autenticar a aplicação e clique no respetivo **Nome**.

    ![Experimentar a Base de Dados SQL: configurar o Azure Active Directory (AAD).][1]

2. Na página do diretório, clique em **APLICAÇÕES**.

    ![A página do diretório com Aplicações.][5]

4. Clique em **ADICIONAR** para criar uma nova aplicação.

5. Selecione **Adicionar uma aplicação que a minha organização está a desenvolver**.

5. Forneça um **NOME** para a aplicação e selecione **APLICAÇÃO CLIENTE NATIVA**.

    ![Forneça informações sobre a aplicação SQL C#.][7]

6. Forneça um **URI DE REDIRECIONAMENTO**. Não tem de ser um ponto final real, apenas um URI válido.

    ![Adicionar um URL de redirecionamento para a sua aplicação SQL C#.][8]

7. Conclua a criação da aplicação, clique em **CONFIGURAR** e copie o **ID DE CLIENTE** (irá precisar do ID de cliente mais à frente no código).

    ![Obter o ID de cliente para a sua aplicação SQL C#.][9]


1. Na parte inferior da página, clique em **Adicionar aplicação**.
1. Selecione **Aplicações da Microsoft**.
1. Selecione **API de Gestão de Serviços do Azure** e, em seguida, conclua o assistente.
2. Com a API selecionado, precisa agora de conceder as permissões específicas necessárias para aceder a esta API, selecionando **Aceder à Gestão de Serviços do Azure (pré-visualização)**.

    ![Definir permissões.][2]

2. Clique em **GUARDAR**.



### Identificar o nome de domínio

O nome de domínio é necessário para o seu código. Uma forma fácil de identificar o nome de domínio correto é:

1. Aceda ao [Portal do Azure](http://portal.azure.com).
2. Colocar o cursor sobre o seu nome no canto superior direito e anotar o Domínio que aparece na janela de pop-up.

    ![Identificar o nome de domínio.][3]





**Recursos Adicionais do AAD**  

Pode encontrar informações adicionais sobre a utilização do Azure Active Directory para autenticação nesta [mensagem de blogue útil](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Obter o token de acesso para o utilizador atual

A aplicação cliente tem de obter o token de acesso de aplicação para o utilizador atual. A primeira vez que este código for executado por um utilizador, é-lhe pedido que introduza as respetivas credenciais de utilizador e o token resultante é colocado em cache localmente. As execuções subsequentes irão obter o token da cache e será apenas pedido ao utilizador que inicie sessão se o token tiver expirado.

Este código devolve um Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult de que irá precisar nos outros fragmentos de código abaixo.

        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }



> [AZURE.NOTE] Os exemplos neste artigo utilizam uma forma síncrona de cada pedido e bloco de API até à conclusão da chamada no serviço subjacente. Existem métodos assíncronos disponíveis.



## Criar um grupo de recursos:

Com o Resource Manager, todos os recursos têm de ser criados num grupo de recursos. Um grupo de recursos é um contentor que retém recursos relacionados para uma aplicação. Com a Base de Dados SQL do Azure, o servidor de base de dados tem de ser criado dentro de um grupo de recursos existente.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Criar um servidor

As bases de dados SQL estão contidas em servidores. O nome do servidor tem de ser exclusivo global entre todos os servidores SQL do Azure, pelo que irá obter aqui um erro se o nome do servidor já estiver atribuído. Também vale a pena realçar que este comando pode demorar vários minutos a concluir.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Criar um administrador de servidor externo


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Criar uma regra de firewall de servidor para permitir o acesso ao servidor

Por predefinição, não é possível ligar a um servidor a partir de qualquer localização. Para ligar a um servidor ou a quaisquer bases de dados no servidor, tem de ser definida uma [regra de firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) que permita o acesso a partir do endereço IP do cliente.

O exemplo seguinte cria uma regra que abre o acesso ao servidor a partir de qualquer endereço IP. É recomendado que crie inícios de sessão e palavras-passe de SQL adequados para proteger a sua base de dados e não confiar nas regras de firewall como uma defesa principal contra intrusões.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Para permitir que outros serviços do Azure acedam a um servidor, adicione uma regra de firewall e defina StartIpAddress e EndIpAddress como 0.0.0.0. Tenha em atenção que esta definição permite que o tráfego do Azure de *qualquer* subscrição do Azure aceda ao servidor.


## Utilizar C# para criar uma base de dados SQL

O seguinte comando C# irá criar uma nova base de dados SQL se ainda não existir uma base de dados com o mesmo nome no servidor. Se já existir uma base de dados com o mesmo nome, este será atualizado.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Exemplo de aplicação da consola C#

O exemplo seguinte cria um grupo de recursos, um servidor, uma regra de firewall e uma base de dados SQL. A secção *Configurar a autenticação no Azure Active Directory* no início deste artigo mostra onde pode obter os valores para as variáveis clientId, redirectUri e domainName.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";

        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString()
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }

        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }




## Passos seguintes
Agora que já experimentou a Base de Dados SQL e configurou uma base de dados com C#, está pronto para os seguintes artigos:

- [Ligar à Base de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo](sql-database-connect-query-ssms.md)

## Recursos Adicionais

- [Base de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png



<!--HONumber=Aug16_HO1-->


