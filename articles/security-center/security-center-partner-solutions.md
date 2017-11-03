---
title: "Gerir ligado soluções no Centro de segurança do Azure de parceiros | Microsoft Docs"
description: "Este documento vai ajudá-lo a compreender como o Centro de Segurança do Azure lhe permite monitorizar rapidamente o estado de funcionamento das suas soluções de parceiros integradas na sua subscrição do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Gerir soluções de parceiros ligadas Centro de segurança do Azure
Este artigo explica como gerir e monitorizar soluções de segurança ligado no Centro de segurança do Azure.

## <a name="monitoring-partner-solutions"></a>Monitorizar soluções de parceiros
Para monitorizar o estado de funcionamento de soluções de segurança ligado e efetuar a gestão básicas:

1. Em **Centro de segurança - descrição geral**, selecione **soluções de segurança**.

  ![Selecione as soluções de segurança][1]

  O **ligado soluções** secção inclui as soluções de segurança que estão ligadas ao centro de segurança e informações sobre o estado de funcionamento de cada solução.

  ![Soluções de parceiros][2]

   O estado de uma solução de parceiros pode ser:

   * Bom estado de funcionamento (verde) - não há nenhum problema de estado de funcionamento.
   * Mau estado de funcionamento (vermelho) – existe um problema de estado de funcionamento que exige atenção imediata.
   * Problemas de estado de funcionamento (cor de laranja) - a solução deixou o estado de funcionamento de relatórios.
   * Não reportada (cinzento) – a solução não comunicou qualquer coisa ainda, estado de uma solução pode ser não reportado se recentemente foi ligada e ainda está a implementar, ou não existem dados de estado de funcionamento estão disponíveis.

   > [!NOTE]
   > Se os dados de estado de funcionamento não estiver disponíveis, o Centro de segurança mostra a data e hora do último evento recebida para indicar se a solução estiver a comunicar ou não. Se não existem dados de estado de funcionamento estão disponíveis e não existem alertas são recebidas nos últimos 14 dias, o Centro de segurança indica que a solução está danificado ou não Reporting Services.
   >
   >

2. Selecione **vista** para opções e informações adicionais, que inclui:

  - **Consola de soluções**. Abre a experiência de gestão para esta solução.
  - **Associar a VM**. Abre o painel de aplicações de ligação. Aqui pode ligar recursos à solução de parceiros.
  - **Eliminar a solução**.
  - **Configurar**.

   ![Detalhe de solução de parceiros][3]

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a gerir e monitorizar soluções de segurança ligado no Centro de segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Descrição geral das soluções de segurança](security-center-partner-integration.md) — Saiba como ligar e gerir soluções de segurança.
* [Ligar o Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) — Saiba como ligar alertas do ATA.
* [Ligar o Azure Active Directory (AD) Identity Protection ](security-center-aadip-integration.md) — Saiba como ligar alertas do Azure AD Identity Protection.
* [Integração e soluções de parceiros](security-center-partner-integration.md) -uma descrição geral da integração de outras soluções de segurança.
* [Gerir e responder a alertas de segurança](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
