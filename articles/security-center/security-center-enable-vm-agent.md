---
title: "Ativar o agente da VM no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação de centro de segurança do Azure * * ativar VM agente * *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 337a7adfd93c76882a749685702bea6d1524c96a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Ativar o agente da VM no Centro de segurança do Azure
O agente da VM tem de estar instalado em máquinas virtuais (VMs) de modo a [ativar a recolha de dados](security-center-enable-data-collection.md).  Centro de segurança do Azure permite-lhe ver as VMs requerem o agente da VM e recomendará que ative o agente da VM dessas VMs.

O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. O artigo [VM Agent and Extensions – Part 2 (Agente de VM e Extensões – Parte 2)](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação. Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **painel recomendações**, selecione **ativar o agente da VM**.
   ![Ativar o Agente de VM][1]
2. Esta ação abre o painel **VM agente em falta ou não está a responder**. Este painel lista as VMs que requerem o agente da VM. Siga as instruções no painel de para instalar o agente da VM.
   ![Agente VM está em falta][2]

## <a name="see-also"></a>Consultar também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
