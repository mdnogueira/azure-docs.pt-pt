---
title: "Responder a eventos com alertas de análise do registo do Azure | Microsoft Docs"
description: "Este tutorial ajuda-o a compreender os alertas na análise de registos para identificar informações importantes no seu repositório do OMS e proativamente notificá-lo de problemas ou da invocação ações para tentar corrigir as entradas."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3ab8d32eb4b3f2748249f40139de76c8e7f4d971
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Responder a eventos com alertas de análise do registo
Alertas na análise de registos identificam informações importantes no seu repositório de análise de registos.  Estes são criados pelas regras de alerta que execute automaticamente pesquisas de registo em intervalos regulares e se os resultados da pesquisa de registo correspondentes aos critérios de específicos, em seguida, é criado um registo de alerta e pode ser configurado para efetuar uma resposta automática.  Este tutorial é uma continuação da a [dashboards de criar e a partilha de dados de análise de registos](log-analytics-tutorial-dashboards.md) tutorial.   

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma regra de alerta
> * Configurar uma regra de alerta para enviar uma notificação de correio electrónico

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligado à área de trabalho de análise de registos](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Inicie sessão no portal do Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Criar alertas

Os alertas são criados pelas regras de alerta que execute automaticamente pesquisas de registo em intervalos regulares.  Pode criar alertas com base nas métricas de desempenho específica ou quando determinados eventos são criados, ausência de um evento ou um número de eventos são criadas dentro de uma janela de tempo específico.  Por exemplo, alertas podem ser utilizados para o notificar quando a utilização média da CPU excede um determinado limiar, é gerado um evento quando um serviço específico do Windows ou Linux daemon não está em execução.   Se os resultados da pesquisa de registo correspondentes aos critérios de específicos, em seguida, é criado um registo de alerta. A regra pode, em seguida, execute automaticamente uma ou mais ações para notificá-lo do alerta proativamente ou da invocação do outro processo. 

No exemplo seguinte, iremos criar uma regra de alerta de métrica de medida que irá criar um alerta para cada objeto de computador na consulta com um valor que excede um limiar de 90%.

1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**...
2. Inicie o portal do OMS, selecionando o Portal do OMS e no **descrição geral** página, selecione **pesquisa registo**.  
3. Selecione **Favoritos** da parte superior do portal e no **pesquisas guardadas** painel à direita, selecione a consulta *VMs do Azure - utilização do processador*.  
4. Clique em **alerta** na parte superior da página para abrir o **Adicionar regra de alerta** ecrã.  
5. Configure a regra de alerta com as seguintes informações:  
   a. Forneça um **nome** para o alerta, tais como *excedida de utilização do processador VM > 90*  
   b. Para **janela de tempo**, especifique um intervalo de tempo para a consulta, tal como *30*.  A consulta devolve apenas os registos que foram criados dentro deste intervalo da hora atual.  
   c. **Frequência de alerta** Especifica a frequência com que a consulta deve ser executada.  Neste exemplo, especifique *5* minutos, que irão ocorrer dentro do nosso janela de tempo especificada.  
   d. Selecione **métrica medida** e introduza *90* para **valor agregado** e introduza *3* para **alerta acionador com base no**   
   e. Em **ações**, desative a notificação por correio eletrónico.
6. Clique em **guardar** para concluir a regra de alerta. Será iniciada imediatamente a executar.<br><br> ![Exemplo de regra de alerta](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Alerta registos criados pelas regras de alerta na análise de registos tem um tipo de **alerta** e um SourceSystem de **OMS**.<br><br> ![Exemplo de eventos de alertas gerados](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Ações de alerta
Pode realizar ações avançadas com alertas, tais como criar uma notificação de correio eletrónico, inicie um [runbook de automatização](../automation/automation-runbook-types.md), utilize um webhook para criar um registo de incidente no seu sistema de gestão de incidentes ITSM ou com o [IT Solução do conector de gestão do serviço](log-analytics-itsmc-overview.md) como resposta quando os critérios de alerta são cumpridos.   

Ações de e-mail enviam um e-mail com os detalhes do alerta para um ou mais destinatários. Pode especificar o assunto do correio, mas o respetivo conteúdo é um formato de padrão construído através da análise de registos.  Vamos atualizar a regra de alerta criado anteriormente e configurá-lo para mensagens de correio electrónico notificá-lo em vez de ativamente monitorização para o registo de alerta com uma pesquisa de registo.     

1. No portal do OMS, no menu superior selecione **definições** e, em seguida, selecione **alertas**.
2. Na lista de regras de alerta, clique no ícone de lápis junto ao alerta criado anteriormente.
3. Em **ações** secção, ativar notificações por e-mail.
4. Forneça um **requerente** para o correio eletrónico, tais como *limiar de utilização excedida do processador > 90*.
5. Adicionar endereços de um ou mais destinatários de correio electrónico no **destinatários** campo.  Se especificar mais de um endereço, separe os endereços com um ponto e vírgula (;).
6. Clique em **guardar** para concluir a regra de alerta. Será iniciada imediatamente a executar.<br><br> ![Regra de alerta com a notificação por e-mail](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu como alerta regras proativamente podem identificar e responder a um problema quando executam pesquisas de registo em agendada intervalos e corresponder a um determinado critério.  

Siga esta ligação para ver pré-criadas amostras de script de análise de registos.  

> [!div class="nextstepaction"]
> [Exemplos de script de análise de registo](powershell-samples.md)