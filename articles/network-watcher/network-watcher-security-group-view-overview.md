---
title: "Introdução à vista de grupo de segurança no observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral da capacidade de vista de segurança de observador de rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: jdial
ms.openlocfilehash: f4175875b68c52e68588b8d0debd003ab73427ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Introdução à vista de grupo de segurança de rede no observador de rede do Azure

Grupos de segurança de rede estão associados a um nível de sub-rede ou um nível de NIC. Quando associados a um nível de sub-rede, aplica-se a todas as instâncias VM na sub-rede. Vista de grupo de segurança de rede devolve todos os NSGs e regras que estão associadas a um nível NIC e a sub-rede para uma máquina virtual que fornecem informações sobre a configuração configurado. Além disso, as regras de segurança eficaz são devolvidas para cada um dos NICs numa VM. Vista de grupo de segurança de rede a utilizar, pode avaliar uma VM para vulnerabilidades de rede, tais como portas abertas. Também pode validar se o grupo de segurança de rede está a funcionar conforme esperado com base num [comparação entre configurada e as regras de segurança eficaz](network-watcher-nsg-auditing-powershell.md).

Um caso de utilização expandido mais está em conformidade de segurança e de auditoria. Pode definir um conjunto de regras de segurança prescritiva como um modelo para a governação de segurança na sua organização. Uma auditoria periódica de compatibilidade pode ser implementada de forma programática através da comparação as regras prescritivos com as regras em vigor para cada uma das VMs na sua rede.

No portal de regras são divididas por eficaz, sub-rede, Interface de rede e predefinido. Isto fornece uma vista simple para as regras aplicadas a uma máquina virtual. É fornecido um botão transferir facilmente transferir todas as regras de segurança, independentemente do separador para um ficheiro CSV.

![vista do grupo de segurança][1]

As regras podem ser selecionadas e um novo painel abre-se para mostrar o grupo de segurança de rede e os prefixos de origem e de destino. A partir deste painel pode navegar diretamente para o recurso do grupo de segurança de rede.

![pesquisa][2]

### <a name="next-steps"></a>Passos seguintes

Saiba como efetuar a auditoria as definições do grupo de segurança de rede, visitando [definições de auditoria do grupo de segurança de rede com o PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









