---
title: "Executar operações no Blob storage do Azure (armazenamento de objetos) com o PowerShell | Microsoft Docs"
description: "Tutorial - executar operações no Blob storage do Azure (armazenamento de objetos) com o PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Efetuar operações de armazenamento de Blobs do Azure com o Azure PowerShell

O Blob Storage do Azure é um serviço para armazenar grandes quantidades de dados de objetos não estruturados, como texto ou dados binários, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Este artigo abrange operações básicas no Blob storage do Azure, tais como o carregamento, transferir e eliminar blobs. Saiba como:

> [!div class="checklist"]
> * Criar um contentor 
> * Carregar blobs
> * Listar os blobs num contentor 
> * Transferir blobs
> * Blobs de cópia
> * Eliminar blobs
> * Ver e defina os metadados e propriedades de um blob
> * Gerir a segurança através de assinaturas de acesso partilhado

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contentor

Os BLOBs sempre são carregados num contentor. Contentores são semelhantes aos diretórios no seu computador, permitindo-lhe organizar grupos de blobs contentores como organizar os seus ficheiros em pastas no seu computador. Uma conta de armazenamento pode ter qualquer número de contentores; apenas é limitada pela quantidade de espaço efetuada cópias de segurança na conta de armazenamento (até 500TB). 

Quando cria um contentor, pode definir o nível de acesso, o que ajuda a definir quem pode aceder a blobs no contentor. Por exemplo, pode ser privados (nível de acesso = `Off`), que significa que ninguém pode aceder aos mesmos sem uma assinatura de acesso partilhado ou as chaves de acesso para a conta de armazenamento. Se não especificar o nível de acesso ao criar o contentor, assume como privada.

Poderá pretender que as imagens no contentor de estar acessível publicamente. Por exemplo, se pretender armazenar imagens para serem apresentados no seu Web site, que pretende ser público. Neste caso, defina o acesso do contentor para `blob`, e qualquer pessoa com um URL que aponta para um blob no contentor pode aceder a BLOBs.

