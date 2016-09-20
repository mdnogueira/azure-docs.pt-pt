<properties
   pageTitle="Utilizar o SDK .NET do Data Lake Store para desenvolver aplicações | Azure"
   description="Utilizar o SDK .NET do Azure Data Lake Store para desenvolver aplicações"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# Introdução ao Azure Data Lake Store com SDK .NET

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a utilizar o [SDK .NET do Azure Data Lake Store ](https://msdn.microsoft.com/library/mt581387.aspx) para criar uma conta do Azure Data Lake e executar operações básicas, como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

## Pré-requisitos

* Visual Studio 2013 ou 2015. As instruções abaixo utilizam o Visual Studio 2015.
* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Criar uma Aplicação do Azure Active Directory**. Existem duas formas para se autenticar com o Azure Active Directory - **interativa** e **não interativa**. Existem pré-requisitos diferentes com base na forma como se pretende autenticar.
    * **Para a autenticação interativa** (utilizada neste artigo) - No Azure Active Directory, tem de criar uma **aplicação de Cliente Nativo**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
        - Obtenha o **ID de cliente** e o **URI de redirecionamento** para a aplicação
        - Definir permissões delegadas

    * **Para a autenticação não interativa** - No Azure Active Directory, tem de criar uma **aplicação Web**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
        - Obtenha o **ID de cliente**, o **segredo de cliente** e o **URI de redirecionamento** para a aplicação
        - Definir permissões delegadas
        - Atribua a aplicação do Azure Active Directory a uma função. A função pode estar ao nível do âmbito a que pretende dar permissão à aplicação do Azure Active Directory. Por exemplo, pode atribuir a aplicação ao nível da subscrição ou ao nível de um grupo de recursos. 

    Veja [Criar aplicação do Active Directory e um principal de serviço com o portal](../resource-group-create-service-principal-portal.md) para obter instruções sobre como obter estes valores, definir as permissões e atribuir funções.

## Criar uma aplicação .NET

1. Abra o Visual Studio e crie uma aplicação de consola.

2. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**.

3. A partir de **Novo Projeto**, escreva ou selecione os seguintes valores:

  	| Propriedade | Valor                       |
  	|----------|-----------------------------|
  	| Categoria | Templates/Visual C#/Windows |
  	| Modelo | Aplicação de Consola         |
  	| Nome     | CreateADLApplication        |

4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes Nuget ao seu projeto.

    1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
    2. No separador **Gestor de Pacotes Nuget**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
    3. Procure e instale os seguintes pacotes do Data Lake Store:

        * `Microsoft.Azure.Management.DataLake.Store`
        * `Microsoft.Azure.Management.DataLake.StoreUploader`

        ![Adicionar uma origem Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Create a new Azure Data Lake account")

    4. Instale também o pacote `Microsoft.IdentityModel.Clients.ActiveDirectory` para a autenticação do Azure Active Directory. Certifique-se de que *desmarca* a caixa de verificação **Incluir pré-lançamento** para poder instalar uma versão estável deste pacote.

        ![Adicionar uma origem Nuget](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Create a new Azure Data Lake account")


    5. Feche o **Gestor de Pacotes NuGet**.

7. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

8. Declare as variáveis, conforme mostrado abaixo, e forneça os valores para o nome do Data Lake Store e o nome do grupo de recursos. O nome do Data Lake Store fornecido será criado pela aplicação. O grupo de recursos fornecido aqui já deve existir. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos aqui existem no computador. Adicione o fragmento de código seguinte após as declarações de espaço de nomes.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como autenticar utilizadores, criar a conta do Data Lake Store, carregar ficheiros, etc. Se estiver à procura de um exemplo completo sobre como utilizar o Data Lake Store, veja o [Apêndice](#appendix-sample-code) na parte inferior deste artigo.

## Autenticar o utilizador

Existem duas formas de autenticar com o Azure Active Directory:

* **Interativo**, em que um utilizador inicia sessão utilizando a aplicação. Está implementado no método `AuthenticateUser` no fragmento de código abaixo.

* **Não interativo**, em que a aplicação fornece as suas próprias credenciais. Está implementado no método `AuthenticateAppliaction` no fragmento de código abaixo.

### Autenticação interativa

O fragmento seguinte mostra um método `AuthenticateUser` que pode utilizar para um registo interativo na experiência.

    // Authenticate the user with AAD through an interactive popup.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
    
        var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
            PromptBehavior.Auto, UserIdentifier.AnyUser);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }

### Autenticação não interativa

O fragmento seguinte mostra um método `AuthenticateApplication` que pode utilizar para um registo não interativo na experiência.

    // Authenticate the application with AAD through the application's secret key.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
        var credential = new ClientCredential(appClientId, clientSecret);
    
        var tokenAuthResult = authContext.AcquireToken(resource, credential);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }
    
## Criar uma conta do Data Lake Store

O fragmento seguinte mostra um método `CreateAccount` que pode utilizar para criar uma conta do Data Lake Store.

    // Create Data Lake Store account
    public static void CreateAccount()
    {
        var adlsParameters = new DataLakeStoreAccount(location: _location);
        _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
    } 

## Listar todas as contas do Data Lake Store numa subscrição

O fragmento seguinte mostra o método `ListAdlStoreAccounts` que pode utilizar para listar todas as contas do Data Lake Store numa determinada subscrição do Azure.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccount>(response);
    
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
    
        return accounts;
    }

