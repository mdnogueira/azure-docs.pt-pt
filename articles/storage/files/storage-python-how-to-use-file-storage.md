---
title: Desenvolver para ficheiros do Azure com o Python | Microsoft Docs
description: "Saiba como desenvolver aplicações Python e serviços que utilizam ficheiros do Azure para armazenar dados de ficheiro."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: tamram
ms.openlocfilehash: f4d3d6a7c6b16ca9c6f1ae7e73b57103a5ae4844
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="develop-for-azure-files-with-python"></a>Desenvolver para ficheiros do Azure com o Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Este tutorial irá demonstram as noções básicas da utilização do Python para desenvolver aplicações ou serviços que utilizam ficheiros do Azure para armazenar dados de ficheiro. Neste tutorial, iremos criar uma aplicação de consola simples e mostra como efetuar ações básicas com o Python e ficheiros do Azure:

* Criar partilhas de ficheiros do Azure
* Criar diretórios
* Enumerar ficheiros e diretórios numa partilha de ficheiros do Azure
* Carregar, transferir e eliminar um ficheiro

> [!Note]  
> Porque os ficheiros do Azure podem ser acedidos através de SMB, é possível escrever as aplicações simples que aceder à partilha de ficheiros do Azure utilizando as classes de e/s de Python e funções de padrão. Este artigo descreve como escrever as aplicações que utilizam o SDK Python do armazenamento do Azure, que utiliza o [API REST da Azure ficheiros](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api) para falar com ficheiros do Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Transfira e instale o armazenamento do Azure SDK para Python

SDK de armazenamento do Azure para Python requer o Python 2.7, 3.3, 3.4, 3.5 ou 3.6 e é apresentada no 4 diferentes pacotes: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` e `azure-storage-queue`. Neste tutorial, vamos utilizar `azure-storage-file` pacote.
 
## <a name="install-via-pypi"></a>Instalar através de PyPi

Para instalar através do índice de pacote do Python (PyPI), escreva:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Se estiver a atualizar o armazenamento do Azure SDK para Python versão 0.36 ou anterior, primeiro terá de desinstalar com `pip uninstall azure-storage` como vamos já não lançar o SDK de armazenamento para o Python num pacote único.
> 
> 

Para métodos de instalação alternativa, visite o [SDK de armazenamento do Azure para Python no Github](https://github.com/Azure/azure-storage-python/).

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar os ficheiros do Azure
Adicione o seguinte perto da parte superior de qualquer ficheiro de origem do Python no qual pretende aceder programaticamente ao Storage do Azure.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma ligação para ficheiros do Azure 
O `FileService` objeto permite-lhe trabalhar com partilhas, diretórios e ficheiros. O código seguinte cria um `FileService` utilizando a chave de conta e o nome da conta de armazenamento de objeto. Substitua `<myaccount>` e `<mykey>` com o nome da conta e a chave.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
No exemplo de código seguinte, pode utilizar um `FileService` objeto para criar a partilha se não existir.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Criar um diretório
Também pode organizar armazenamento colocando os ficheiros secundárias diretórios em vez de ter todos eles no diretório de raiz. Ficheiros do Azure permite-lhe criar tantas diretórios como irá permitir a sua conta. O código abaixo, irá criar um subdiretório com o nome **sampledir** sob o diretório de raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros do Azure
Para listar os ficheiros e diretórios numa partilha, utilize o **lista\_diretórios\_e\_ficheiros** método. Este método devolve um gerador de dimensões. O seguinte código saídas de **nome** de cada ficheiro e diretório numa partilha para a consola.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Carregar um ficheiro 
Ficheiros do Azure partilha contém em muito menos, um diretório de raiz, onde podem residir os ficheiros. Nesta secção, irá aprender como carregar um ficheiro a partir do armazenamento local para o diretório de raiz de uma partilha.

Para criar um ficheiro e carregar dados, utilize o `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` ou `create_file_from_text` métodos. Estes são os métodos de alto nível que executam a segmentação necessárias quando o tamanho dos dados exceder 64 MB.

`create_file_from_path`carrega os conteúdos de um ficheiro a partir do caminho especificado e `create_file_from_stream` carrega o conteúdo a partir de um fluxo/ficheiro já está aberto. `create_file_from_bytes`carrega uma matriz de bytes, e `create_file_from_text` carrega o valor de texto especificado, utilizando a codificação especificada (por predefinição UTF-8).

O exemplo seguinte carrega o conteúdo do **sunset.png** de ficheiros para o **myfile** ficheiro.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Transferir um ficheiro
Para transferir dados a partir de um ficheiro, utilize `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, ou `get_file_to_text`. Estes são os métodos de alto nível que executam a segmentação necessárias quando o tamanho dos dados exceder 64 MB.

O exemplo seguinte demonstra a utilização `get_file_to_path` para transferir os conteúdos do **myfile** de ficheiros e armazená-las para o **fora sunset.png** ficheiro.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Por fim, para eliminar um ficheiro, chame `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Criar partilha de um instantâneo (pré-visualização)
Pode criar um ponto em cópia de tempo da sua partilha de ficheiro completo.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar partilha de instantâneo com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Listar partilhas e instantâneos 
Pode listar todos os instantâneos para uma determinada partilha.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procurar partilha instantâneo
Pode procurar conteúdo de cada instantâneo de partilha para obter ficheiros e diretórios a partir desse ponto no tempo.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Obter o ficheiro a partir do instantâneo de partilha
Pode transferir um ficheiro a partir de um instantâneo de partilha para o seu cenário de restauro.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Eliminar um instantâneo de partilha única  
Pode eliminar um instantâneo de partilha única.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Eliminar a partilha quando existem a partilha de instantâneos
Não é possível eliminar uma partilha que contenha instantâneos, a menos que todos os instantâneos são eliminados primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como manipular ficheiros do Azure com o Python, siga estas ligações para saber mais.

* [Centro para Programadores do Python](/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Armazenamento do Microsoft Azure SDK para Python](https://github.com/Azure/azure-storage-python)