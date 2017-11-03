---
title: Azure metas de desempenho e escalabilidade de ficheiros | Microsoft Docs
description: "Saiba mais sobre as metas de desempenho e escalabilidade para ficheiros do Azure, incluindo a capacidade, a taxa de pedidos e limites de largura de banda de entrada e saída."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/17/2017
ms.author: wgries
ms.openlocfilehash: 35d430b683224d1035cccdfb58b9db415d47ea3b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure metas de desempenho e escalabilidade de ficheiros
[Ficheiros do Azure](storage-files-introduction.md) oferece completamente geridos partilhas de ficheiros na nuvem que estão acessíveis através do protocolo SMB padrão da indústria. Este artigo aborda os destinos de desempenho e escalabilidade para ficheiros do Azure e sincronização de ficheiros do Azure (pré-visualização).

Os destinos de escalabilidade e desempenho aqui listados são destinos de gama alta de classe, mas podem ser afetados por outras variáveis na sua implementação. Por exemplo, o débito para um ficheiro poderá também ser limitado pela sua largura de banda disponível, não apenas os servidores que alojam o serviço de ficheiros do Azure. Recomendamos vivamente que teste o seu padrão de utilização para determinar se a escalabilidade e o desempenho dos ficheiros de Azure satisfazer os seus requisitos. Podemos também empenhados em aumentar estes limites ao longo do tempo. . Não hesitam em fornecer-nos comentários, ou nos comentários abaixo ou no [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre os limites gostaria de ver-na aumentar.

## <a name="azure-storage-account-scale-targets"></a>Destinos de escala de conta de armazenamento do Azure
O recurso principal para uma partilha de ficheiros do Azure é uma conta de armazenamento do Azure. Uma conta de armazenamento representa um agrupamento de armazenamento no Azure que pode ser utilizado por vários serviços de armazenamento, incluindo ficheiros do Azure, para armazenar dados. Outros serviços que armazenam dados em contas do storage são Blob storage do Azure, o armazenamento de filas do Azure e o Table storage do Azure. Os seguintes destinos de aplicam a todos os serviços de armazenamento armazenar dados numa conta do storage:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Utilização da conta de armazenamento de outros serviços de armazenamento afeta as partilhas de ficheiros do Azure na sua conta de armazenamento. Por exemplo, se atingir a capacidade de conta de armazenamento máximo Blob Storage do Azure, não será possível criar novos ficheiros na partilha de ficheiros do Azure, mesmo que a partilha de ficheiros do Azure está abaixo do tamanho máximo de partilha.

## <a name="azure-files-scale-targets"></a>Destinos de escala de ficheiros do Azure
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Destinos de escala de sincronização de ficheiros do Azure
Sincronização de ficheiros do Azure, ter tentámos quanto possível para a estrutura para utilização ilimitada, no entanto, isto não é sempre possível. A tabela abaixo indica os limites do nosso teste e os destinos são os limites fixos realmente:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>Consultar também
- [Planear uma implementação de ficheiros do Azure](storage-files-planning.md)
- [Planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md)
- [Metas de desempenho e escalabilidade para outros serviços de armazenamento](../common/storage-scalability-targets.md)