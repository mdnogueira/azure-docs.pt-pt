---
title: Como utilizar o Blob storage (armazenamento de objeto) do Ruby | Microsoft Docs
description: "Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos)."
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 2c1534dcbb0e26ecdff7c057efb5094c60b5c5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-ruby"></a>Como utilizar o Blob Storage do Ruby
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
O Blob Storage do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos.

Este guia irá mostrar como efetuar cenários comuns utilizando o Blob storage. Os exemplos são escritos utilizando a API de Ruby. Os cenários abrangidos incluem **carregar, listar, transferir,** e **eliminar** blobs.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Crie uma aplicação Ruby. Para obter instruções, consulte [Ruby na aplicação Rails Web numa VM do Azure](../../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Configurar a sua aplicação para aceder ao armazenamento
Para utilizar o Storage do Azure, terá de transferir e utilizar o pacote do azure Ruby, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST do storage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Escreva "azure de instalação gem" na janela de comando para instalar o gem e dependências.

### <a name="import-the-package"></a>Importar o pacote
Utilizar o editor de texto favorito, adicione o seguinte na parte superior do ficheiro Ruby em que pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do azure irá ler as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_ACCESS_KEY** de informações necessárias para ligar à sua conta de armazenamento do Azure. Se estas variáveis de ambiente não estiver definidas, tem de especificar as informações de conta antes de utilizar **Azure::Blob::BlobService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Para obter estes valores a partir de um clássico ou conta de armazenamento do Resource Manager no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. No painel definições à direita, clique em **chaves de acesso**.
4. No painel de chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer um destes.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## <a name="create-a-container"></a>Criar um contentor
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

O **Azure::Blob::BlobService** objeto permite-lhe trabalhar com blobs e contentores. Para criar um contentor, utilize o **criar\_container()** método.

Exemplo de código seguinte cria um contentor ou imprima o erro se existir alguma.

```ruby
azure_blob_service = Azure::Blob::BlobService.new
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

Se pretender que os ficheiros no contentor público, pode definir permissões do contentor.

Apenas pode modificar o <strong>criar\_container()</strong> chamada para passar o **: pública\_acesso\_nível** opção:

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

Os valores válidos para o **: pública\_acesso\_nível** opção são:

* **blob:** Especifica público acesso de leitura de blobs. Dados de BLOBs dentro deste contentor podem ser lidos através do pedido anónimo, mas não estão disponíveis dados de contentor. Os clientes não é possível enumerar os blobs no contentor através do pedido anónimo.
* **contentor:** Especifica completa público acesso de leitura de dados de blob e o contentor. Os clientes podem enumerar os blobs no contentor através do pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.

Em alternativa, pode modificar o nível de acesso público de um contentor utilizando **definir\_contentor\_acl()** método para especificar o nível de acesso público.

Exemplo de código seguinte altera o nível de acesso público ao **contentor**:

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Para carregar conteúdo para um blob, utilize o **criar\_bloco\_blob()** método para criar o blob, utilize um ficheiro ou uma cadeia como o conteúdo do blob.

O seguinte código carrega o ficheiro **test.png** como um novo blob com o nome "-blob de imagem" no contentor.

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os contentores, utilize **list_containers()** método.
Para listar os blobs num contentor, utilize **lista\_blobs()** método.

Isto produz os urls de todos os blobs em todos os contentores para a conta.

```ruby
containers = azure_blob_service.list_containers()
containers.each do |container|
    blobs = azure_blob_service.list_blobs(container.name)
    blobs.each do |blob|
    puts blob.name
    end
end
```

## <a name="download-blobs"></a>Transferir blobs
Para transferir blobs, utilize o **obter\_blob()** método para obter os conteúdos.

Exemplo de código seguinte demonstra utilizando **obter\_blob()** para transferir os conteúdos de "-blob de imagem" e escrevem-para um ficheiro local.

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>Eliminar um Blob
Por fim, para eliminar um blob, utilize o **eliminar\_blob()** método. Exemplo de código seguinte demonstra como eliminar um blob.

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as tarefas de armazenamento mais complexas, siga estas ligações:

* [Blogue da Equipa de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Azure SDK para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) repositório no GitHub
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

