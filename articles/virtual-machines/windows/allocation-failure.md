---
title: "Resolução de problemas de falhas de atribuição de VM do Windows | Microsoft Docs"
description: "Resolver problemas de falhas de alocação quando criar, reiniciar ou redimensionar uma VM do Windows no Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: a27b728dd902f6230a8e0a029ab69dabdd4cc4aa
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Resolver problemas de falhas de alocação quando criar, reiniciar ou redimensionar VMs do Windows no Azure
Quando criar uma VM, reiniciar VMs (desalocadas) paradas ou redimensionar uma VM, o Microsoft Azure aloca recursos de computação para a sua subscrição. Ocasionalmente, poderá receber erros ao efetuar estas operações – mesmo antes de atingir os limites de subscrição do Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possíveis remediação. As informações também podem ser úteis quando planear a implementação dos seus serviços. Também pode [resolver falhas de alocação ao criar, reiniciar ou redimensionar VMs com Linux no Azure](../linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

