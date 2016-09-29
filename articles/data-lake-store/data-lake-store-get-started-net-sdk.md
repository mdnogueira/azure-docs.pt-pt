<properties
   pageTitle="Utilizar o SDK .NET do Data Lake Store para desenvolver aplicações | Microsoft Azure"
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
   ms.date="09/15/2016"
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

Saiba como utilizar o [SDK. NET do Azure Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx) para executar operações básicas, como criar pastas, carregar e transferir ficheiros de dados, etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

## Pré-requisitos

* **Visual Studio 2013 ou 2015**. As instruções abaixo utilizam o Visual Studio 2015.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure Data Lake Store**. Para obter instruções sobre como criar uma conta, veja [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **Crie uma Aplicação do Azure Active Directory** se pretender uma autenticação automática da sua aplicação com o Azure Active Directory.

    * **Para uma autenticação não interativa do serviço principal** – No Azure Active Directory, deve criar uma **Aplicação Web**. Assim que tiver criado a aplicação, obtenha os seguintes valores relacionados com a aplicação.
        - Obtenha o **ID de cliente** e **segredo de cliente** da aplicação
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
    3. Procure e instale os seguintes pacotes NuGet:

        * `Microsoft.Azure.Management.DataLake.Store` - Este tutorial utiliza a v0.12.5-preview.
        * `Microsoft.Azure.Management.DataLake.StoreUploader` - Este tutorial utiliza a v0.10.6-preview.
        * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial utiliza a v2.2.8-preview.

        ![Adicionar uma origem Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Create a new Azure Data Lake account")

    4. Feche o **Gestor de Pacotes NuGet**.

6. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

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
                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como a autenticação, carregamento de ficheiros, etc.

## Autenticação

O fragmento seguinte pode ser utilizado para um início de sessão interativo.

    // User login via interactive popup
    //    Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"))
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Em alternativa, o fragmento seguinte pode ser utilizado para autenticar a aplicação de forma não interativa através do segredo do cliente/chave de uma aplicação/serviço principal.

    // Service principal / appplication authentication with client secret / key
    //    Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-clientid>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

Como terceira opção, o fragmento seguinte pode ser utilizado para autenticar a aplicação de forma não interativa através do certificado de uma aplicação/serviço principal.

    // Service principal / application authentication with certificate
    //    Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## Criar objetos de cliente

O fragmento seguinte cria a conta do Data Lake Store e os objetos de cliente do sistema de ficheiros, utilizados para emitir pedidos ao serviço.

    // Create client objects
    var fileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## Listar todas as contas do Data Lake Store numa subscrição

O fragmento seguinte lista todas as contas do Data Lake Store numa determinada subscrição do Azure.

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

## Carregar um ficheiro

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

## Passos seguintes

- [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)
- [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referência do SDK .NET do Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referência do REST do Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)



<!--HONumber=Sep16_HO3-->


