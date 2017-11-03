---
title: "Criar aplicações de .NET de autenticação não interativa no Azure HDInsight | Microsoft Docs"
description: "Saiba como criar aplicações do Microsoft .NET de autenticação não interativa no Azure HDInsight."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jgao
ms.openlocfilehash: c1aeed8c6fe4f83b170838e4efb9d9c4744fb951
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Criar uma aplicação do HDInsight de .NET de autenticação não interativa
Pode executar a aplicação do Microsoft .NET do Azure HDInsight com a identidade da aplicação (não interativo) ou com a identidade do utilizador com sessão iniciada da aplicação (interativa). Este artigo mostra como criar uma aplicação .NET para ligar ao Azure e gerir HDInsight de autenticação não interativa. Para um exemplo de uma aplicação interativa, consulte [ligar ao Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

A aplicação .NET não interativa, é necessário:

* O ID de inquilino da subscrição do Azure (também designado por um *ID de diretório*). Consulte [obter ID de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* O ID de cliente de aplicações do Azure Active Directory (Azure AD). Consulte [criar uma aplicação do Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) e [obter uma ID da aplicação](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* O Azure AD aplicação chave secreta. Consulte [chave de autenticação de aplicação Get](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster do HDInsight. Consulte o [tutorial de introdução](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Atribuir uma função para a aplicação do Azure AD
Atribuir a aplicação do Azure AD um [função](../active-directory/role-based-access-built-in-roles.md), para conceder permissão para efetuar ações. Pode definir o âmbito ao nível da subscrição, do grupo de recursos ou do recurso. As permissões são herdadas a níveis inferiores de âmbito. (Por exemplo, adicionar uma aplicação para a função de leitor para um grupo de recursos significa que a aplicação pode ler o grupo de recursos e quaisquer recursos na mesma.) Neste tutorial, é possível definir o âmbito ao nível do grupo de recursos. Para obter mais informações, consulte [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](../active-directory/role-based-access-control-configure.md).

**Para adicionar a função de proprietário para a aplicação do Azure AD**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **grupo de recursos**.
3. Selecione o grupo de recursos que tem o cluster do HDInsight no qual irá executar a consulta do Hive mais tarde no tutorial. Se tiver um grande número de grupos de recursos, pode utilizar o filtro para localizar aquela que pretende.
4. No menu de grupo de recursos, selecione **(IAM) do controlo de acesso**.
5. Em **utilizadores**, selecione **adicionar**.
6. Siga as instruções para adicionar a função de proprietário para a aplicação do Azure AD. Depois de adicionar a função com êxito, a aplicação está listada em **utilizadores**, com a função de proprietário. 

## <a name="develop-an-hdinsight-client-application"></a>Desenvolver uma aplicação de cliente do HDInsight

1. Crie uma aplicação de consola C#.
2. Adicione os seguintes pacotes de NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Execute o seguinte código:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Passos seguintes
* [Criar aplicação e serviço principal de um Azure Active Directory no portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Saiba como [autenticar um principal de serviço com o Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* Saiba mais sobre [controlo de acesso do Azure baseada em funções (RBAC)](../active-directory/role-based-access-control-configure.md).