## Criar um diretório

O fragmento seguinte mostra um método `CreateDirectory` que pode utilizar para criar um diretório numa conta do Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## Carregar um ficheiro para o Data Lake Store

O fragmento seguinte mostra um método `UploadFile` que pode utilizar para carregar ficheiros para uma conta do Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

O DataLakeStoreUploader suporta carregamento e transferência recursivos entre um caminho de ficheiro (ou pasta) local para o Data Lake Store.    

## Obter as informações do ficheiro ou diretório

O fragmento seguinte mostra um método `GetItemInfo` que pode utilizar para obter informações sobre um ficheiro ou diretório disponível no Data Lake Store. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## Listar o ficheiro ou os diretórios

O fragmento seguinte mostra um método `ListItem` que pode utilizar para listar o ficheiro e os diretórios numa conta do Data Lake Store.
    
    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## Concatenar ficheiros

O fragmento seguinte mostra um método `ConcatenateFiles` que pode utilizar para concatenar ficheiros. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## Acrescentar a um ficheiro

O fragmento seguinte mostra um método `AppendToFile` que pode utilizar para acrescentar dados a um ficheiro já armazenado numa conta do Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## Transferir um ficheiro

O fragmento seguinte mostra um método `DownloadFile` que pode utilizar para transferir um ficheiro de uma conta do Data Lake Store.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);

        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## Eliminar uma conta do Data Lake Store

O fragmento seguinte mostra um método `DeleteAccount` que pode utilizar para eliminar uma conta do Data Lake Store.

    // Delete account
    public static void DeleteAccount()
    {
        _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
    }

## Apêndice: código de exemplo

O fragmento seguinte é um exemplo de código abrangente que pode copiar e colar na sua aplicação para ver uma operação ponto a ponto no Data Lake Store. Antes de executar o fragmento de código, certifique-se de que fornece os valores necessários, como o nome do Data Lake Store, o nome do grupo de recursos, etc. Também tem de fornecer os valores necessários para a autenticação do Azure Active Directory, como **\<APPLICATION-CLIENT-ID>** , **\<APPLICATION-REPLY-URI>** e **\<SUBSCRIPTION-ID>**.

Embora o fragmento de código abaixo forneça métodos para ambas as abordagens, interativa e não interativa, o bloco de código não interativo é comentado. O método interativo requer que forneça o ID de cliente da aplicação AAD e o URI de redirecionamento. A ligação no pré-requisito fornece instruções sobre como obtê-los.

