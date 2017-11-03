---
title: Arquitetura do Kit de desenvolvimento de pilha do Microsoft Azure | Microsoft Docs
description: Ver a arquitetura do Kit de desenvolvimento de pilha do Microsoft Azure.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e8ff17e069bd9237bf06cd79b4c222c50a999eda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitetura do Kit de desenvolvimento de pilha do Microsoft Azure

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

O Kit de desenvolvimento de pilha do Azure é uma implementação de nó único da pilha do Azure. Todos os componentes são instalados em máquinas virtuais em execução numa máquina de anfitrião único. 

## <a name="logical-architecture-diagram"></a>Diagrama de arquitetura lógica
O diagrama seguinte ilustra a arquitetura lógica do kit de desenvolvimento de pilha do Azure e os respetivos componentes.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Funções de máquina virtual
O kit de desenvolvimento de pilha do Azure oferece serviços, utilizando as seguintes VMs no anfitrião:

| Nome | Descrição |
| ----- | ----- |
| **AzS ACS01** | Serviços de armazenamento de pilha do Azure.|
| **AzS ADFS01** | Serviços de Federação do Active Directory (ADFS).  |
| **AzS BGPNAT01** | Contorno router e fornece capacidades NAT e VPN para a pilha do Azure. |
| **AzS CA01** | Serviços de autoridade de certificados para serviços de função de pilha do Azure.|
| **AzS DC01** | Active Directory, DNS e DHCP dos serviços de pilha do Microsoft Azure.|
| **AzS ERCS01** | Consola da recuperação de emergência VM. |
| **AzS GWY01** | Gateway de serviços, tais como ligações do VPN site a site para redes de inquilino.|
| **AzS NC01** | Controlador de rede, que gere os serviços de rede de pilha do Azure.  |
| **AzS SLB01** | O balanceamento de carga serviços multiplexer na pilha do Azure para os inquilinos e os serviços de infraestrutura de pilha do Azure.  |
| **AzS SQL01** | Arquivo de dados internos para funções de infraestrutura de pilha do Azure.  |
| **AzS WAS01** | Portal de administração de pilha do Azure e os serviços do Azure Resource Manager.|
| **AzS WASP01**| Portal de utilizador (inquilino) de pilha do Azure e os serviços do Azure Resource Manager.|
| **AzS XRP01** | Controlador de gestão de infraestrutura do Microsoft Azure pilha, incluindo os fornecedores de recursos de computação, rede e armazenamento.|


## <a name="next-steps"></a>Passos seguintes
[Implementar a pilha do Azure](azure-stack-deploy.md)

[Cenários de primeira para experimentar](azure-stack-first-scenarios.md)

