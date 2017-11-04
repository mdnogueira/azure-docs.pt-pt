---
title: "Ligar o Microsoft Advanced Threat Analytics ao centro de segurança do Azure | Microsoft Docs"
description: "Saiba como o Centro de segurança do Azure integra-se com o Microsoft Advanced Threat Analytics."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: e1b9e598af3b55c1d9591e5c1e529a80ae3319ca
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Ligação Microsoft Advanced Threat Analytics ao centro de segurança do Azure
Este documento ajuda-o a configurar a integração entre o Microsoft Advanced Threat Analytics e o Centro de segurança do Azure.

## <a name="why-add-advanced-threat-analytics-data"></a>Por que motivo para adicionar dados Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) é uma plataforma no local que ajuda a detetar comportamentos dos utilizadores suspeita. Quando estiver ligado, conseguir ver ações suspeitas detetadas pelo ATA no Centro de segurança. Esta integração permite-lhe ver, correlacionar e investigar todos os alertas de segurança relacionadas com as cargas de trabalho de nuvem híbrida no Centro de segurança. 

## <a name="how-do-i-configure-this-integration"></a>Como configurar a esta integração?
Partindo do princípio que já tiver instalado o ATA, e a funcionar corretamente no local, siga estes passos para configurar esta integração:

1. Inicie sessão para o ATA Center e aceder ao portal do ATA.
2. Clique em **servidor Syslog** no painel esquerdo.

    ![Servidor syslog](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. No **ponto final do servidor Syslog** campo, escreva 127.0.0.7 (tem de ser este endereço) e escreva 5114 na porta (recomendada). Enquanto o número da porta é uma recomendação, qualquer porta exclusivo deverão funcionar. Deixe todas as outras opções como está e clique em **guardar**.
4. Clique em **notificações** no painel esquerdo e ative todas as notificações de Syslog (recomendadas), conforme mostrado na imagem seguinte:

    ![Notificações](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Clique em **Guardar**.
6. Abra o dashboard do **Centro de Segurança**.
7. No painel esquerdo, clique em **soluções de segurança**.
8. Em **Advanced Threat Analytics**, clique em **adicionar**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Vá para o último passo e clique em **Transferir agente**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. No **adicionar novo computador de não Azure** página, selecione a área de trabalho.

    ![Não do Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. No **agente direta** página, transfira o agente de Windows adequado e anota do **ID da área de trabalho** e **chave primária**.

    ![Direcionar o agente](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Instale este agente no ATA Center. Durante a instalação, certifique-se de que seleciona a opção **ligar o agente para análise de registos do Azure (OMS)**e forneça o *ID da área de trabalho*, e *chave primária* quando solicitado .


Depois de concluir a instalação, a integração for concluída e será capaz de ver novos alertas enviadas do ATA para o Centro de segurança no **alertas de segurança** e **pesquisa**. A solução é apresentado no **soluções de segurança** página **ligado soluções**. 

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Microsoft ATA ao centro de segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Ligar o Azure Active Directory Identity Protection ao Centro de Segurança do Azure](security-center-aadip-integration.md)
* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar políticas de segurança para as subscrições do Azure e os grupos de recursos.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Segurança de dados do Centro de segurança do Azure](security-center-data-security.md) -Saiba como os dados são geridos e salvaguardados no Centro de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenha as mais recentes notícias de segurança do Azure e as informações.


