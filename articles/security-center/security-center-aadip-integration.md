---
title: "Ligar a proteção de identidade do Azure Active Directory ao centro de segurança do Azure | Microsoft Docs"
description: "Saiba como o Centro de segurança do Azure integra-se com o Azure Active Directory Identity Protection."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 7562dd5e1c303a6cb97d25bda5aa080bb5643583
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Proteção de identidade do Azure Active Directory ao ligar ao centro de segurança do Azure
Este documento ajuda-o a configurar a integração entre a proteção de identidade do Azure Active Directory (AD) e o Centro de segurança do Azure.

## <a name="why-connect-azure-ad-identity-protection"></a>Por que motivo ligar o Azure AD Identity Protection?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) ajuda a detetar potenciais vulnerabilidades que afetam as identidades da organização. Quando estiver ligado, são capazes de ver alertas do Azure AD Identity Protection no Centro de segurança. Esta integração permite-lhe ver, correlacionar e investigar todos os alertas de segurança relacionadas com as cargas de trabalho de nuvem híbrida no Centro de segurança. 

## <a name="how-do-i-configure-this-integration"></a>Como configurar a esta integração?
Se a sua organização já estiver a utilizar o Azure AD Identity Protection, siga os passos abaixo para configurar a integração:

1. Abra o dashboard do **Centro de Segurança**.
2. No painel esquerdo, clique em **soluções de segurança**. Centro de segurança Deteta automaticamente se o Azure AD Identity Protection está ativado para a sua organização.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Clique em **CONNECT**.
4. No **integrar o Azure AD Identity Protection** página, desloque para baixo e selecione a área de trabalho adequada:

    ![Área de trabalho](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Clique em **Ligar**.

Depois de concluir esta configuração, a solução do Azure AD Identity Protection aparece no **soluções de segurança** página **ligado soluções**. 

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Azure AD Identity Protection ao centro de segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Ligar o Microsoft Advanced Threat Analytics ao Centro de Segurança do Azure](security-center-ata-integration.md)
* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar políticas de segurança para as subscrições do Azure e os grupos de recursos.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Segurança de dados do Centro de segurança do Azure](security-center-data-security.md) -Saiba como os dados são geridos e salvaguardados no Centro de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenha as mais recentes notícias de segurança do Azure e as informações.


