---
title: "Utilizar o SDK .NET para desenvolver aplicações no Azure Data Lake Store | Microsoft Docs"
description: "Utilizar o SDK .NET do Azure Data Lake Store para criar uma conta do Data Lake Store e executar operações básicas no Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 70f94a07b0102e3135eaf85e5877e3502762d7e3
ms.contentlocale: pt-pt
ms.lasthandoff: 08/03/2017

---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introdução ao Azure Data Lake Store com SDK .NET
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [python](data-lake-store-get-started-python.md)
>
>

Saiba como utilizar o [SDK. NET do Azure Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet) para executar operações básicas, como criar pastas, carregar e transferir ficheiros de dados, etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo utilizam o Visual Studio 2015 Atualização 2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure Data Lake Store**. Para obter instruções sobre como criar uma conta, veja [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **Criar uma Aplicação do Azure Active Directory**. A aplicação do Azure AD é utilizada para autenticar a aplicação do Data Lake Store no Azure AD. Existem abordagens diferentes para a autenticação no Azure AD, que são **autenticação do utilizador final** ou **autenticação de serviço para serviço**. Para obter instruções e obter mais informações sobre como autenticar, veja [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticação de utilizador final) ou [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Autenticação de serviço a serviço).

## <a name="create-a-net-application"></a>Criar uma aplicação .NET
1. Abra o Visual Studio e crie uma aplicação de consola.
2. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**.
3. A partir de **Novo Projeto**, escreva ou selecione os seguintes valores:

   | Propriedade | Valor |
   | --- | --- |
   | Categoria |Templates/Visual C#/Windows |
   | Modelo |Aplicação de Consola |
   | Nome |CreateADLApplication |
4. Clique em **OK** para criar o projeto.
5. Adicione os pacotes Nuget ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   2. No separador **Gestor de Pacotes Nuget**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - Este tutorial utiliza a v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial utiliza a v2.2.12.

        ![Adicionar uma origem Nuget](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gestor de Pacotes NuGet**.
6. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. Declare as variáveis, conforme mostrado abaixo, e forneça os valores para o nome do Data Lake Store e o nome do grupo de recursos que já existe. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos aqui existem no computador. Adicione o fragmento de código seguinte após as declarações de espaço de nomes.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como a autenticação, carregamento de ficheiros, etc.

## <a name="authentication"></a>Autenticação

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Se estiver a utilizar autenticação de utilizador final (recomendada para este tutorial)

Utilize esta opção com uma aplicação nativa existente do Azure AD para autenticar a sua aplicação **interativamente**, o que significa que ser-lhe-á pedido que introduza as credenciais do Azure.

Para facilidade de utilização, o fragmento abaixo utiliza valores predefinidos para o ID de cliente e o URI de redirecionamento que irá funcionar com qualquer subscrição do Azure. Para o ajudar a concluir este tutorial mais depressa, recomendamos que utilize esta abordagem. No fragmento abaixo, basta indicar o valor para o seu ID de inquilino. Poderá recuperá-lo com as instruções fornecidas em [Criar uma Aplicação do Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

    // User login via interactive popup
    // Use the client ID of an existing AAD Web application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;

Algumas coisas que deve saber acerca do fragmento acima:

* Para o ajudar a concluir o tutorial mais depressa, este fragmento utiliza um domínio do Azure AD e um ID de cliente que estão disponível por predefinição para todas as subscrições do Azure. Por isso, pode **utilizar este fragmento tal como está na sua aplicação**.
* No entanto, se pretender utilizar o seu próprio domínio do Azure AD e o ID de cliente de aplicação, tem de criar uma aplicação nativa do Azure AD e, depois, utilizar o ID de inquilino do Azure AD, o ID de cliente e o URI de redirecionamento da aplicação que criou. Veja [Criar uma Aplicação do Active Directory para autenticação de utilizador final com o Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Se estiver a utilizar a autenticação serviço para serviço com o segredo do cliente
O fragmento seguinte pode ser utilizado para autenticar a aplicação de forma **não interativa** com o segredo/chave do cliente de uma aplicação/principal de serviço. Utilize esta opção com uma “Aplicação Web” do Azure AD. Para obter instruções sobre como criar a aplicação Web do Azure AD e como obter o ID de cliente e o segredo do cliente necessários no fragmento abaixo, veja [Criar uma Aplicação do Active Directory para autenticação serviço a serviço com o Data Lake Store](data-lake-store-authenticate-using-active-directory.md).

    // Service principal / appplication authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = await ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential);

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Se estiver a utilizar a autenticação serviço para serviço com certificado

Como terceira opção, o fragmento seguinte pode ser utilizado para autenticar a aplicação de forma **não interativa** através do certificado de uma aplicação/principal de serviço do Azure Active Directory. Utilize esta opção com uma [Aplicação do Azure AD com certificados](../azure-resource-manager/resource-group-authenticate-service-principal.md) existente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = await ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate);

## <a name="create-client-objects"></a>Criar objetos de cliente
O fragmento seguinte cria a conta do Data Lake Store e os objetos de cliente do sistema de ficheiros, utilizados para emitir pedidos ao serviço.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Listar todas as contas do Data Lake Store numa subscrição
O fragmento seguinte lista todas as contas do Data Lake Store numa determinada subscrição do Azure.

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>Criar um diretório
O fragmento seguinte mostra um método `CreateDirectory` que pode utilizar para criar um diretório numa conta do Data Lake Store.

    // Create a directory
    public static async Task CreateDirectory(string path)
    {
        await _adlsFileSystemClient.FileSystem.MkdirsAsync(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Carregar um ficheiro
O fragmento seguinte mostra um método `UploadFile` que pode utilizar para carregar ficheiros para uma conta do Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

O SDK suporta carregamento e transferência recursivos entre um caminho de ficheiro local e um caminho de ficheiro do Data Lake Store.    

## <a name="get-file-or-directory-info"></a>Obter as informações do ficheiro ou diretório
O fragmento seguinte mostra um método `GetItemInfo` que pode utilizar para obter informações sobre um ficheiro ou diretório disponível no Data Lake Store.

    // Get file or directory info
    public static async Task<FileStatusProperties> GetItemInfo(string path)
    {
        return await _adlsFileSystemClient.FileSystem.GetFileStatusAsync(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Listar o ficheiro ou os diretórios
O fragmento seguinte mostra um método `ListItem` que pode utilizar para listar o ficheiro e os diretórios numa conta do Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concatenar ficheiros
O fragmento seguinte mostra um método `ConcatenateFiles` que pode utilizar para concatenar ficheiros.

    // Concatenate files
    public static Task ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        await _adlsFileSystemClient.FileSystem.ConcatAsync(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro
O fragmento seguinte mostra um método `AppendToFile` que pode utilizar para acrescentar dados a um ficheiro já armazenado numa conta do Data Lake Store.

    // Append to file
    public static async Task AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            await _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

## <a name="download-a-file"></a>Transferir um ficheiro
O fragmento seguinte mostra um método `DownloadFile` que pode utilizar para transferir um ficheiro de uma conta do Data Lake Store.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
         _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath);
    }

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Referência do SDK .NET do Data Lake Store](https://docs.microsoft.com/dotnet/api/?view=azuremgmtdatalakestore-2.1.0-preview&term=DataLake.Store)
* [Referência do REST do Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)

