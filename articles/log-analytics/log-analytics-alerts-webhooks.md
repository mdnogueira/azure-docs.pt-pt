---
title: "Exemplo de ação de alerta de Webhook no OMS Log Analytics | Microsoft Docs"
description: "Uma das ações que pode ser executadas em resposta a um alerta de análise de registos é uma * webhook *, que lhe permite invocar um processo externo através de um único pedido HTTP. Este artigo explica-se um exemplo de criação de uma ação de webhook num alerta de análise de registos utilizando Slack."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>Criar uma ação do webhook alerta na análise de registos do OMS enviar mensagem para Slack
Uma das ações pode executar em resposta a um [alerta de análise de registos](log-analytics-alerts.md) é um *webhook*, que lhe permite invocar um processo externo através de um único pedido HTTP.  Pode ler sobre os detalhes de alertas e webhooks no [alertas na análise de registos](log-analytics-alerts.md)

Neste artigo, vamos explicar através de um exemplo de criação de uma ação de webhook num alerta de análise de registos utilizando Slack que é um serviço de mensagens.

> [!NOTE]
> Tem de ter uma conta para concluir este exemplo Slack.  Pode inscrever-se numa conta gratuita em [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>Passo 1 – ativar webhooks no Slack
1. Inicie sessão no Slack em [slack.com](http://slack.com).
2. Selecione um canal no **canais** secção no painel esquerdo.  Este é o canal de que a mensagem será enviada para.  Pode selecionar um dos canais predefinido, tal como **geral** ou **aleatório**.  Num cenário de produção, provavelmente criaria um canal especial, tal como **criticalservicealerts**. <br>
   
   ![Nos canais slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Clique em **adicionar uma aplicação ou integração personalizados** para abrir o diretório de aplicação.
4. Tipo *webhooks* para a caixa de pesquisa e, em seguida, selecione **WebHooks entrada**. <br>
   
   ![Nos canais slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Clique em **instalar** junto ao nome da sua equipa.
6. Clique em **Adicionar configuração**.
7. Selecione o canal que vai utilizar para este exemplo e, em seguida, clique em **Adicionar entrada WebHooks integração**.  
8. Copiar o **URL do Webhook**.  Que irá ser colar isto para a configuração do alerta. <br>
   
    ![Nos canais slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Passo 2 - Criar regra de alerta na análise de registos
1. [Criar uma regra de alerta](log-analytics-alerts.md) com as seguintes definições.
   * Consulta:```    Type=Event EventLevelName=error ```
   * Verificar este alerta cada: 5 minutos
   * O número de resultados é: maiores que 10
   * Durante este período de tempo: 60 minutos
   * Selecione **Sim** para **Webhook** e **não** para outras ações.
2. Cole o URL de Slack para o **URL do Webhook** campo.
3. Selecione a opção para **incluem um payload JSON personalizado**.
4. Slack espera um payload formatado em JSON, com um parâmetro com o nome *texto*.  Este é o texto que apresentará a mensagem cria.  Pode utilizar um ou mais dos parâmetros de alerta utilizando o  *#*  símbolo, tais como no exemplo seguinte.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![payload JSON de exemplo](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Clique em **guardar** para guardar a regra de alerta.
6. Aguarde algum tempo suficiente para um alerta a ser criado e, em seguida, procurar Slack uma mensagem que será semelhante ao seguinte.
   
   ![exemplo webhook no Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Avançadas payload de webhook para Slack
Amplamente pode personalizar as mensagens de entrada com Slack. Para obter mais informações, consulte [Webhooks entrada](https://api.slack.com/incoming-webhooks) no Web site Slack. Segue-se um payload mais complexo para criar uma mensagem avançada com formatação:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Isto irá gerar uma mensagem no Slack semelhante ao seguinte.

![mensagem de exemplo na Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Resumo
Com esta regra de alerta no local, terá de uma mensagem enviada para Slack sempre que os critérios são cumpridos.  

Este é apenas um exemplo de uma ação que pode criar em resposta a um alerta.  Pode criar uma ação de webhook que chama outro serviço externo, uma ação de runbook para iniciar um runbook na automatização do Azure ou uma ação de correio eletrónico para enviar um e-mail para si ou outros destinatários.   

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre outros [ações na análise de registos de alerta](log-analytics-alerts-actions.md) incluindo outras ações.


