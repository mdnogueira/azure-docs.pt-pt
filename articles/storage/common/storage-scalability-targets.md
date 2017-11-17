---
title: Metas de desempenho e escalabilidade do Storage do Azure | Microsoft Docs
description: "Saiba mais sobre os destinos de escalabilidade e desempenho para o armazenamento do Azure, incluindo a capacidade, a taxa de pedidos e a largura de banda de entrada e saída para as contas do storage standard e premium. Compreenda metas de desempenho para partições dentro de cada um dos serviços de armazenamento do Azure."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: tamram
ms.openlocfilehash: f62f2020d40e473886cb679cdfe1c164b95f7114
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Alvos de Dimensionamento e Desempenho do Armazenamento do Azure
## <a name="overview"></a>Descrição geral
Este artigo descreve os tópicos de escalabilidade e desempenho para o Storage do Azure. Para obter um resumo dos outros limites do Azure, consulte [subscrição do Azure e limites de serviço, Quotas e restrições](../../azure-subscription-service-limits.md).

> [!NOTE]
> Todas as contas de armazenamento executam na topologia de rede simples novo e suportam as metas de desempenho e escalabilidade descritas neste artigo, independentemente de quando foram criados. Para obter mais informações sobre a arquitetura de rede simples de armazenamento do Azure e na escalabilidade, consulte [armazenamento do Microsoft Azure: A nuvem armazenamento serviço de elevada disponibilidade com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Os destinos de escalabilidade e desempenho aqui listados são destinos de gama alta de classe, mas são alcançável. Em todos os casos, a taxa de pedidos e largura de banda alcançada através do armazenamento conta depende do tamanho dos objetos armazenados, os padrões de acesso utilizados, e o tipo de carga de trabalho executa a sua aplicação. Lembre-se de que o serviço para determinar se o seu desempenho aos seus requisitos de teste. Se for possível, evitar picos repentino na taxa de tráfego e certifique-se de que o tráfego é distribuído bem em partições.
> 
> Quando a aplicação atinge o limite do que uma partição pode processar a carga de trabalho, o Storage do Azure começa a devolver o código de erro 503 (servidor ocupado) ou um código de erro 500 respostas de (tempo limite da operação). Se estes erros estão a ocorrer, a aplicação deve utilizar uma política de término exponencial de tentativas. O término exponencial permite que a carga na partição para reduzir e para facilitar a picos de tráfego para esse partição.
> 
> 

Se as necessidades da sua aplicação excedem os destinos de escalabilidade de uma única conta de armazenamento, pode criar a sua aplicação para utilizar várias contas de armazenamento. Em seguida, pode particionar os objetos de dados entre essas contas de armazenamento. Consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre os preços de volume.

## <a name="scalability-targets-for-a-storage-account"></a>Objetivos de escalabilidade para uma conta de armazenamento
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Destinos de escala de armazenamento de Blobs do Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Destinos de escala de ficheiros do Azure
Para obter mais informações sobre os destinos de dimensionamento e desempenho para ficheiros do Azure e sincronização de ficheiros do Azure, consulte [metas de desempenho e escalabilidade de ficheiros do Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Destinos de escala de sincronização de ficheiros do Azure
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Destinos de escala de armazenamento de filas do Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Destinos de escala de armazenamento de tabela do Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Veja Também
* [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
* [Subscrição do Azure e limites de serviço, Quotas e restrições](../../azure-subscription-service-limits.md)
* [Replicação de armazenamento do Azure](../storage-redundancy.md)
* [Desempenho de armazenamento do Microsoft Azure e a lista de verificação de escalabilidade](../storage-performance-checklist.md)
* [Armazenamento do Microsoft Azure: Um elevada serviço em nuvem armazenamento com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