Para criar o contentor, defina o nome do contentor, em seguida, criar o contentor, definir as permissões para 'BLOBs'. Os nomes de contentor tem de começar com uma letra ou um número e só podem conter letras, números e o caráter de hífen (-). Para mais regras sobre a nomenclatura de blobs e contentores, consulte [nomenclatura e referência de contentores, Blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Criar um novo contentor com [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Defina o nível de acesso público. O nome do contentor neste exemplo é *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Carregar os blobs num contentor

Armazenamento de Blobs do Azure suporta blobs de blocos, blobs de páginas e blobs de acréscimo.  Ficheiros VHD utilizados para efetuar cópias de VMs de IaaS são blobs de páginas. Acrescentar blobs são utilizados para registo, por exemplo, se pretender escrever um ficheiro e, em seguida, mantenha a adição de mais informações. A maioria dos ficheiros armazenados no Blob storage são blobs de blocos. 

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor, em seguida, obtenha uma referência para o blob de bloco no contentor. Assim que tiver a referência de blob, pode carregar dados para a mesma utilizando [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Esta operação cria o blob, se não existir ou substitui-lo se já existir.

O seguinte mostra como carregar um blob para um contentor. Em primeiro lugar, definir variáveis que apontam para o diretório no computador local onde estão localizados os ficheiros e definir uma variável para o nome do ficheiro a ser carregado. Esta ação é útil quando pretender executar repetidamente a mesma operação. Carregar alguns dos ficheiros para que possa ver várias entradas quando listar os blobs no contentor.

Os exemplos seguintes Image001.jpg e carga Image002.png d:\\_TestImages no disco local para o contentor que acabou de criar.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Carregar ficheiro noutro. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Carregar ficheiros tantos como pretender antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obter uma lista de blobs no contentor com [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) e selecionar o nome do blob que será apresentado.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Transferir blobs

Transferir os blobs para o seu disco local. Em primeiro lugar, defina uma variável que aponta para a pasta local para o qual pretende transferir os blobs. Em seguida, para cada blob ser transferidos, defina o nome e a chamada [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) para transferir o blob.

Neste exemplo copia os blobs para d:\\_TestImages\Downloads no disco local. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Blobs de cópia

Existem vários aspetos a considerar ao copiar os blobs. O blob já foi ser apenas copiar para um novo nome na mesma localização. Foi possível copiar o blob para uma conta de armazenamento separada. Foi possível copiar um blob de grandes dimensões (por exemplo, um ficheiro VHD) para uma conta de armazenamento diferente. Estes seriam cada ser feitas diferente.

### <a name="simple-blob-copy"></a>Copiar blob simples

Pode efetuar uma cópia de um dos blobs num contentor ao copiá-los para um blob de novo. Neste exemplo será copie-o para o mesmo contentor com um nome diferente, mas pode tal como facilmente criar outro contentor e copie-não existe em vez disso. Este exemplo mostra como utilizar [início AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) para copiar o blob. Tem de especificar a origem e nome do blob de destino e nome do contentor.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Copiar um blob para uma conta de armazenamento diferente 

Poderá copiar um blob para uma conta de armazenamento separada. Um exemplo para fazê-lo é ao copiar um ficheiro VHD que efetua uma cópia de uma das suas VMs para uma conta de armazenamento diferentes para cópia de segurança. 

Configurar uma segunda conta de armazenamento, obter o contexto, configurar um contentor nessa conta de armazenamento e efetuar a cópia. Esta parte do script é quase idêntico ao script acima, exceto para utilizar a segunda conta de armazenamento em vez do primeiro.

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Copiar blobs muito grande de forma assíncrona

Se estiver a copiar um blob muito grande de vários GB, pode tirar partido da cópia assíncrona ao iniciá-la e, em seguida, regressam e verificar o estado até que esteja concluída. Desta forma, poderá não está associadas enquanto está a efetuar a operação de cópia.

Se estiver a copiar dentro de uma conta de armazenamento, a cópia é muito rápida. Se estiver a copiar entre duas contas de armazenamento na mesma região, a cópia é pretty rápida. No entanto, se a origem e de destino estiverem em regiões separadas, pode demorar várias horas a concluir a cópia, consoante a distância e o tamanho do ficheiro. 

Este script utiliza as mesmas variáveis definidas no exemplo a última cópia. A diferença é que este captura o estado da cópia e consultas repetidamente cada 10 segundos até que esteja concluída. Poderá ter de carregar um blob muito grande para a sua conta de armazenamento para ver este demorar mais do que uma iteração para concluir.

Utilize [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) para consultar o estado da cópia. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Eliminar blobs

Para remover blobs a partir de uma conta de armazenamento, utilize [remover AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Ler e escrever propriedades de um blob e metadados

Para aceder ao conjunto de propriedades e métodos de um **IListBlobItem**, tem de o transmitir (ou convertê-la) para um **CloudBlockBlob**, **CloudPageBlob**, ou **CloudBlobDirectory** objeto. Se o tipo for desconhecido, pode utilizar uma verificação de tipo para determinar para qual este deve ser transmitido. O código seguinte demonstra como obter e apresentar as propriedades de um dos blobs carregadas anteriormente utilizando [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) e conversão de resultados para um objeto de CloudBlockBlob.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Preencher as propriedades do sistema ao chamar FetchAttributes, em seguida, ver essas propriedades. Algumas das propriedades são graváveis e pode alterar os respetivos valores. Este exemplo mostra como alterar o tipo de conteúdo, também conhecido como o tipo de MIME.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Cada blob tem os suas próprias metadados que pode definir. Por exemplo, pode armazenar o nome do utilizador que carregou o blob ou a data/hora-lo primeiro foi carregado. Os metadados é constituído por pares chave/valor. Isto pode ser modificado utilizando o PowerShell da seguinte forma.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Gerir a segurança dos blobs 

Por predefinição, o armazenamento de Azure mantém os seus dados seguros ao limitar o acesso ao proprietário da conta, que está na posse das chaves de acesso de conta de armazenamento. Quando precisar de partilhar dados de blobs na sua conta do Storage, é importante que o faça sem comprometer a segurança das suas chaves de acesso da conta. Para tal, pode utilizar o URL de assinatura de um acesso partilhado, que é um URL para o elemento que inclui os parâmetros de consulta e um token de segurança que permite que um nível específico de permissão para um período de tempo específico. Por exemplo, poderá pretende conceder acesso de leitura para um blob privado durante 5 minutos para que alguém pode vê-la. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Definir o nível de acesso de contentor e os respetivos blobs privada

Em primeiro lugar, defina o nível de acesso do contentor para `Off`, que designa que não há nenhum acesso sem uma assinatura de acesso partilhado ou a chave de conta. Utilize o [conjunto AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Testar o acesso privado

Para verificar se tem sem acesso para os blobs no contentor, construir o URL de um dos blobs sem uma assinatura de acesso partilhado e tente ver o blob. Utilizando o protocolo HTTPS, o URL será no seguinte formato:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Isto mostra como criar o URL do blob.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Copie o URL de blob e cole-o uma janela do browser privada – esta operação irá mostrar um erro de autorização, uma vez que o blob é privado e não tenha incluído uma assinatura de acesso partilhado. 

### <a name="create-the-sas-uri"></a>Crie o URI de SAS

Criar um URI de SAS – esta é a ligação para o blob, incluindo os parâmetros de consulta e o token de segurança que compõem a SAS. Cole este URI uma janela de navegação privada – esta operação irá mostrar a imagem. 

Crie primeiro a data/hora de início e a data/hora de expiração para o acesso. Esta opção utiliza uma janela de 2 minutos. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Crie o URI de SAS utilizando [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). É necessário o nome do contentor, nome do blob, contexto da conta de armazenamento, as permissões para ser concedida (neste caso, ler, escrita e eliminação) e a hora de início e de fim para o acesso. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Copie o URI de SAS e coloque-o numa janela do browser privada; Esta operação irá mostrar a imagem.

Aguarde algum tempo suficiente para o URL expirar (2 minutos, neste exemplo), em seguida, cole o URL de outra janela do browser privada. Este tempo, obterá um erro de autorização e -não mostra a imagem, porque o URI de SAS expirou.

## <a name="clean-up-resources"></a>Limpar recursos

Remova todos os recursos que criou. Para tal, pode remover o grupo de recursos, o que também elimina todos os recursos contidos dentro do grupo. Neste caso, remove todas as contas de armazenamento e o grupo de recursos em si.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a gestão de armazenamento de BLOBs básico tais como:

> [!div class="checklist"]
> * Criar um contentor 
> * Carregar blobs
> * Listar os blobs num contentor 
> * Transferir blobs
> * Blobs de cópia
> * Eliminar blobs
> * Ler e escrever os metadados e as propriedades de um blob
> * Gerir a segurança através de assinaturas de acesso partilhado

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de armazenamento do Microsoft Azure PowerShell
* [Cmdlets do PowerShell do armazenamento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure
* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.