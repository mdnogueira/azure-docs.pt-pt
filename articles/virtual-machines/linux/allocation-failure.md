---
title: "Resolução de problemas de falhas de atribuição de VM com Linux | Microsoft Docs"
description: "Resolver problemas de falhas de alocação quando criar, reiniciar ou redimensionar uma VM com Linux no Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 8cf421afe9b41af050152dc965d42e7809b5f2d9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Resolver problemas de falhas de alocação quando criar, reiniciar ou redimensionar VMs com Linux no Azure
Quando criar uma VM, reiniciar VMs (desalocadas) paradas ou redimensionar uma VM, o Microsoft Azure aloca recursos de computação para a sua subscrição. Ocasionalmente, poderá receber erros ao efetuar estas operações – mesmo antes de atingir os limites de subscrição do Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possíveis remediação. As informações também podem ser úteis quando planear a implementação dos seus serviços. Também pode [resolver falhas de alocação ao criar, reiniciar ou redimensionar VMs do Windows no Azure](../windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

