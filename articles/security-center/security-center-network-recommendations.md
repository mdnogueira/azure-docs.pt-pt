---
title: "Proteger a sua rede no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento trata as recomendações no Centro de segurança do Azure que o ajudam a proteger a rede do Azure e manter em conformidade com as políticas de segurança."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-network-in-azure-security-center"></a>Proteger a sua rede no Centro de segurança do Azure
Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identificar potenciais vulnerabilidades de segurança, cria recomendações ajudá-lo durante o processo de configuração de controlos necessários.  Recomendações se aplicam a tipos de recursos do Azure: máquinas virtuais (VMs), redes, SQL e aplicações.

Este artigo aborda as recomendações que se aplicam à sua rede.  Centro de recomendações de rede em torno da próximas geração firewalls, grupos de segurança de rede, configurar regras de tráfego de entrada e mais informações.  Utilize a tabela abaixo como referência para ajudar a compreender as recomendações de rede disponível e que cada um faz se aplicá-lo.

## <a name="available-network-recommendations"></a>Recomendações de rede disponível
| Recomendação | Descrição |
| --- | --- |
| [Adicionar uma Firewall da Próxima Geração](security-center-add-next-generation-firewall.md) |Recomenda-se de que adicionar uma Firewall seguinte de geração (NGFW) a partir de um parceiro da Microsoft para aumentar as proteções de segurança. |
| [Encaminhar o tráfego apenas através da NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Recomenda-se de que configura regras de grupo (NSG) de segurança de rede forçar o tráfego de entrada à VM através do seu NGFW. |
| [Ativar grupos de segurança de rede em sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md) |Recomenda-se que ative NSGs em sub-redes ou VMs. |
| [Restringir o acesso através da Internet com o ponto final](security-center-restrict-access-through-internet-facing-endpoints.md) |Recomenda-se de que configura as regras de tráfego de entrada para os NSGs. |

## <a name="see-also"></a>Consultar também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as máquinas virtuais no Centro de segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Proteger as suas aplicações no Centro de segurança do Azure](security-center-application-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
