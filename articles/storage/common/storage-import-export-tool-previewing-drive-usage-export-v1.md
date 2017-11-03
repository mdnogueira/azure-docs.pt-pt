---
title: "Utilização da unidade de uma tarefa de exportação de importação/exportação do Azure - v1 de pré-visualização | Microsoft Docs"
description: "Saiba como pré-visualize a lista de blobs que selecionou para uma tarefa de exportação no serviço de importação/exportação do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 6ec74ae0b0931f3fed99a43f4f7e58f9d425b138
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Pré-visualização da utilização da unidade para uma tarefa de exportação
Antes de criar uma tarefa de exportação, tem de escolher um conjunto de blobs para ser exportada. O serviço de importação/exportação do Microsoft Azure permite-lhe utilizar uma lista de caminhos de blob ou blob prefixos para representar os blobs que selecionou.  
  
Em seguida, precisa de determinar quantos unidades terá de enviar. A ferramenta de importação/exportação fornece o `PreviewExport` comando para pré-visualizar a utilização da unidade para os blobs que selecionou, com base no tamanho unidades que pretende utilizar.

## <a name="command-line-parameters"></a>Parâmetros da linha de comandos

Pode utilizar os seguintes parâmetros ao utilizar o `PreviewExport` comando da ferramenta de importação/exportação.

|Parâmetro da linha de comandos|Descrição|  
|--------------------------|-----------------|  
|**/LOGDIR:**< LogDirectory\>|Opcional. O diretório de registo. Ficheiros de registo verboso serão escritos neste diretório. Se nenhum diretório de registo for especificado, será utilizado como o diretório de registo do diretório atual.|  
|**/SN:**< StorageAccountName\>|Necessário. O nome da conta de armazenamento para a tarefa de exportação.|  
|**/SK:**< StorageAccountKey\>|Necessário se e apenas se não for especificado um contentor SAS. A chave de conta para a conta de armazenamento para a tarefa de exportação.|  
|**/csas:**< ContainerSas\>|Necessário se e apenas se não for especificada uma chave de conta do storage. O contentor de SAS para listar os blobs para ser exportado na tarefa de exportação.|  
|**/ ExportBlobListFile:**< ExportBlobListFile\>|Necessário. Caminho para o XML do ficheiro que contém lista de caminhos de blob ou blob prefixos de caminho para os blobs ser exportada. O formato de ficheiro utilizado no `BlobListBlobPath` elemento o [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operação da REST API do serviço de importação/exportação.|  
|**/ DriveSize:**< DriveSize\>|Necessário. O tamanho das unidades a utilizar para uma tarefa de exportação, *por exemplo,*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Exemplo de linha de comandos

O exemplo seguinte demonstra a `PreviewExport` comando:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
O ficheiro de lista de BLOBs de exportação pode conter nomes de BLOBs e BLOBs prefixos, conforme mostrado aqui:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

A ferramenta de importação/exportação do Azure apresenta uma lista de todos os blobs para ser exportada e calcula como pacote-los para unidades de tamanho especificado, tendo em conta quaisquer necessário overhead, em seguida, calcula o número de unidades necessário para armazenar os blobs e informações de utilização da unidade.  
  
Eis um exemplo de saída, com os registos informativas omitido:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Passos seguintes

* [Referência da ferramenta de importação/exportação do Azure](../storage-import-export-tool-how-to-v1.md)
