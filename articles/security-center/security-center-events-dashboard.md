---
title: "Monitorização e a processar eventos de segurança no Centro de segurança do Azure | Microsoft Docs"
description: "Saiba como pode utilizar o dashboard do Centro de segurança eventos para ver eventos de segurança de VMs do Azure e computadores não do Azure."
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
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorização e a processar eventos de segurança no Centro de segurança do Azure
O dashboard de eventos fornece uma descrição geral do número de eventos de segurança recolhidos ao longo do tempo e uma lista dos eventos relevantes que poderão necessitam da sua atenção.  

> [!NOTE]
> Para utilizar esta funcionalidade, a área de trabalho tem de estar a executar a análise de registos de versão 2 e de estar no escalão Standard do Centro de segurança. Consulte o Centro de segurança [página de preços](security-center-pricing.md) para obter mais informações sobre o escalão Standard.
>
>

## <a name="what-is-a-security-event"></a>O que é um evento de segurança?
Utiliza o Centro de segurança do Microsoft Monitoring Agent para recolher vários segurança e configurações relacionados com eventos das suas máquinas e armazena estes eventos na sua workspace(s). Os exemplos destes dados são: registos de sistema (registos de eventos do Windows), em execução operativo processa e eventos de soluções de segurança integrado com o Centro de segurança. O Agente de Monitorização da Microsoft também copia os ficheiros de informação de falha de sistema para as suas áreas de trabalho.

## <a name="events-processed-dashboard"></a>Dashboard de eventos processados
Aceder a **eventos** dashboard do menu principal do Centro de segurança ou do Centro de segurança **descrição geral** painel.  

![Dashboard de eventos processados][1]

O **eventos** mosaico em **Centro de segurança** mostra o número de eventos que fluem para o Centro de segurança de VMs do Azure e computadores não do Azure.

O **dashboard eventos** fornece uma descrição geral do número de overtime de eventos processados e uma lista de eventos.

 ![Dashboard][2]

 A metade superior do dashboard tendências todos os eventos processados na última semana. Na parte inferior metade do dashboard apresenta uma lista de eventos relevantes e todos os eventos por tipo:

 - **Eventos relevantes** incluir consultas de eventos que fornece o Centro de segurança e consultas de eventos que criar e adicionar. O dashboard também fornece uma vista rápida para a contagem de cada evento relevantes.
 - **Todos os eventos por tipo** mostra os tipos de eventos que estão a ser recebidos e uma contagem de cada tipo. Exemplos de tipo de evento são SecurityEvent, CommonSecurityLog, WindowsFirewall e W3CIISLog.

> [!NOTE]
> Eventos relevantes de incluir [web avaliação de linha de base](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). A Avaliação da Linha de Base Web tem por objetivo localizar definições de servidor Web potencialmente vulneráveis.

## <a name="view-processed-event-details"></a>Ver detalhes do evento processados
1. Sob o **Centro de segurança** menu principal, selecione **eventos**.
2. O **dashboard eventos** pode abrir o Seletor de área de trabalho. Se tiver apenas uma área de trabalho, este Seletor de área de trabalho não aparecer. Se tiver mais de uma área de trabalho, tem de selecionar uma área de trabalho para ver os detalhes de eventos processados. Selecione uma área de trabalho na lista se tiver mais de uma área de trabalho.

  ![Lista de área de trabalho][3]

3. O **dashboard eventos** abre Mostrar detalhes do evento para a área de trabalho selecionado. Pode ver os eventos relevantes e todos os eventos por tipo.  Neste exemplo, selecionamos **eventos relevantes**.

  ![Eventos relevantes][4]

4. Pode consultar mais dados na área de trabalho ao selecionar um tipo de evento. Neste exemplo, selecionamos **SecurityEvent**.

  ![Selecionar um tipo de evento][5]

5. **Registo de pesquisa** abre-se com detalhes adicionais sobre o tipo de evento.

  ![Pesquisas de registos][6]

## <a name="add-a-notable-event"></a>Adicionar um evento relevantes
Centro de segurança fornece eventos relevantes out of box. Pode adicionar eventos relevantes com base na sua própria consulta utilizando o [idioma de consulta de análise de registos](../log-analytics/log-analytics-search-reference.md). Voltaremos ao **dashboard eventos** para adicionar um evento relevantes.

1. Selecione **adicionar eventos relevantes**.

  ![Adicionar um evento relevantes][7]

2. **Adicionar eventos relevantes personalizados** abre.  Em **nome a apresentar**, introduza um nome para o evento relevantes. Em **consulta de pesquisa**, introduza a consulta para o evento.

  ![Introduza a consulta][8]

4. Selecione **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Atualizar a sua área de trabalho para processamento de eventos
A área de trabalho tem de estar a executar a análise de registos de versão 2 e estar no escalão Standard do Centro de segurança a utilizar o processamento de eventos no Centro de segurança. O **dashboard eventos** Seletor de área de trabalho identifica áreas de trabalho que não cumpra estes requisitos.

![Área de trabalho não cumpre os requisitos][9]

Se a linha de área de trabalho:

- Contém **necessita de ATUALIZAR** -tem de atualizar a sua área de trabalho para análise de registos de versão 2
- Contém **ATUALIZAR planear** – é necessário atualizar a sua área de trabalho para o escalão Standard do Centro de segurança
- Está em branco - a área de trabalho cumpre os requisitos e selecionar uma área de trabalho leva-o para o dashboard

> [!NOTE]
> Em **dashboard eventos**, a **eventos** coluna indica a quantidade de eventos em cada área de trabalho.  Esta coluna está em branco para algumas áreas de trabalho porque o escalão gratuito do Centro de segurança é aplicado a área de trabalho. Sob o escalão gratuito, o Centro de segurança irá recolher eventos, mas os eventos não são guardados na análise de registos e não estão disponíveis no dashboard.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Atualizar a área de trabalho para análise de registos de versão 2
1. Selecione uma área de trabalho que **requer ATUALIZAÇÃO**.
2. **Pesquisar atualização** abre. Selecione **atualizar agora**.

  ![Atualizar agora][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Atualizar para o escalão Standard do Centro de segurança
1. Selecione uma área de trabalho com **ATUALIZAR planear**.
2. **Dashboard de eventos** abre. Selecione **dashboard tente eventos**.

  ![Tente dashboard][11]

3. Em **integração de segurança avançada**, selecione a área de trabalho que está a atualizar.
4. Em **preços**, selecione **padrão**.
5. Selecione **Guardar**.

  ![Atualizar para o escalão Standard][12]

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu como utilizar o dashboard de eventos do Centro de segurança. Para obter mais informações sobre como funciona o dashboard e escrever as suas próprias consultas de eventos, consulte:

- [O que é o Log Analytics?](../log-analytics/log-analytics-overview.md) – Descrição geral na análise de registos
- [Registo de compreender procura na análise de registos](../log-analytics/log-analytics-log-search-new.md) - descreve como as pesquisas de registo são utilizadas na análise de registos e fornece os conceitos que devem ser compreendidos antes de criar uma pesquisa de registo
- [Análise de registos de pesquisa referência](../log-analytics/log-analytics-search-reference.md) – aprender a escrever as suas próprias consultas de eventos utilizando a linguagem de consulta no registo

Para saber mais acerca do Centro de segurança, consulte:

- [Descrição geral do Centro de segurança](security-center-intro.md) – principais capacidades de centro de segurança a Describes

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
