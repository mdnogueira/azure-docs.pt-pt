---
title: "Guia de introdução do Azure - objetos de transferência da Blob storage do Azure com o Python | Microsoft Docs"
description: "Saiba rapidamente a transferência de objetos do Blob storage do Azure com o Python"
services: storage
documentationcenter: storage
author: ruthogunnnaike
manager: cwatson
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 76e23d85b392f8120914f6170040c6b3c450aba6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Transferência de objetos para/de Blob storage do Azure com o Python
Este guia de introdução, irá aprender a utilizar o Python para carregar, transfira e lista de blobs de blocos num contentor de Blob storage do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido: 
* Instalar [Python](https://www.python.org/downloads/)
* Transfira e instale [SDK de armazenamento do Azure para Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo
O [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) utilizado neste início rápido é uma aplicação básica do Python.  

Utilize [git](https://git-scm.com/) para transferir uma cópia da aplicação para o ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Este comando clones o repositório para a pasta de local git. Para abrir o programa de Python, procure o armazenamento-blobs-python-guia de introdução, ficheiros e pastas example.py.  

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
Na aplicação, tem de fornecer a chave de conta e o nome conta de armazenamento para criar um `BlockBlobService` objeto. Abra o `example.py` ficheiro a partir do Explorador de soluções do seu IDE. Substitua o **accountname** e **accountkey** valores com o nome da conta e a chave. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Executar o exemplo
Este exemplo cria um ficheiro de teste na pasta 'Documentos'. O programa de exemplo carrega o ficheiro de teste para o Blob storage, apresenta uma lista de blobs no contentor e transfere o ficheiro com um novo nome. 

Execute o exemplo. O resultado seguinte é um exemplo do resultado devolvido ao executar a aplicação:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quando prima qualquer tecla para continuar, o programa de exemplo elimina o contentor de armazenamento e os ficheiros. Antes de continuar, verifique a pasta de 'Documentos' para os dois ficheiros. Pode abrir e ver que se são idênticos.

Também pode utilizar uma ferramenta como o [Explorador de armazenamento do Azure](http://storageexplorer.com) para ver os ficheiros no Blob storage. Explorador de armazenamento do Azure é uma ferramenta de plataforma livre que lhe permite aceder às suas informações de conta de armazenamento. 

Depois de verificar que os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que faz o exemplo, abra o ficheiro de example.py para ver o código. 

## <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento
A primeira coisa a fazer é criar as referências a objectos utilizados para aceder e gerir o armazenamento de Blobs. Criar estes objetos entre si, e cada um é utilizada pela seguinte na lista.

* Instanciar o **BlockBlobService** objeto, que aponta para o serviço Blob na sua conta de armazenamento. 

* Instanciar o **CloudBlobContainer** objeto que representa o contentor que está a aceder. Contentores são utilizados para organizar os blobs como utilizar as pastas no seu computador para organizar os seus ficheiros.

Assim que tiver o contentor de Blob de nuvem, pode instanciar o **CloudBlockBlob** objeto que aponta para o blob específico no qual está interessado e efetuar operações como carregamento, a transferência e a cópia.

> [!IMPORTANT]
> Os nomes de contentor tem de ser em minúsculas. Consulte [nomenclatura e referência de contentores, Blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) para obter mais informações sobre os nomes de contentor e BLOBs.

Nesta secção, instanciar os objetos, criar um novo contentor e, em seguida, definir as permissões no contentor para que os blobs públicos. O contentor é designado por **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
## <a name="upload-blobs-to-the-container"></a>Carregar os blobs no contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são frequentemente utilizadas e que é o que é utilizado neste guia de introdução.  

Para carregar um ficheiro para um blob, obtenha o caminho completo do ficheiro ao associar o nome do diretório e o nome de ficheiro no disco local. Em seguida, pode carregar o ficheiro para o caminho especificado, utilizando o **criar\_blob\_de\_caminho** método. 

O código de exemplo cria um ficheiro local para ser utilizado para o carregamento e transferência, armazenar o ficheiro a carregar como **ficheiro\_caminho\_para\_ficheiro** e o nome do blob como **local\_ficheiro\_nome**. O exemplo seguinte carrega o ficheiro para o contentor denominado **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Existem vários métodos de carregamento que pode utilizar com o Blob storage. Por exemplo, se tiver uma sequência de memória, pode utilizar o **criar\_blob\_de\_fluxo** método em vez do **criar\_blob\_de\_caminho**. 

Os blobs de blocos podem ser igual 4.7 TB e podem ser qualquer coisa de folhas de cálculo do Excel para ficheiros de vídeos grandes. Os blobs de páginas são utilizados principalmente para os ficheiros VHD utilizados para fazer uma cópia de VMs de IaaS. Acrescentar blobs são utilizados para registo, por exemplo, se pretender escrever um ficheiro e, em seguida, mantenha a adição de mais informações. A maioria dos objetos armazenados no Blob storage são blobs de blocos.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obter uma lista de ficheiros no contentor com o **list_blobs** método. Este método devolve um gerador de dimensões. O código seguinte obtém a lista de blobs, em seguida, repetido ao longo, que mostra os nomes dos blobs encontrados num contentor.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

## <a name="download-the-blobs"></a>Transferir os blobs

Transferir blobs para a sua utilização de disco local da **obter\_blob\_para\_caminho** método. O código seguinte transfere o blob carregado numa secção anterior. "_DOWNLOADED" é adicionado como um sufixo no nome do blob para que possa ver ambos os ficheiros no disco local. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

## <a name="clean-up-resources"></a>Limpar recursos
Se já não necessita de blobs carregados este início rápido, pode eliminar o contentor inteiro utilizando o **eliminar\_contentor**. Se os ficheiros criados já não são necessárias, utilize o **eliminar\_blob** método para eliminar os ficheiros.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>Passos seguintes
 
Este guia de introdução, aprendeu como transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure com o Python. Para obter mais informações sobre como trabalhar com armazenamento de BLOBs, continue para o Blob storage procedimentos.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de BLOBs](./storage-python-how-to-use-blob-storage.md)
 

Para obter mais informações sobre o Explorador de armazenamento e Blobs, consulte [recursos de armazenamento de Blobs do Azure de gerir com o Explorador de armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
