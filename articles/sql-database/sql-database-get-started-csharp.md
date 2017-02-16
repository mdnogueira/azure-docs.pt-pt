---
title: "C#: Introdução à Base de Dados SQL do Azure | Microsoft Docs"
description: "Experimente a Base de Dados SQL para desenvolver aplicações SQL e C#, e crie uma Base de Dados SQL do Azure com C# utilizando a Biblioteca da Base de Dados SQL para .NET."
keywords: experimentar sql, sql c#
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: cfff2299-a474-4054-8d99-759af1ae5188
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: d55c08dbd1d2372e5396f40e74b4f63f3e2f0022


---
# <a name="use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>Utilizar C# para criar uma base de dados SQL com a Biblioteca da Base de Dados SQL para .NET

Saiba como utilizar o C# para criar uma base de dados SQL do Azure com a [Biblioteca de Gestão da Base de Dados SQL do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Este artigo descreve como criar uma base de dados única com o SQL Server e o C#. Para criar conjuntos elásticos, consulte [Create an elastic pool (Criar um conjunto elástico)](sql-database-elastic-pool-create-csharp.md).

A Biblioteca de Gestão da Base de Dados SQL do Azure para .NET fornece uma API baseada no [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) que encapsula num wrapper a [API REST da Base de Dados SQL baseada no Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx).

> [!NOTE]
> Muitas funcionalidades novas da Base de Dados SQL só são suportadas quando utilizar o [Modelo de implementação Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Assim, deverá sempre utilizar a mais recente **Biblioteca de Gestão da Base de Dados SQL do Azure para .NET ([docs](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. O antigo [modelo de implementação clássica baseado em bibliotecas](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) é apenas suportado para a retrocompatibilidade. Assim, recomendamos a utilização das bibliotecas mais recentes baseadas no Resource Manager.
> 
> 

Para executar os passos descritos neste artigo é necessário o seguinte:

* Uma subscrição do Azure. Se precisar de uma subscrição do Azure, basta clicar em ** GRATUITA** no início desta página e, em seguida, voltar para terminar este artigo.
* Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs).

> [!NOTE]
> Este artigo cria uma nova base de dados SQL em branco. Modifique o método *CreateOrUpdateDatabase(...)* no exemplo seguinte para copiar bases de dados, dimensionar bases de dados, criar uma base de dados num conjunto, etc. Para obter mais informações, veja as classes [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) e [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx).
> 
> 

## <a name="create-a-console-app-and-install-the-required-libraries"></a>Criar uma aplicação de consola e instale as bibliotecas necessárias
1. Inicie o Visual Studio.
2. Clique em **Ficheiro** > **ovo** > **Projeto**.
3. Crie uma **Aplicação de Consola** #C e nomeie-a: *SqlDbConsoleApp*

Para criar uma base de dados do SQL com o C#, carregue as bibliotecas de gestão necessárias (utilizando a [consola do gestor de pacote](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola de Gestor de Pacotes**.
2. Escreva `Install-Package Microsoft.Azure.Management.Sql -Pre` para instalar a [Biblioteca de Gestão da Base de Dados SQL do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) mais recente.
3. Escreva `Install-Package Microsoft.Azure.Management.ResourceManager -Pre` para instalar a [Biblioteca do Microsoft Azure Resource Manager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Escreva `Install-Package Microsoft.Azure.Common.Authentication -Pre` para instalar a [Biblioteca de Autenticação Comum do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 

> [!NOTE]
> Os exemplos neste artigo utilizam uma forma síncrona de cada pedido e bloco de API até à conclusão da chamada no serviço subjacente. Existem métodos assíncronos disponíveis.
> 
> 

## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>Criar uma Base de dados SQL, uma regra de firewall e uma base de dados do SQL – exemplo #C
O exemplo seguinte cria um grupo de recursos, um servidor, uma regra de firewall e uma base de dados SQL. Consulte, [Criar um principal de serviço para aceder aos recursos](#create-a-service-principal-to-access-resources) para obter as `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` variáveis.

Substituir o conteúdo de **Program.cs** com o seguinte e atualizar as `{variables}` com os valores de aplicação (não incluir `{}`).

    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>Criar um principal de serviço para aceder aos recursos
O seguinte script do PowerShell cria a aplicação do Active Directory (AD) e o principal de serviço que é necessário para autenticar a nossa aplicação C#. O script produz os valores de necessários para a amostra do C# precedente. Para obter informações detalhadas, consulte [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret



## <a name="next-steps"></a>Passos seguintes
Agora que já experimentou a Base de Dados SQL e configurou uma base de dados com C#, está pronto para os seguintes artigos:

* [Ligar à Base de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Recursos Adicionais
* [Base de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [Classe de Base de Dados](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)

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



<!--HONumber=Jan17_HO2-->


