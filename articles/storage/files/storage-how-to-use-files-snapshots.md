---
title: "Trabalhar com instantâneos de partilha (pré-visualização) | Microsoft Docs"
description: "Um instantâneo de partilha é uma versão só de leitura de uma partilha de ficheiros do Azure que é executada num ponto no tempo, como uma forma de fazer cópias de segurança da partilha."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 5212866bda9ff775d32ebb57874b3d58e11f1eb3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="work-with-share-snapshots-preview"></a>Trabalhar com instantâneos de partilha (pré-visualização)
Um instantâneo de partilha (pré-visualização) é uma versão só de leitura de uma partilha de ficheiros do Azure que é executada num ponto no tempo. Depois de criar um instantâneo de partilha,-lo pode ser ler, copiar, ou eliminada, mas não modificado. Um instantâneo de partilha fornece uma forma de fazer cópias de segurança a partilha como é apresentado um momento. 

Neste artigo, irá aprender a criar, gerir e elimine os instantâneos de partilha. Para obter mais informações, consulte o [partilhar descrição geral de instantâneo](storage-snapshots-files.md) ou [instantâneo FAQ](storage-files-faq.md).

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de partilha

Pode criar um instantâneo de partilha ao utilizar o portal do Azure, PowerShell, CLI do Azure, a API REST ou qualquer SDK de armazenamento do Azure. As secções seguintes descrevem como criar um instantâneo de partilha, utilizando o portal, CLI e PowerShell. 

Pode tirar um instantâneo de partilha de uma partilha de ficheiros enquanto esta se encontra em utilização. No entanto, os instantâneos de partilha capturam apenas os dados que já foi escritos para uma partilha de ficheiros no momento em que o comando de instantâneo de partilha é emitido. Isto pode excluir todos os dados que tem foi colocado em cache por quaisquer aplicações ou o sistema operativo.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>Criar um instantâneo de partilha através do portal  
Para criar um instantâneo de partilha do ponto no tempo, vá para o ficheiro de partilha no portal e selecione **criar um instantâneo**.

