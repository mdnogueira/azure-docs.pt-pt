---
title: Como utilizar o Blob storage do Azure (armazenamento de objeto) do Python | Microsoft Docs
description: "Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos)."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: b94a0f95454f9243ef09ce37a62466bca4003a91
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Como utilizar o Blob storage do Azure do Python
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
O Blob Storage do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos.

Este artigo irá mostrar como efetuar cenários comuns utilizando o Blob storage. Os exemplos são escritos no Python e utilize o [SDK de armazenamento do Microsoft Azure para Python]. Os cenários abrangidos incluem carregar, listar, transferir e eliminar blobs.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Transfira e instale o armazenamento do Azure SDK para Python

SDK de armazenamento do Azure para Python requer o Python 2.7, 3.3, 3.4, 3.5 ou 3.6 e é apresentada no 4 diferentes pacotes: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` e `azure-storage-queue`. Neste tutorial, vamos utilizar `azure-storage-blob` pacote.
 
### <a name="install-via-pypi"></a>Instalar através de PyPi

Para instalar através do índice de pacote do Python (PyPI), escreva:

```bash
pip install azure-storage-blob
```


> [!NOTE]
> Se estiver a atualizar o armazenamento do Azure SDK para Python versão 0.36 ou anterior, primeiro terá de desinstalar com `pip uninstall azure-storage` como vamos já não lançar o SDK de armazenamento para o Python num pacote único.
> 
> 

Para métodos de instalação alternativa, visite o [SDK de armazenamento do Azure para Python no Github](https://github.com/Azure/azure-storage-python/).

## <a name="create-a-container"></a>Criar um contentor
Com base no tipo de blob que pretende utilizar, criar um **BlockBlobService**, **AppendBlobService**, ou **PageBlobService** objeto. O seguinte código utiliza um **BlockBlobService** objeto. Adicione o seguinte perto da parte superior de qualquer ficheiro de Python no qual pretende aceder programaticamente ao armazenamento de BLOBs de blocos do Azure.

```python
from azure.storage.blob import BlockBlobService
```

O código seguinte cria um **BlockBlobService** utilizando a chave de conta e o nome da conta de armazenamento de objeto.  Substitua 'myaccount' e 'mykey' com o nome da conta e a chave.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

No exemplo de código seguinte, pode utilizar um **BlockBlobService** objeto para criar o contentor, se não existir.

```python
block_blob_service.create_container('mycontainer')
```

Por predefinição, o novo contentor é privado, pelo que tem de especificar a chave de acesso de armazenamento (tal como fez anteriormente) para transferir blobs deste contentor. Se pretender disponibilizar os blobs no contentor para todos os utilizadores, pode criar o contentor e passar o nível de acesso público utilizando o seguinte código.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

Em alternativa, pode modificar um contentor depois de criar utilizando o seguinte código.

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

Após esta alteração, qualquer pessoa na Internet pode ver os blobs num contentor público, mas só pode modificar ou eliminá-los.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Para criar um blob de bloco e carregar dados, utilize o **criar\_blob\_de\_caminho**, **criar\_blob\_de\_fluxo**, **criar\_blob\_de\_bytes** ou **criar\_blob\_de\_texto** métodos. Estes são os métodos de alto nível que executam a segmentação necessárias quando o tamanho dos dados exceder 64 MB.

**criar\_blob\_de\_caminho** carrega os conteúdos de um ficheiro a partir do caminho especificado e **criar\_blob\_de\_fluxo** carrega o conteúdo a partir de um fluxo/ficheiro já está aberto. **criar\_blob\_de\_bytes** carrega uma matriz de bytes, e **criar\_blob\_de\_texto** carrega o valor de texto especificado, utilizando a codificação especificada (por predefinição UTF-8).

O exemplo seguinte carrega o conteúdo do **sunset.png** de ficheiros para o **myblockblob** blob.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, utilize o **lista\_blobs** método. Este método devolve um gerador de dimensões. O seguinte código saídas de **nome** de cada blob num contentor para a consola.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Transferir blobs
Para transferir dados de um blob, utilize **obter\_blob\_para\_caminho**, **obter\_blob\_para\_fluxo**, **obter\_blob\_para\_bytes**, ou **obter\_blob\_para\_texto**. Estes são os métodos de alto nível que executam a segmentação necessárias quando o tamanho dos dados exceder 64 MB.

O exemplo seguinte demonstra utilizando **obter\_blob\_para\_caminho** para transferir os conteúdos do **myblockblob** blob e armazene-à **fora sunset.png** ficheiro.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Eliminar um blob
Por fim, para eliminar um blob, chame **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Escrever num blob de acréscimo
Um blob de acréscimo está otimizado para operações de acréscimo, como o registo. Tal como um blob de blocos, um blob de acréscimo é composto por blocos. Porém, quando adiciona um novo bloco a um blob de acréscimo, este é sempre acrescentado ao fim do blob. Não é possível atualizar ou eliminar um bloco existente num blob de acréscimo. O IDs de bloco de um blob de acréscimo não são expostos como acontece num blob de blocos.

Cada bloco num blob de acréscimo pode ter um tamanho diferente, até um máximo de 4 MB, e um blob de acréscimo pode incluir um máximo de 50 000 blocos. Por conseguinte, o tamanho máximo de um blob de acréscimo é ligeiramente superior a 195 GB (4 MB X 50 000 blocos).

O exemplo abaixo cria um novo blob de acréscimo e acrescenta alguns dados ao mesmo, simulando uma operação de registo simples.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do Blob Storage, siga estas ligações para saber mais.

* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Blogue da Equipa de Armazenamento do Azure]
* [SDK de armazenamento do Microsoft Azure para Python]

[Blogue da Equipa de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[SDK de armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python
