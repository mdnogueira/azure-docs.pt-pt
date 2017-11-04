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
ms.date: 07/12/2017
ms.author: tamram
ms.openlocfilehash: 805b0eee46846345ee1f33faf0c28393c3e8ebb1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Alvos de Dimensionamento e Desempenho do Armazenamento do Azure
## <a name="overview"></a>Descrição geral
Este tópico descreve os tópicos de escalabilidade e desempenho para o armazenamento do Microsoft Azure. Para obter um resumo dos outros limites do Azure, consulte [subscrição do Azure e limites de serviço, Quotas e restrições](../../azure-subscription-service-limits.md).

> [!NOTE]
> Todas as contas de armazenamento executam na topologia de rede simples novo e suportam as metas de desempenho e escalabilidade indicadas abaixo, independentemente de quando foram criados. Para obter mais informações sobre a arquitetura de rede simples de armazenamento do Azure e na escalabilidade, consulte [armazenamento do Microsoft Azure: A nuvem armazenamento serviço de elevada disponibilidade com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 
> [!IMPORTANT]
> Os destinos de escalabilidade e desempenho aqui listados são destinos de gama alta de classe, mas são alcançável. Em todos os casos, a taxa de pedidos e largura de banda alcançada através do armazenamento conta depende do tamanho dos objetos armazenados, os padrões de acesso utilizados, e o tipo de carga de trabalho executa a sua aplicação. Lembre-se de que o serviço para determinar se o seu desempenho aos seus requisitos de teste. Se for possível, evitar picos repentino na taxa de tráfego e certifique-se de que o tráfego é distribuído bem em partições.
> 
> Quando a aplicação atinge o limite do que uma partição pode processar a carga de trabalho, do armazenamento do Azure irá começar a devolver o código de erro 503 (servidor ocupado) ou um código de erro 500 respostas de (tempo limite da operação). Se isto ocorre, a aplicação deve utilizar uma política de término exponencial de tentativas. O término exponencial permite que a carga na partição para reduzir e para facilitar a picos de tráfego para esse partição.
> 
> 

Se as necessidades da sua aplicação excedem os destinos de escalabilidade de uma única conta de armazenamento, pode criar a sua aplicação para utilizar várias contas de armazenamento e os objetos de dados de partição entre essas contas de armazenamento. Consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre os preços de volume.

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

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>Objetivos de escalabilidade para discos de máquinas virtuais
[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Consulte [tamanhos de Windows VM](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [tamanhos de VM com Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes adicionais.

## <a name="managed-virtual-machine-disks"></a>Discos de máquinas de virtuais gerido

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>Discos de máquinas de virtuais não geridos
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>Veja Também
* [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
* [Subscrição do Azure e limites de serviço, Quotas e restrições](../../azure-subscription-service-limits.md)
* [Armazenamento Premium: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Máquinas Virtuais do Azure](../../virtual-machines/windows/premium-storage.md)
* [Replicação de armazenamento do Azure](../storage-redundancy.md)
* [Desempenho de armazenamento do Microsoft Azure e a lista de verificação de escalabilidade](../storage-performance-checklist.md)
* [Armazenamento do Microsoft Azure: Um elevada serviço em nuvem armazenamento com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

