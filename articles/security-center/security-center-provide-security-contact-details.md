---
title: "Fornecer detalhes de contacto de segurança no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como fornecer detalhes de contacto de segurança no Centro de segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 726b59c45e2eb18eebe28a180db23336ae141408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornecer detalhes de contacto de segurança no Centro de segurança do Azure
Centro de segurança do Azure recomendará que forneça os detalhes de contacto de segurança para a sua subscrição do Azure se ainda não o fez. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. MSRC efetua a segurança selecione monitorização de rede do Azure e a infraestrutura e recebe queixas intelligence e abuso de ameaças de terceiros.

Uma notificação por e-mail é enviada a primeira ocorrência diária de um alerta e apenas para os alertas de elevada gravidade. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Grupos de recursos dentro de uma subscrição irão herdar estas definições.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Em **recomendações**, selecione **fornecer detalhes de contacto de segurança**.
   ![Forneça o contacto de segurança][1]
2. Selecione a subscrição do Azure para fornecer informações de contacto.
3. Esta ação abre **política de segurança - notificações por E-Mail**.

   ![Disponibilizar detalhes de contacto de segurança][2]

   * Introduza o endereço de correio eletrónico de contacto de segurança ou os endereços, separados por vírgulas. Não é um limite para o número de endereços de e-mail que pode introduzir.
   * Introduza um número de telefone internacional contacto de segurança.
   * Para receber mensagens de correio eletrónico sobre alertas de elevada gravidade, ative a opção **enviar-me mensagens de correio eletrónico sobre alertas**.
   * No futuro, terá a opção para enviar notificações por e-mail para proprietários de subscrição. Esta opção está atualmente a cinzento.
   * Selecione **guardar** para aplicar as informações de contacto de segurança à sua subscrição.

## <a name="see-also"></a>Consultar também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e as informações.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
