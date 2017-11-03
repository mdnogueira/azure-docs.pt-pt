---
title: Desenvolver para os ficheiros do Azure com C++ | Microsoft Docs
description: "Saiba como desenvolver aplicações C++ e serviços que utilizam ficheiros do Azure para armazenar dados de ficheiro."
services: storage
documentationcenter: .net
author: renashahmsft
manager: aungoo
editor: tysonn
ms.assetid: a1e8c99e-47a6-43a9-9541-c9262eb00b38
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renashahmsft
ms.openlocfilehash: d2f55b5ca6348ba8e190c65ec9a72c6f730d869e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-files-with-c"></a>Desenvolver para os ficheiros do Azure com C++
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial

Neste tutorial, irá aprender a executar operações básicas em ficheiros do Azure. Através de amostras de escrita em C++, irá aprender a criar diretórios e partilhas, carregar, listar e eliminar ficheiros. Se estiver familiarizado com ficheiros do Azure, será útil compreender os exemplos percorrer os conceitos das secções que se seguem.


* Criar e eliminar as partilhas de ficheiros do Azure
* Criar e eliminar diretórios
* Enumerar ficheiros e diretórios numa partilha de ficheiros do Azure
* Carregar, transferir e eliminar um ficheiro
* Definir a quota (tamanho máximo) para uma partilha de ficheiros do Azure
* Criar uma assinatura de acesso partilhado (chave SAS) para um ficheiro que utiliza uma política de acesso partilhado definida na partilha.

> [!Note]  
> Porque os ficheiros do Azure podem ser acedidos através de SMB, é possível escrever as aplicações simples que aceder à partilha de ficheiros do Azure utilizando as classes de e/s de C++ e funções de padrão. Este artigo descreve como escrever as aplicações que utilizam o SDK de C++ de armazenamento do Azure, que utiliza o [API de REST do ficheiro](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) para falar com ficheiros do Azure.

## <a name="create-a-c-application"></a>Criar uma aplicação do C++
Para criar os exemplos, terá de instalar a biblioteca de clientes do Storage do Azure 2.4.0 para C++. Deve também criou uma conta de armazenamento do Azure.

Para instalar o cliente de armazenamento do Azure 2.4.0 C++, pode utilizar um dos seguintes métodos:

* **Linux:** siga as instruções indicadas no [biblioteca de clientes do Storage do Azure para o ficheiro Leia-me do C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) página.
* **Windows:** no Visual Studio, clique em **ferramentas &gt; Gestor de pacotes NuGet &gt; consola do Gestor de pacotes**. Escreva o seguinte comando para o [consola do Gestor de pacotes NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar os ficheiros do Azure
Adicione que declarações na parte superior do ficheiro de origem C++ para manipular ficheiros do Azure onde pretende de incluir o seguinte:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Para utilizar o File storage, tem de ligar à sua conta do storage do Azure. É o primeiro passo configurar uma cadeia de ligação, que iremos utilizar para ligar à sua conta de armazenamento. Vamos definir uma variável estática fazê-lo.

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Ligar a uma conta de armazenamento do Azure
Pode utilizar o **cloud_storage_account** classe para representar informações da sua conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o **analisar** método.

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Todos os ficheiros e diretórios numa partilha de ficheiros de Azure residem num contentor chamado um **partilhar**. A conta do storage pode ter partilhas tantos como permite a capacidade de conta. Para obter acesso a uma partilha e o respetivo conteúdo, tem de utilizar um cliente de ficheiros do Azure.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

Utilizando o cliente de ficheiros do Azure, em seguida, pode obter uma referência a uma partilha.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Para criar a partilha, utilize o **create_if_not_exists** método o **cloud_file_share** objeto.

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

Neste momento, **partilhar** contém uma referência a uma partilha denominada **partilha my exemplo**.

## <a name="delete-an-azure-file-share"></a>Eliminar uma partilha de ficheiros do Azure
Eliminar uma partilha é feito chamando a **delete_if_exists** método num objeto cloud_file_share. Eis o código de exemplo que suporta que.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Criar um diretório
Pode organizar armazenamento colocando os ficheiros subdiretórios em vez de ter todos eles no diretório de raiz. Ficheiros do Azure permite-lhe criar tantas diretórios como irá permitir a sua conta. O código abaixo, irá criar um diretório com o nome **diretório my exemplo** sob o diretório de raiz, bem como um subdiretório com o nome **subdiretório my exemplo**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory = 
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Eliminar um diretório
Eliminar um diretório é uma tarefa simple, embora deve ser salientado que não é possível eliminar um diretório que contiver ficheiros ou outros diretórios.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory = 
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros do Azure
Obter uma lista de ficheiros e diretórios dentro de uma partilha facilmente é feito chamando **list_files_and_directories** num **cloud_file_directory** referência. Para aceder ao conjunto avançado de propriedades e métodos de um **list_file_and_directory_item**, tem de chamar o **list_file_and_directory_item.as_file** método para obter um **cloud_file** objeto, ou o **list_file_and_directory_item.as_directory** método para obter um **cloud_file_directory** objeto.

O código seguinte demonstra como obter e apresentar o URI de cada item no diretório de raiz da partilha.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_diretory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }        
}
```

## <a name="upload-a-file"></a>Carregar um ficheiro
Em muito menos, uma partilha de ficheiros de Azure contém um diretório de raiz, onde podem residir os ficheiros. Nesta secção, irá aprender como carregar um ficheiro a partir do armazenamento local para o diretório de raiz de uma partilha.

O primeiro passo no carregamento de um ficheiro é para obter uma referência para o diretório onde deve residir. Fazê-lo ao chamar o **get_root_directory_reference** método do objeto de partilha.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Agora que tem uma referência para o diretório de raiz da partilha, pode carregar um ficheiro no mesmo. Neste exemplo carrega a partir de um ficheiro, a partir de texto e de um fluxo.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream = 
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));    
```

