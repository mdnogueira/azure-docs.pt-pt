---
title: "Aplicar atualizações do sistema no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar as recomendações do Centro de segurança do Azure * * aplicar sistema atualizações * * e * * reinício após atualizações do sistema *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar atualizações do sistema no Centro de segurança do Azure
Centro de segurança do Azure monitoriza diárias Windows e Linux as máquinas virtuais (VMs) e computadores em falta atualizações do sistema operativo. Centro de segurança obtém uma lista de atualizações críticas e de segurança disponíveis do Windows Update ou Windows Server Update Services (WSUS), dependendo de que o serviço está configurado num computador Windows. Centro de segurança também verifica se as atualizações mais recentes nos sistemas Linux. Se a VM ou o computador está em falta uma atualização do sistema, o Centro de segurança recomendará que aplique a atualizações do sistema.

## <a name="implement-the-recommendation"></a>Implementar a recomendação
Aplicar o sistema atualizações é apresentada como uma recomendação no Centro de segurança. Se a VM ou o computador está em falta uma atualização do sistema, será apresentada esta recomendação em **recomendações** e, em **computação**.  Selecionar a recomendação é aberto o **aplicar atualizações do sistema** dashboard.

Neste exemplo, utilizaremos **computação**.

1. Selecione **computação** sob o menu principal do Centro de segurança.

   ![Selecione de computação][1]

2. Em **computação**, selecione **em falta atualizações do sistema**. O **aplicar atualizações do sistema** dashboard abre.

   ![Aplicar o dashboard de atualizações do sistema][2]

   Fornece a parte superior do dashboard:

    - O número total de VMs com Linux e do Windows em falta atualizações do sistema de computadores.
    - O número total de atualizações críticas em falta entre as VMs e computadores.
    - O número total de atualizações de segurança em falta entre as VMs e computadores.

  Na parte inferior do dashboard apresenta uma lista de todas as atualizações em falta nas suas VMs e computadores e a gravidade da atualização em falta.  A lista inclui:

    - NOME: Nome da atualização em falta.
    - NÃO. VMs &AMP; computadores: número Total de VMs e os computadores que estão em falta esta atualização.
    - Estado: O estado atual da Recomendação:

      - Abra: A recomendação ainda não foi tratada.
      - Em curso: A recomendação está atualmente a ser aplicada a esses recursos e é necessária nenhuma ação por si.
      - Resolver: A recomendação já foi concluída. (Quando o problema for resolvido, a entrada fica a cinzento).

    - GRAVIDADE: Descreve a gravidade dessa recomendação específica:

      - Elevado: Uma vulnerabilidade existe com um recurso significativo (aplicação, máquina virtual ou grupo de segurança de rede) e necessita de atenção.
      - Média: Passos não críticos ou adicionais são necessários para concluir um processo ou eliminar uma vulnerabilidade.
      - Baixa: Uma vulnerabilidade deve ser tratada, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).

3. Selecione uma atualização em falta na lista para ver os detalhes.

   ![Atualização de segurança em falta][3]

4. Selecione o **pesquisa** ícone na fita superior.  Uma consulta de pesquisa de análise de registos abre filtrados para os computadores em falta a atualização.

   ![Análise de registos de pesquisa][4]

5. Selecione um computador a partir da lista para obter mais informações. Resultado da pesquisa outro abre-se com informações filtradas apenas para esse computador.

    ![Análise de registos de pesquisa][5]

## <a name="reboot-after-system-updates"></a>Reiniciar após as atualizações do sistema
1. Volte à **recomendações** painel. Uma nova entrada foi gerada depois de aplicar atualizações do sistema, denominadas **reinício após atualizações do sistema**. Esta entrada permite-lhe saber que necessita de reiniciar a VM para concluir o processo de aplicação de atualizações do sistema.

   ![Reiniciar após as atualizações do sistema][6]
2. Selecione **reinício após atualizações do sistema**. Esta ação abre **um reinício está pendente para concluir a atualizações do sistema** painel a apresentar uma lista de VMs que é necessário reiniciar para concluir o sistema de aplicar as atualizações de processo.

   ![Reinício pendente][7]

Reinicie a VM do Azure para concluir o processo.

## <a name="next-steps"></a>Passos seguintes
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e conformidade.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
