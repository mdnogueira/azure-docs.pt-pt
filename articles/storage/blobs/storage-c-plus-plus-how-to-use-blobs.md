---
title: Como utilizar o blob storage (armazenamento de objeto) do C++ | Microsoft Docs
description: "Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos)."
services: storage
documentationcenter: .net
author: MichaelHauss
manager: vamshik
editor: tysonn
ms.assetid: 53844120-1c48-4e2f-8f77-5359ed0147a4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: 9fe2112370f7d29eb0fde856995768660f9871e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="how-to-use-blob-storage-from-c"></a>Como utilizar o Blob Storage do C++
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
O Blob Storage do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos.

Este guia demonstrar como realizar cenários comuns utilizando o serviço de armazenamento de Blobs do Azure. Os exemplos são escritos em C++ e utilize o [biblioteca de clientes do Storage do Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abrangidos incluem **carregar**, **listagem**, **transferir**, e **eliminar** blobs.  

> [!NOTE]
> Este guia destina-se a biblioteca de clientes de armazenamento do Azure para C++ versão 1.0.0 e acima. A versão recomendada é biblioteca de clientes de armazenamento 2.2.0, que está disponível através de [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação do C++
Neste guia, irá utilizar as funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação de C++.  

Para tal, terá de instalar a biblioteca de clientes de armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure na sua subscrição do Azure.   

Para instalar a biblioteca de clientes de armazenamento do Azure para C++, pode utilizar os seguintes métodos:

* **Linux:** siga as instruções indicadas no [biblioteca de clientes do Storage do Azure para o ficheiro Leia-me do C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) página.  
* **Windows:** no Visual Studio, clique em **ferramentas > Gestor de pacotes NuGet > consola do Gestor de pacotes**. Escreva o seguinte comando para o [consola do Gestor de pacotes NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.  
  
     Pacote de instalação wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de BLOBs
Adicione que declarações na parte superior do ficheiro C++ onde pretende que utilizem as APIs de armazenamento do Azure para aceder aos blobs de incluir o seguinte:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Um cliente de armazenamento do Azure utiliza uma cadeia de ligação de armazenamento para armazenar os pontos finais e credenciais para aceder aos serviços de gestão de dados. Quando em execução numa aplicação de cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, com o nome da sua conta de armazenamento e a chave de acesso de armazenamento para a conta do storage listadas no [Portal do Azure](https://portal.azure.com) para o *AccountName* e *AccountKey* valores. Para obter informações sobre as contas do storage e chaves de acesso, consulte [sobre contas de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Este exemplo mostra como podem declarar um campo estático para conter a cadeia de ligação:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar a aplicação no seu computador local do Windows, pode utilizar o Microsoft Azure [emulador do storage](../storage-use-emulator.md) que é instalado com o [Azure SDK](https://azure.microsoft.com/downloads/). O emulador do storage é um utilitário que simula os serviços tabela, fila e Blob disponíveis no Azure no seu computador de desenvolvimento local. O exemplo seguinte mostra como podem declarar um campo estático para conter a cadeia de ligação para o emulador de armazenamento local:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador do storage do Azure, selecione o **iniciar** botão ou prima o **Windows** chave. Começa a escrever **emulador do Storage do Azure**e selecione **emulador do Storage do Microsoft Azure** da lista de aplicações.  

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.  

## <a name="retrieve-your-connection-string"></a>Obter a cadeia de ligação
Pode utilizar o **cloud_storage_account** classe para representar informações da sua conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o **analisar** método.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obter uma referência a um **cloud_blob_client** como permite-lhe obter os objetos que representam a contentores e blobs armazenados no serviço de armazenamento de BLOBs de classe. O código seguinte cria um **cloud_blob_client** objeto utilizando o objeto de conta de armazenamento são obtidas acima:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Como: criar um contentor
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contentor se ainda não existir:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Por predefinição, o novo contentor é privado e tem de especificar a chave de acesso de armazenamento para transferir blobs deste contentor. Se pretender disponibilizar os ficheiros (blobs) dentro do contentor para todos os utilizadores, pode definir o contentor como público utilizando o seguinte código:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Qualquer pessoa na Internet pode ver os blobs num contentor público, mas pode modificar ou eliminá-los apenas se tiver a chave de acesso adequados.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Como: carregar um blob para um contentor
O Blob Storage do Azure suporta blobs de blocos e blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.  

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Depois de ter uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o **upload_from_stream** método. Esta operação irá criar o blob caso não exista, ou substituí-lo se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Em alternativa, pode utilizar o **upload_from_file** método para carregar um ficheiro para um blob de bloco.

## <a name="how-to-list-the-blobs-in-a-container"></a>Como: listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o contentor **list_blobs** método para obter os blobs e/ou os diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um **list_blob_item**, tem de chamar o **list_blob_item.as_blob** método para obter um **cloud_blob** objeto, ou o **list_blob.as_directory** método obter um objeto de cloud_blob_directory. O código seguinte demonstra como obter e apresentar o URI de cada item no **contentor my exemplo** contentor:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Para obter mais detalhes sobre operações de listagem, consulte [lista de recursos de armazenamento do Azure em C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Como: Transferir blobs
Para transferir blobs, obtenha primeiro uma referência de blob e, em seguida, chame o **download_to_stream** método. O exemplo seguinte utiliza o **download_to_stream** método para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode manter num ficheiro local.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Em alternativa, pode utilizar o **download_to_file** método para transferir os conteúdos de um blob para um ficheiro.
Além disso, também pode utilizar o **download_text** método para transferir os conteúdos de um blob como uma cadeia de texto.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Como: eliminar blobs
Para eliminar um blob, obtenha primeiro uma referência de blob e, em seguida, chame o **delete_blob** método.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do blob storage, siga estas ligações para saber mais sobre o Storage do Azure.  

* [Como utilizar o armazenamento de filas do C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Como utilizar o Table Storage do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Recursos de armazenamento do Azure de lista em C++](../storage-c-plus-plus-enumeration.md)
* [Biblioteca de clientes do Storage para referência do C++](http://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Transferir dados com o utilitário de linha de comandos AzCopy](../storage-use-azcopy.md)

