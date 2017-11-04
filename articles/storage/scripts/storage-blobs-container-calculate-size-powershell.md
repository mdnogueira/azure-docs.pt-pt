---
title: Script do PowerShell do Azure de exemplo - calcular o tamanho do contentor do blob | Microsoft Docs
description: Calcule o tamanho de um contentor no Blob storage do Azure por totaling o tamanho de cada um dos respetivos blobs.
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
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contentor de armazenamento de BLOBs

Este script calcula o tamanho de um contentor no Blob storage do Azure por totaling o tamanho dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Compreender o tamanho do contentor de Blob storage

Tamanho total do contentor de Blob storage inclui o tamanho do contentor de si próprio e o tamanho de todos os blobs no contentor.

O seguinte descreve como a capacidade de armazenamento é calculada para contentores de BLOBs e Blobs. No abaixo Len(X) significa que o número de carateres da cadeia.

### <a name="blob-containers"></a>Contentores de BLOBs

Segue-se como estimar a quantidade de armazenamento consumida por contentor do blob:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Segue-se a repartição:
* 48 bytes de overhead para cada contentor inclui a hora da última modificação, permissões, definições público e alguns metadados do sistema.
* O nome do contentor é armazenado como Unicode tirar o número de carateres e multiplicar por 2.
* Para cada contentor de blob os metadados armazenados, armazenamos o comprimento do nome (armazenado como ASCII) e o comprimento do valor de cadeia.
* De 512 bytes por assinado identificador inclui o nome do identificador assinado, iniciar a tempo, a hora de expiração e as permissões.

### <a name="blobs"></a>Blobs

Segue-se como estimar a quantidade de armazenamento consumida por BLOBs:

* Blob de bloco (base blob ou um instantâneo)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* Blob de páginas (base blob ou um instantâneo)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

Segue-se a repartição:

* 124 bytes de overhead de blob, que inclui a hora da última modificação, tamanho, a Cache-Control, o tipo de conteúdo, o idioma de conteúdo, codificação de conteúdo, Content-MD5, permissões, instantâneo informações, concessão e alguns metadados do sistema.
* O nome do blob é armazenado como Unicode demorar, por isso, o número de carateres e vários por 2.
* Em seguida, para cada metadados armazenados, o comprimento do nome (armazenado como ASCII) e o comprimento do valor de cadeia.
* Em seguida, para Blobs de blocos
    * 8 bytes para a lista de bloqueios
    * Número de blocos vezes o tamanho de ID do bloco em bytes
    * Além do tamanho dos dados em todos os blocos de consolidada e não consolidados. Tenha em atenção, quando são utilizados os instantâneos, este tamanho inclui apenas os dados exclusivos para este blob base ou de instantâneo. Se os blocos não consolidados não são utilizados após uma semana, estarão libertados e, em seguida, nessa altura já não contabilizará faturação depois disso.
* Em seguida, para Blobs de páginas
    * Número de intervalos de página nonconsecutive com dados vezes 12 bytes. Este é o número de intervalos de página exclusivo que vir ao chamar a API de GetPageRanges.
    * Além do tamanho dos dados de todas as páginas armazenadas em bytes. Tenha em atenção, quando são utilizados instantâneos, este tamanho incluir apenas as páginas exclusivas para o base blob ou a ser contadas de blob de instantâneo.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre faturação de armazenamento do Azure, consulte [compreender o Windows Azure armazenamento faturação](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de armazenamento adicional que podem ser encontrados na [exemplos do PowerShell para o Storage do Azure](../blobs/storage-samples-blobs-powershell.md).
