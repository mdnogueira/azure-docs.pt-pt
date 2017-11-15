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
ms.date: 11/02/2017
ms.author: nitinme
ms.openlocfilehash: a5d446986f810993d65c7e73eb95eeb2283c39a3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
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
O exemplo de código disponível no [GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) explica-lhe o processo de criação de ficheiros no arquivo, de concatenação de ficheiros, de transferência de um ficheiro e de eliminação de alguns ficheiros do arquivo. Esta secção do artigo explica-lhe as partes principais do código.

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

      * `Microsoft.Azure.DataLake.Store` - este tutorial utiliza a v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial utiliza a v2.3.1.
    
    Feche o **Gestor de Pacotes NuGet**.

6. Abra **Program.cs**, elimine o código existente e, em seguida, inclua as seguintes instruções para adicionar referências aos espaços de nomes.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Declare as variáveis conforme mostrado abaixo e forneça os valores para os marcadores de posição. Além disso, certifique-se de que o caminho local e o nome de ficheiro fornecidos aqui existem no computador.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; //Replace this value with the name of your existing Data Lake Store account.        
            }
        }

Nas restantes secções do artigo, pode ver como utilizar os métodos .NET disponíveis para executar operações, como a autenticação, carregamento de ficheiros, etc.

## <a name="authentication"></a>Autenticação

* Para a autenticação de utilizador final na sua aplicação, veja [Autenticação de utilizador final com o Data Lake Store com o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para a autenticação serviço a serviço na sua aplicação, veja [Autenticação serviço a serviço com o Data Lake Store com o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Criar o objeto de cliente
O fragmento seguinte cria o objeto de cliente filesystem do Data Lake Store, que é utilizado para emitir pedidos ao serviço.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Criar um ficheiro e um diretório
Adicione o fragmento seguinte à sua aplicação. Este fragmento adiciona um ficheiro, bem como qualquer diretório principal que não exista.

    // Create a file - automatically creates any parent directories that don't exist
    
    string fileName = "/Test/testFilename1.txt";
    using (var streamWriter = new StreamWriter(client.CreateFile(fileName, IfExists.Overwrite)))
    {
        streamWriter.WriteLine("This is test data to write");
        streamWriter.WriteLine("This is line 2");
    }

## <a name="append-to-a-file"></a>Acrescentar a um ficheiro
O fragmento seguinte liga dados a um ficheiro já existente na conta do Data Lake Store.

    // Append to existing file
    using (var streamWriter = new StreamWriter(client.GetAppendStream(fileName)))
    {
        streamWriter.WriteLine("This is the added line");
    }

## <a name="read-a-file"></a>Ler um ficheiro
O fragmento seguinte lê os conteúdos de ficheiros no Data Lake Store.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Obter as propriedades do ficheiro
O fragmento seguinte devolve as propriedades associadas a um ficheiro ou diretório.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

A definição do método `PrintDirectoryEntry` está disponível como parte do exemplo [no Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro
O fragmento seguinte muda o nome de um ficheiro já existente numa conta do Data Lake Store.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Enumerar um diretório
O fragmento seguinte enumera os diretórios numa conta do Data Lake Store.

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

A definição do método `PrintDirectoryEntry` está disponível como parte do exemplo [no Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Eliminar diretórios recursivamente
O fragmento seguinte elimina um diretório e todos os respetivos sub-diretórios, recursivamente.

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Amostras
Seguem-se alguns exemplos da utilização do SDK Filesystem do Data Lake Store.
* [Exemplo básico no Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Exemplo avançado no Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Consultar também
* [Operações de gestão de contas no Data Lake Store com o SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Referência do SDK .NET do Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
