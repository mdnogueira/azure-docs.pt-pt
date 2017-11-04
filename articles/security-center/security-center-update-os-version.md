---
title: "Versão de atualização do SO no Centro de segurança do Azure | Microsoft Docs"
description: "Este artigo mostra como implementar a recomendação de centro de segurança do Azure * * SO de atualização versão * *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
ms.openlocfilehash: ce0d178914907750e5da59f223a4b1e04b9bb6fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="update-os-version-in-azure-security-center"></a>Atualizar a versão do SO no Centro de segurança do Azure
Para máquinas virtuais (VMs) nos serviços em nuvem, o Centro de segurança do Azure recomendará que o sistema operativo (SO) ser atualizada se existir uma versão mais recente disponível.  Nuvem apenas dos serviços de funções web e de trabalho em execução na produção ranhuras são monitorizadas.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
> 
> 

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **atualizar versão do SO**.
   ![Atualizar a versão do SO][1]
2. Esta ação abre o painel **atualizar versão do SO**. Siga os passos neste painel para atualizar a versão do SO.

## <a name="see-also"></a>Consultar também
Este artigo mostrou como implementar a recomendação de centro de segurança "Versão de atualização do SO". Para obter mais informações sobre serviços em nuvem e atualizar a versão do SO para um serviço em nuvem, consulte:

* [Descrição geral dos Serviços Cloud](../cloud-services/cloud-services-choose-me.md)
* [Como atualizar um serviço em nuvem](../cloud-services/cloud-services-update-azure-service.md)
* [Como configurar um Serviços Cloud](../cloud-services/cloud-services-how-to-configure-portal.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e as informações.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