## <a name="download-a-file"></a>Transferir um ficheiro
Para transferir os ficheiros, obtenha primeiro uma referência de ficheiro e, em seguida, chame o **download_to_stream** método para transferir os conteúdos do ficheiro para um objeto de fluxo que, em seguida, pode manter num ficheiro local. Em alternativa, pode utilizar o **download_to_file** método para transferir os conteúdos de um ficheiro para um ficheiro local. Pode utilizar o **download_text** método para transferir os conteúdos de um ficheiro como uma cadeia de texto.

O exemplo seguinte utiliza o **download_to_stream** e **download_text** métodos para demonstrar a transferir os ficheiros que foram criados nas secções anteriores.

```cpp
// Download as text
azure::storage::cloud_file text_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Há outra operação de ficheiros do Azure comum é a eliminação de ficheiros. O seguinte código elimina um ficheiro denominado my-exemplo-ficheiro-3 armazenados no diretório de raiz.

```cpp
// Get a reference to the root directory for the share.    
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

azure::storage::cloud_file file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Definir a quota (tamanho máximo) para uma partilha de ficheiros do Azure
Pode definir a quota (ou o tamanho máximo) para uma partilha de ficheiros, em gigabytes. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

Ao definir a quota para uma partilha, pode limitar o tamanho total dos ficheiros armazenados na partilha. Se o tamanho total dos ficheiros na partilha exceder a quota definida na partilha, os clientes não poderão aumentar o tamanho dos ficheiros existentes ou criar novos ficheiros, a menos que esses ficheiros estejam vazios.

O exemplo abaixo mostra como verificar a utilização atual para uma partilha e como configurar a quota para a partilha.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso partilhado para um ficheiro ou partilha de ficheiros
Pode gerar uma assinatura de acesso partilhado (SAS) para uma partilha de ficheiros ou para um ficheiro individual. De igual modo, pode criar uma política de acesso partilhado numa partilha de ficheiros para gerir assinaturas de acesso partilhado. Recomenda-se a criação de uma política de acesso partilhado, uma vez que fornece um meio de revogação do SAS se este estiver comprometido.

O exemplo seguinte cria uma política de acesso partilhado numa partilha e, em seguida, utiliza essa política para fornecer as restrições para um SAS num ficheiro dentro da partilha.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy = 
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() + 
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;    

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir = 
        share.get_root_directory_reference();
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share 
    //  and associate this access policy with it.        
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.        
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");        
    file_with_sas.upload_text(text);        

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();        
    ucout << downloaded_text << std::endl;        
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```
## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Storage do Azure, explore estes recursos:

* [Biblioteca de Clientes de Armazenamento para C++](https://github.com/Azure/azure-storage-cpp)
* [Storage do azure exemplos de serviço de ficheiros em C++] (https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Explorador do Armazenamento do Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)