>[AZURE.NOTE] Se pretender modificar o fragmento de código e utilizar antes o método não interativo (`AuthenticateApplication`), tem de fornecer também a chave de autenticação de cliente, além do ID de cliente e do URI de resposta de cliente, como entradas para o método. O artigo [Criar a aplicação Active Directory e um principal de serviço com o portal](../resource-group-create-service-principal-portal.md) também fornece informações sobre como gerar e obter a chave de autenticação de cliente.
    
Por fim, certifique-se de que o caminho local e o nome de ficheiro fornecidos aqui existem no computador. Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).



    using System;
    using System.IO;
    using System.Security;
    using System.Text;
    using System.Collections.Generic;
    using System.Linq;

    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;

    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                _location = "East US 2";

                string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                string remoteFolderPath = "/data_lake_path/";
                string remoteFilePath = remoteFolderPath + "file.txt";

                // Authenticate the user
                var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                    "<APPLICATION-CLIENT-ID>", new Uri("<APPLICATION-REPLY-URI>")); // TODO: Replace bracketed values.

                SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                // Run sample scenarios
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Creating a directory.");

                // Create a directory in the Data Lake Store
                CreateDirectory(remoteFolderPath);
                WaitForNewline("Directory created.", "Showing directory info.");

                // Get info about the directory in the Data Lake Store
                var itemInfo = GetItemInfo(remoteFolderPath);
                Console.WriteLine("Type: " + itemInfo.Type);
                Console.WriteLine("Last modified (UTC): " +
                                  new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                      itemInfo.ModificationTime.Value));
                WaitForNewline("Directory info shown.", "Uploading a file.");

                // Upload a file to the Data Lake Store
                UploadFile(localFilePath, remoteFilePath);
                WaitForNewline("File uploaded.", "Listing files and directories.");

                // List the files in the Data Lake Store
                var itemList = ListItems(remoteFolderPath);
                var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                Console.WriteLine(String.Join("\r\n", fileMenuItems));
                WaitForNewline("Files and directories listed.", "Appending content to a file.");

                // Append to a file in the Data Lake Store
                AppendToFile(remoteFilePath, "123");
                WaitForNewline("Content appended.", "Downloading a file.");

                // Download a file from the Data Lake Store
                DownloadFile(remoteFilePath, localFilePath);
                WaitForNewline("File downloaded.", "Deleting account.");

                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted. You can now exit.");
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                if (!String.IsNullOrWhiteSpace(nextAction))
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                    Console.WriteLine(nextAction);
                }
                else
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                }
            }

            // Authenticate the user with AAD through an interactive popup.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                    PromptBehavior.Auto, UserIdentifier.AnyUser);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            
            /*
            // Authenticate the application with AAD through the application's secret key.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                var credential = new ClientCredential(appClientId, clientSecret);

                var tokenAuthResult = authContext.AcquireToken(resource, credential);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            */

            //Set up clients
            public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
            {
                _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                _adlsClient.SubscriptionId = subscriptionId;

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            // Create account
            public static void CreateAccount()
            {
                // Create ADLS account
                var adlsParameters = new DataLakeStoreAccount(location: _location);
                _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }

            // Delete account
            public static void DeleteAccount()
            {
                _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
            }

            // List all ADLS accounts within the subscription
            public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
            {
                var response = _adlsClient.Account.List(_adlsAccountName);
                var accounts = new List<DataLakeStoreAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlsClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }

            // Upload a file
            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            // Concatenate files
            public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
            {
                _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(string path)
            {
                return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(string directoryPath)
            {
                return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
            }

            // Download file
            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Append to file
            public static void AppendToFile(string path, string content)
            {
                var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
            }

            // Create a directory
            public static void CreateDirectory(string path)
            {
                _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
            }
        }
    }


## Passos seguintes

- [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)
- [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referência do SDK .NET do Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referência do REST do Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)



<!--HONumber=sep16_HO2-->


