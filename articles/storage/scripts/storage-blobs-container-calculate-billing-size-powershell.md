---
title: "Script do PowerShell do Azure de exemplo - calcular o tamanho total de faturação de um contentor de blob | Microsoft Docs"
description: "Calcule o tamanho total de um contentor no Blob storage do Azure para fins de faturação."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: a9d7cc69fbbd037a553e877ca9c26d84c376ccc0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Calcular o tamanho total de faturação de um contentor de blob

Este script calcula o tamanho de um contentor no Blob storage do Azure para fins de estimar os custos de faturação. O script totais o tamanho dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Este script do PowerShell calcula o tamanho de um contentor para fins de faturação. Se estiver a calcular o tamanho do contentor para outros fins, consulte o artigo [calcular o tamanho total de um contentor de armazenamento de BLOBs](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script mais simples que fornece uma estimativa.

## <a name="determine-the-size-of-the-blob-container"></a>Determinar o tamanho do contentor do blob

O tamanho total do contentor do blob inclui o tamanho do contentor próprio e o tamanho de todos os blobs no contentor.

As secções seguintes descreve a forma como a capacidade de armazenamento é calculada para contentores de BLOBs e blobs. Na secção seguinte, Len(X) significa que o número de carateres da cadeia.

### <a name="blob-containers"></a>Contentores de BLOBs

O cálculo seguinte descreve como estimar a quantidade de armazenamento é consumido por contentor do blob:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Segue-se a repartição:
* 48 bytes de overhead para cada contentor inclui a hora da última modificação, permissões, definições público e alguns metadados do sistema.

* O nome do contentor é armazenado como Unicode, por isso, coloque o número de carateres e multiplicar por dois.

* Para cada bloco de metadados do contentor de blob que é armazenado, armazenamos o comprimento do nome (ASCII), juntamente com o comprimento do valor de cadeia.

* Os 512 bytes por assinado identificador inclui o nome do identificador assinado, hora de início, hora de expiração e as permissões.

### <a name="blobs"></a>Blobs

Os cálculos seguintes mostram como estimar a quantidade de armazenamento consumida por BLOBs.

* Blob de bloco (base blob ou um instantâneo):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* Blob de páginas (base blob ou um instantâneo):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

Segue-se a repartição:

* 124 bytes de overhead de blob, que inclui:
    - Hora da última modificação
    - Tamanho
    - Cache-Control
    - Tipo de conteúdo
    - Idioma de conteúdo
    - Codificação de conteúdo
    - Content-MD5
    - Permissões
    - Informações de instantâneo
    - Concessão
    - Alguns metadados do sistema

* O nome do blob é armazenado como Unicode, por isso, coloque o número de carateres e multiplicar por dois.

* Para cada bloco de metadados que é armazenado, adicione o comprimento do nome (armazenado como ASCII), juntamente com o comprimento do valor de cadeia.

* Para os blobs de blocos:
    * 8 bytes para a lista de bloqueios.
    * Número de blocos vezes o tamanho de ID do bloco em bytes.
    * O tamanho dos dados em todos os blocos consolidados e não consolidados. 
    
    >[!NOTE]
    >Quando são utilizados os instantâneos, este tamanho inclui apenas os dados exclusivos para este blob base ou de instantâneo. Se os blocos não consolidados não são utilizados após uma semana, que são recolhidos de libertação da memória. Depois disso, não contagem na direção de faturação.

* Para blobs de páginas:
    * O número de intervalos de página nonconsecutive com dados vezes 12 bytes. Este é o número de intervalos de página exclusivo vir ao chamar o **GetPageRanges** API.

    * O tamanho dos dados de todas as páginas armazenadas em bytes. 
    
    >[!NOTE]
    >Quando são utilizados os instantâneos, este tamanho inclui apenas as páginas exclusivas para o blob base ou o blob de instantâneo que está a ser contado.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Passos seguintes

- Consulte [calcular o tamanho total de um contentor de armazenamento de BLOBs](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script simple que fornece uma estimativa do tamanho do contentor.

- Para obter mais informações sobre faturação de armazenamento do Azure, consulte [compreender o Windows Azure armazenamento faturação](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Para mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.4.1).

- Pode encontrar exemplos de script do PowerShell de armazenamento adicionais no [exemplos do PowerShell para o Storage do Azure](../blobs/storage-samples-blobs-powershell.md).
