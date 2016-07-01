<properties
    pageTitle="Criar um conjunto de bases de dados elásticas com o #C | Microsoft Azure"
    description="Utilize técnicas de programação de base de dados C# para criar um conjunto de bases de dados elásticas dimensionáveis na Base de Dados SQL do Azure, para poder partilhar recursos em muitas bases de dados."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Criar um novo conjunto de bases de dados elásticas com C&#x23;

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Saiba como criar um [conjunto de bases de dados elásticas](sql-database-elastic-pool.md), utilizando C&#x23;. 

Para códigos de erro comuns, consulte [Códigos de erro de SQL para aplicações de clientes de Base de Dados SQL: erro de ligação à base de dados e outros problemas](sql-database-develop-error-messages.md).

Os conjuntos de bases de dados elásticas estão atualmente na pré-visualização e só estão disponíveis com os servidores V12 de Base de Dados SQL. Se tiver um servidor V11 de Base de Dados SQL, pode [utilizar o PowerShell para atualizar para o V12 e criar um conjunto](sql-database-upgrade-server-portal.md), num único passo.

Os exemplos utilizam a [Biblioteca de Base de Dados SQL para .NET](https://msdn.microsoft.com/library/azure/mt349017.aspx). Por conseguinte, terá de instalar a biblioteca. Pode instalá-la ao executar seguinte comando na [consola do gestor de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) no Visual Studio (**Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre

## Criar um novo conjunto

Crie uma instância [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient), utilizando os valores [do Azure Active Directory](sql-database-client-id-keys.md). Crie uma instância [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters) e chame o método [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate). Os valores de eDTU por conjunto e das Dtus mínimas e máximas estão restritos pelo valor da camada de serviço (básica, standard ou premium). Consulte [eDTU e limites de armazenamento para conjuntos elásticos e bases de dados elásticas](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Criar uma nova base de dados num conjunto

Crie uma instância [DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties) e defina as propriedades da nova base de dados. Em seguida, chame o método CreateOrUpdate com o grupo de recursos, o nome do servidor e o nome da nova base de dados.

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

Para mover uma base de dados existente para um conjunto, consulte [Mover uma base de dados para um conjunto elástico](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool).

## Exemplo: criar um conjunto com C&#x23

Este exemplo cria um novo grupo de recursos do Azure, uma nova instância do Azure SQL Server e um novo conjunto elástico. 
 

As bibliotecas seguintes são necessárias para executar este exemplo. Pode instalá-las ao executar os seguintes comandos na [consola do gestor de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) no Visual Studio (**Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**)

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

Crie uma aplicação da consola e substitua os conteúdos de Program.cs pelos seguintes. Para instalar o id de cliente e os valores relacionados necessários, consulte [Registar a aplicação e obter os valores de cliente necessários para ligar a aplicação à Base de Dados SQL](sql-database-client-id-keys.md). Utilize o cmdlet [Get-AzureRmSubscription](https://msdn.microsoft.com/library/mt619284.aspx) para obter o valor para o subscriptionId.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
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

- [Gerir o conjunto](sql-database-elastic-pool-manage-csharp.md)
- [Criar tarefas elásticas](sql-database-elastic-jobs-overview.md) As tarefas elásticas permitem scripts T-SQL em qualquer número de bases de dados no conjunto.
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): utilize ferramentas de base de dados elástica para aumentar horizontalmente.

## Recursos Adicionais

- [Base de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [APIs de Gestão de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)


<!--HONumber=Jun16_HO2-->


