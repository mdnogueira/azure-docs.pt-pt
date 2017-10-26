---
title: "SDK .NET: operações do sistema de ficheiros no Azure Data Lake Store | Microsoft Docs"
description: "Utilize o SDK .NET do Azure Data Lake Store para executar operações do sistema de ficheiros no Data Lake Store, como criar pastas, etc."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 7f6319dcf1ae66a686dd1c2ea2810b3041183098
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Operações do sistema de ficheiros no Azure Data Lake Store com o SDK .NET
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [python](data-lake-store-data-operations-python.md)
>
>

Neste artigo, aprenderá a efetuar operações do sistema de ficheiros no Data Lake Store com o SDK .NET. As operações do sistema de ficheiros incluem criar pastas numa conta do Data Lake Store, carregar ficheiros, transferir ficheiros, etc.

Para obter instruções sobre como efetuar operações de gestão de contas no Data Lake Store com o SDK .NET, veja [Operações de gestão de contas no Data Lake Store com o SDK .NET](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo utilizam o Visual Studio 2017.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure Data Lake Store**. Para obter instruções sobre como criar uma conta, veja [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

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
5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do rato no nome do projeto no Explorador de Soluções e clique em **Gerir Pacotes NuGet**.
   2. No separador **Gestor de Pacotes NuGet**, certifique-se de que a **Origem do pacote** está definida como **nuget.org** e que a caixa de verificação **Incluir pré-lançamento** está selecionada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - Este tutorial utiliza a v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial utiliza a v2.2.12.

        ![Adicionar uma origem NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gestor de Pacotes NuGet**.
6. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Declare as variáveis conforme mostrado abaixo e forneça os valores para os marcadores de posição. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos aqui existem no computador.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como a autenticação, carregamento de ficheiros, etc.

## <a name="authentication"></a>Autenticação

* Para a autenticação de utilizador final na sua aplicação, veja [Autenticação de utilizador final com o Data Lake Store com o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para a autenticação serviço a serviço na sua aplicação, veja [Autenticação serviço a serviço com o Data Lake Store com o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-objects"></a>Criar objetos de cliente
O fragmento seguinte cria a conta do Data Lake Store e os objetos de cliente do sistema de ficheiros, utilizados para emitir pedidos ao serviço.

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

## <a name="create-a-directory"></a>Criar um diretório
Adicione o método seguinte à sua classe. O fragmento mostra um método `CreateDirectory()` que pode utilizar para criar um diretório numa conta do Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

Adicione o seguinte fragmento ao método `Main()` para invocar o método `CreateDirectory()`.

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>Carregar um ficheiro
Adicione o método seguinte à sua classe. O fragmento mostra um método `UploadFile()` que pode utilizar para carregar ficheiros para uma conta do Data Lake Store. O SDK suporta carregamento e transferência recursivos entre um caminho de ficheiro local e um caminho de ficheiro do Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Adicione o seguinte fragmento ao método `Main()` para invocar o método `UploadFile()`.

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>Obter as informações do ficheiro ou diretório
O fragmento seguinte mostra um método `GetItemInfo()` que pode utilizar para obter informações sobre um ficheiro ou diretório disponível no Data Lake Store.

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

Adicione o seguinte fragmento ao método `Main()` para invocar o método `GetItemInfo()`.

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>Listar o ficheiro ou os diretórios
O fragmento seguinte mostra um método `ListItems()` que pode utilizar para listar o ficheiro e os diretórios numa conta do Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

Adicione o seguinte fragmento ao método `Main()` para invocar o método `ListItems()`.

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>Concatenar ficheiros
O fragmento seguinte mostra um método `ConcatenateFiles()` que pode utilizar para concatenar ficheiros.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

Adicione o seguinte fragmento ao método `Main()` para invocar o método `ConcatenateFiles()`. Este fragmento parte do princípio que carregou outro ficheiro para a conta do Data Lake Store e que o caminho do ficheiro é fornecido na cadeia *anotherRemoteFilePath*.

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro
O fragmento seguinte mostra um método `AppendToFile()` que pode utilizar para acrescentar dados a um ficheiro já armazenado numa conta do Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

Adicione o seguinte fragmento ao método `Main()` para invocar o método `AppendToFile()`.

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>Transferir um ficheiro
O fragmento seguinte mostra um método `DownloadFile()` que pode utilizar para transferir um ficheiro de uma conta do Data Lake Store.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

Adicione o seguinte fragmento ao método `Main()` para invocar o método `DownloadFile()`.

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>Consultar também
* [Operações de gestão de contas no Data Lake Store com o SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Referência do SDK .NET do Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
