---
title: "Ativar a auditoria e deteção de ameaças em SQL servers no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação de centro de segurança do Azure * * deteção de ameaças e ativar a auditoria em servidores SQL *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 660b537aef8d175a478ff93d60b8391d55fc92ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Ativar a auditoria e deteção de ameaças em SQL servers no Centro de segurança do Azure
Centro de segurança do Azure recomendará que ativar a auditoria e deteção de ameaças para todas as bases de dados em servidores SQL do Azure se auditoria já não está ativada. Auditoria e ameaças deteção pode ajudar a manter a conformidade de regulamentação, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que poderão indicar preocupações para a empresa ou suspeitas de violação de segurança.

Uma vez que ativou auditoria pode configurar as definições de deteção de ameaças e mensagens de correio eletrónico para receber alertas de segurança. A deteção de ameaças Deteta atividades de base de dados anómalas, indicando potenciais ameaças de segurança para a base de dados. Isto permite-lhe detetar e reagir a potenciais ameaças à medida que ocorrem.

Esta recomendação aplica-se apenas; o serviço do SQL do Azure não inclui o SQL Server em execução em máquinas virtuais nos serviços de infraestrutura do Azure (IaaS do Azure).

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **deteção de ameaças e ativar a auditoria em servidores SQL**.  Esta ação abre o **deteção de ameaças e ativar a auditoria em servidores SQL** painel.

   ![Ativar a auditoria em servidores SQL][1]
2. Selecione um servidor SQL para ativar a auditoria e deteção de ameaças no. Esta ação abre o **a deteção de ameaças e auditoria** painel.

3. No **a deteção de ameaças e auditoria** painel, selecione **ON** em **auditoria**.

   ![Ativar definições de auditoria][2]
4. Siga os passos no [auditoria de base de dados SQL no portal do Azure](../sql-database/sql-database-auditing-portal.md) para configurar o armazenamento onde serão armazenados os registos de auditoria. Conta de armazenamento da subscrição para recolha de dados é a conta do storage predefinida.
5. Siga os passos no [começar com deteção de ameaças de base de dados do SQL Server](../sql-database/sql-database-threat-detection.md) para ativar e configurar a deteção de ameaças e para configurar a lista de e-mails que vão receber alertas de segurança mediante a deteção de atividades anómalas.

## <a name="see-also"></a>Consultar também
Este artigo mostrou como implementar a centro de segurança recomendação "ativar a auditoria e deteção de ameaças em SQL servers." Para obter mais informações sobre como proteger a base de dados do SQL Server, consulte o seguinte:

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
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
