---
title: "Proteger as suas aplicações no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento trata as recomendações no Centro de segurança do Azure que o ajudam a proteger as suas aplicações do Azure e manter em conformidade com as políticas de segurança."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-applications-in-azure-security-center"></a>Proteger as suas aplicações no Centro de segurança do Azure
Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identificar potenciais vulnerabilidades de segurança, cria recomendações ajudá-lo durante o processo de configuração de controlos necessários.  Recomendações se aplicam a tipos de recursos do Azure: máquinas virtuais (VMs), redes, SQL e aplicações.

Este artigo aborda as recomendações que se aplicam às aplicações.  Centro de recomendações de aplicação em torno da implementação de uma firewall de aplicação web.  Utilize a tabela abaixo como referência para ajudar a compreender as recomendações de aplicação disponíveis e que cada um faz se aplicá-lo.

## <a name="available-application-recommendations"></a>Recomendações de aplicação disponíveis
| Recomendação | Descrição |
| --- | --- |
| [Adicionar uma firewall de aplicação Web](security-center-add-web-application-firewall.md) |Recomenda-se de que irá implementar uma firewall de aplicação web (WAF) para pontos finais do web. É apresentada uma recomendação WAF para qualquer destinado ao IP público (IP de nível de instância ou IP com balanceamento de carga) que tenha um grupo de segurança de rede associada com portas web de entrada aberta (80,443).</br></br>Centro de segurança recomenda que Aprovisiona uma WAF para ajudar a proteger contra ataques direcionada para as aplicações web em máquinas virtuais e no ambiente de serviço de aplicações. Aplicação serviço de ambiente (ASE) é um [Premium](https://azure.microsoft.com/pricing/details/app-service/) service opção plano do App Service do Azure fornece um ambiente completamente isolado e dedicado para execução segura de aplicações do App Service do Azure. Para saber mais sobre ASE, consulte o [a documentação de ambiente de serviço de aplicação](../app-service/environment/intro.md).</br></br>Pode proteger várias aplicações web no Centro de segurança ao adicionar estas aplicações para as implementações de WAF existentes. |
| [Finalizar a proteção das aplicações](security-center-add-web-application-firewall.md#finalize-application-protection) |Para concluir a configuração de uma WAF, tráfego deve ser reencaminhado para a aplicação de WAF. Seguir esta recomendação conclui as alterações de configuração necessários. |

## <a name="see-also"></a>Consultar também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as máquinas virtuais no Centro de segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Proteger a sua rede no Centro de segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