>   ![Menu de instantâneo no portal](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Criar um instantâneo de partilha utilizando o Azure CLI 2.0
Pode criar um instantâneo de partilha, utilizando o `az storage share snapshot` comando:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Exemplo de saída:
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>Criar um instantâneo de partilha utilizando o PowerShell
Pode criar um instantâneo de partilha, utilizando o `$share.Snapshot()` comando:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>Efetuar operações de instantâneo de partilha comuns

Pode enumerar os instantâneos de partilha associados com a partilha de ficheiros utilizando o **versões anteriores** separador no Windows, através de REST, a biblioteca de clientes, PowerShell e o portal. Depois da partilha de ficheiros está montada, pode ver todas as versões anteriores do ficheiro utilizando o **versões anteriores** separador no Windows. 

As secções seguintes descrevem como utilizar o portal do Azure, o Windows e o Azure CLI 2.0 para listar, navegue para e restaure instantâneos de partilha.

### <a name="share-snapshot-operations-in-the-portal"></a>Operações de instantâneo de partilha no portal

Pode observar todos os instantâneos a partilha de um ficheiro partilham no portal e navegue para um instantâneo de partilha para ver o respetivo conteúdo.

#### <a name="view-a-share-snapshot"></a>Ver um instantâneo de partilha
Numa partilha de ficheiros, em **instantâneo**, selecione **ver instantâneos**.

![Comando "Ver instantâneos" no portal](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>Lista e navegue para a partilha de conteúdo de instantâneo
Ver a lista de instantâneos de partilha e, em seguida, navegue para o conteúdo de um instantâneo diretamente selecionando o carimbo de hora pretendida.

![Instantâneo selecionado na lista de carimbos de data / hora](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Também pode selecionar o **Connect** botão na sua lista de instantâneo vista para obter o `net use` comando e o caminho do diretório para um instantâneo de uma partilha específica. Pode, em seguida, diretamente procurar nesse instantâneo.


![Ligar o painel com caixa de comando](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>Transferir ou restaurar a partir de um instantâneo de partilha
No portal, transferir ou restaurar um ficheiro a partir de um instantâneo, utilizando o **transferir** ou **restaurar** botão, respetivamente.

![Botões de restauro e transferência](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Operações de instantâneo de partilha no Windows
Quando já tiver criado instantâneos de partilha de partilha de ficheiros, pode ver as versões anteriores de uma partilha, um diretório ou um ficheiro específico da sua partilha de ficheiros instalado no Windows. Por exemplo, eis como pode utilizar a funcionalidade de versões anteriores para ver e restaurar uma versão anterior de um diretório do Windows.

> [!Note]  
> Pode efetuar as mesmas operações no nível de partilha e o nível de ficheiro. Apenas a versão que contém as alterações para esse ficheiro ou diretório é apresentada na lista. Se um ficheiro ou diretório particular não mudou entre duas de partilha de instantâneos, o instantâneo de partilha é apresentado na lista de versão anterior do nível da partilha, mas não se encontra na lista de versão anterior do diretório ou do ficheiro.

#### <a name="mount-a-file-share"></a>Montar uma partilha de ficheiros
Em primeiro lugar, montar a partilha de ficheiros utilizando o `net use` comando.

#### <a name="open-a-mounted-share-in-file-explorer"></a>Abra uma partilha montada no Explorador de ficheiros
Aceda ao Explorador de ficheiros e localize a partilha montada.

![Partilha montada no Explorador de ficheiros](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Lista de versões anteriores
Navegue para o item ou item de principal tem de ser restaurados. Faça duplo clique para ir para o diretório pretendido. Clique com botão direito e selecione **propriedades** no menu.

![Clique no menu de para um diretório selecionado](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Selecione **versões anteriores** para ver a lista de partilha de instantâneos para este diretório. A lista poderá demorar alguns segundos a carregar, consoante a velocidade da rede e o número de instantâneos de partilha no diretório.

![Separador de versões anterior](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Pode selecionar **abrir** para abrir um instantâneo específico. 

![Abrir instantâneo](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar a partir de uma versão anterior
Selecione **restaurar** para copiar o conteúdo do recursivamente diretório de todo o momento de criação do instantâneo de partilha para a localização original.
 ![Restaurar botão na mensagem de aviso](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Operações de instantâneo de partilha no Azure CLI 2.0
Pode utilizar o Azure CLI 2.0 para efetuar operações como instantâneos de partilha de listagem, navegar para partilhar conteúdo de instantâneo, restaurar ou partilham de transferir ficheiros de instantâneos de partilha ou eliminar instantâneos.

#### <a name="list-share-snapshots"></a>Instantâneos de partilha de lista

Pode listar os instantâneos de uma determinada partilha utilizando [ `az storage share list` ](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) com `--include-snapshots`:

```azurecli-interactive 
az storage share list --include-snapshots
```

O comando fornece uma lista de instantâneos de partilha, juntamente com todas as propriedades associadas. O resultado seguinte é um exemplo:

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>Navegue para um instantâneo de partilha
Pode procurar um instantâneo de uma partilha específica e ver o respetivo conteúdo utilizando [ `az storage file list` ](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Especifique o nome de partilha e o carimbo de hora a que pretende procurar, conforme mostrado no exemplo seguinte:

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

Na saída, verá que o conteúdo do instantâneo de partilha é idêntico ao conteúdo da partilha num momento de criação de instantâneo essa partilha:

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>Restaurar a partir de um instantâneo de partilha

Pode restaurar um ficheiro através da cópia ou transferindo-a partir do instantâneo de partilha. Utilize o `az storage file download` comando, conforme mostrado no exemplo seguinte:

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

Na saída, verá que o conteúdo do ficheiro transferido e as respetivas propriedades é idêntico para o conteúdo e propriedades num momento que partilham o instantâneo foi criada:

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha

Pode eliminar os instantâneos de partilha ao utilizar o portal do Azure, PowerShell, CLI, a API REST ou qualquer SDK de armazenamento. As secções seguintes descrevem como eliminar os instantâneos de partilha utilizando o portal do Azure, CLI e PowerShell.

Pode navegar para partilhar instantâneos e ver as diferenças entre dois instantâneos utilizando qualquer ferramenta de comparação. Em seguida, pode determinar o instantâneo de partilha que pretende eliminar. 

Não é possível eliminar uma partilha que tenha uma partilha de instantâneo. Para poder eliminar a partilha, tem de eliminar primeiro todos os respetivos instantâneos de partilha.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>Eliminar um instantâneo de partilha através do portal  
No portal, aceda ao painel da partilha de ficheiros e utilizar o **eliminar** botão para eliminar instantâneos de partilha de um ou mais.

>   ![Eliminar botão](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Eliminar um instantâneo de partilha utilizando o Azure CLI 2.0
Pode eliminar um instantâneo de partilha utilizando o `[az storage share delete]` comando. Utilizar o carimbo de hora do instantâneo de partilha para o `--snapshot '2017-10-04T23:28:35.0000000Z' ` parâmetro no exemplo seguinte:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Exemplo de saída:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>Eliminar um instantâneo de partilha utilizando o PowerShell
Pode eliminar um instantâneo de partilha utilizando o `Remove-AzureStorageShare -Share` comando:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral de instantâneo](storage-snapshots-files.md)
* [Instantâneo FAQ](storage-files-faq.md)
