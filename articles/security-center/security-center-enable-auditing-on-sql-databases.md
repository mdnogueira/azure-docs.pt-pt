---
title: "Ativar a auditoria e deteção de ameaças nas bases de dados do SQL Server no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação de centro de segurança do Azure * * ativar a auditoria e deteção de ameaças em bases de dados SQL *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 8f4febdaa4497fee0dc690b59cd6eaa415c5e5cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Ativar a auditoria e deteção de ameaças nas bases de dados do SQL Server no Centro de segurança do Azure
Centro de segurança do Azure recomendará que ativar a auditoria e deteção de ameaças para todas as bases de dados do SQL Server se auditoria e deteção de ameaças já não está ativada. Auditoria e ameaças deteção pode ajudar a manter a conformidade de regulamentação, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que poderão indicar preocupações para a empresa ou suspeitas de violação de segurança.

Uma vez que ativou auditoria pode configurar as definições de deteção de ameaças e mensagens de correio eletrónico para receber alertas de segurança. A deteção de ameaças Deteta atividades de base de dados anómalas, indicando potenciais ameaças de segurança para a base de dados. Isto permite-lhe detetar e reagir a potenciais ameaças à medida que ocorrem.

Esta recomendação aplica-se apenas; o serviço do SQL do Azure não inclui o SQL Server em execução em máquinas virtuais.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **deteção de ameaças e ativar auditoria nas bases de dados do SQL Server**.  Esta ação abre o **deteção de ameaças e ativar auditoria nas bases de dados do SQL Server** painel.

   ![Ativar a auditoria em bases de dados SQL][1]
2. Selecione uma base de dados do SQL Server para ativar a auditoria em. Esta ação abre o **a deteção de ameaças e auditoria** painel.

3. No **a deteção de ameaças e auditoria** painel, selecione **ON** em **auditoria**.

   ![Ativar a auditoria e deteção de ameaças][2]
4. Siga os passos no [a deteção de ameaças de base de dados de SQL no portal do Azure](../sql-database/sql-database-threat-detection-portal.md) para ativar e configurar a deteção de ameaças e para configurar a lista de e-mails que vão receber alertas de segurança mediante a deteção de atividades anómalas.

## <a name="see-also"></a>Consultar também
Este artigo mostrou como implementar a recomendação de centro de segurança "ativar auditoria deteção de ameaças e nas bases de dados do SQL Server." Para obter mais informações sobre como proteger a base de dados do SQL Server, consulte o seguinte:

* [Securing your SQL Database (Proteger a sua Base de Dados SQL)](../sql-database/sql-database-security-overview.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e as informações.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
