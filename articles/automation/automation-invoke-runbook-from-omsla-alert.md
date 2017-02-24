---
title: "Invocar um Runbook da Automatização do Azure a partir de um Alerta do Log Analytics | Microsoft Docs"
description: "Este artigo disponibiliza uma descrição geral de como invocar um runbook da Automatização a partir de um alerta do Microsoft OMS Log Analytics."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 4ce5ad30d79e92a11231313fe13dd42b94fc2aa4
ms.openlocfilehash: 50969591267ca74e5c4d4aa5c1efe5b673498309

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Invocar um runbook da Automatização do Azure a partir de um Alerta do OMS Log Analytics

Quando é configurado um alerta no Log Analytics para criar um registo de alerta se os resultados corresponderem a um determinado critério, como um pico prolongado na utilização do processador ou um processo de uma aplicação específica crítico para a funcionalidade de uma aplicação empresarial falhar e escrever um evento correspondente no registo de eventos do Windows, esse alerta pode executar automaticamente um rubook da Automatização numa tentativa de tentar remediar sozinho o problema.  

Existem duas opções para invocar um runbook durante a configuração do alerta.  Mais concretamente:

1. Utilizar um Webhook.
   * Esta é a única opção disponível se a sua área de trabalho do OMS não estiver associada a uma conta de Automatização.
   * Se já tiver uma conta de Automatização ligada a uma área de trabalho do OMS, esta opção continua disponível.  

2. Selecionar um runbook diretamente.
   * Esta opção só está disponível se a área de trabalho do OMS estiver ligada a uma conta de Automatização.  

## <a name="calling-a-runbook-using-a-webhook"></a>Utilizar um webhook para chamar um runbook

Um webhook permite-lhe iniciar um runbook específico na Automatização do Azure através de um único pedido HTTP.  Antes de configurar o [alerta do Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule) para chamar o runbook com um webhook como ação do alerta, tem de, primeiro, criar um webhook para o runbook que será chamado com este método.  Veja e siga os passos no artigo [Create a Webhook (Criar um Webhook)](automation-webhooks.md#creating-a-webhook) e não se esqueça de registar o URL do webhook, para que o possa referenciar enquanto configura a regra do alerta.   

## <a name="calling-a-runbook-directly"></a>Chamar um runbook diretamente

Se tiver instalada e configurada a oferta Automatização e Controlo na área de trabalho do OMS, quando configurar a opção das ações do Runbook para o alerta, pode ver todos os runbooks na lista pendente **Selecionar runbook** e selecionar o runbook específico que pretende executar como resposta ao alerta.  O runbook selecionado pode ser executado numa área de trabalho na cloud do Azure ou numa função de trabalho de runbook híbrida.  Se o alerta for criado com a opção do runbook, é criado um webhook para o runbook.  Pode ver o runbook ao aceder à conta de Automatização e navegar para o painel do webhook do runbook selecionado.  Se eliminar o alerta, o webhook não é eliminado, mas o utilizador pode eliminá-lo manualmente.  Não há problema se o webhook não for eliminado; é apenas um item órfão que terá de ser eliminado, a um dado momento, de modo a manter uma conta de Automatização organizada.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Características de um runbook (para ambas as opções)

Ambos os métodos para chamar o runbook a partir do alerta do Log Analytics têm características comportamentais diferentes que tem de perceber antes de configurar as regras do alerta.  

* Tem de ter um parâmetro de entrada do runbook com o nome **WebhookData** e que seja do tipo **Object**.  Pode ser obrigatório ou opcional.  O alerta utiliza este parâmetro de entrada para transmitir os resultados da pesquisa ao runbook.

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )
  
*  Tem de ter código para converter o WebhookData num objeto do PowerShell.

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* será uma matriz de objetos; cada objeto contém os campos com valores de um resultado de pesquisa

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>Inconsistências do WebhookData entre a opção webhook e a opção runbook 

* Quando configurar um alerta para chamar um webhook, introduza um URL do webhook criado para um runbook e clique no botão **Testar Webhook**.  O WebhookData resultante enviado para o runbook não contém nem *SearchResult* nem *.SearchResults*.

*  Se guardar o alerta, quando este for acionado e chamar o webhook, o WebhookData enviado para o runbook conterá *.SearchResult*.
* Se criar um alerta e o configurar para chamar um runbook (o que também cria um webhook), quando o alerta for acionado, envia o WebhookData ao runbook que contém *.SearchResults*.

Deste modo, no exemplo de código acima, terá de obter *.SearchResult* se o alerta chamar um webhook e terá de obter *.SearchResults* se o alerta chamar um runbook diretamente.

## <a name="example-walkthrough"></a>Instruções de exemplo 

Para demonstrar como isto funciona, vamos utilizar o runbook gráfico de exemplo seguinte, que inicia um serviço do Windows.<br><br> ![Runbook Gráfico de Início de Serviço do Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

O runbook tem um parâmetro de entrada do tipo **Object** com o nome **WebhookData** e inclui os dados do webhook transmitidos do alerta que contém *.SearchResults*.<br><br> ![Parâmetros de entrada do runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Neste exemplo, criámos, no Log Analytics, dois campos personalizados, *SvcDisplayName_CF* e *SvcState_CF*, para extrair o nome a apresentar e o estado do serviço (ou seja, em execução ou parado) a partir do evento escrito no registo de eventos do Sistema.  Depois, criámos uma regra de alerta com a consulta de pesquisa `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"`, para que possamos detetar quando o serviço Print Spooler é parado no sistema Windows.  Pode ser qualquer serviço de interesse, mas este exemplo faz referência a um dos serviços pré-existentes que estão incluídos no SO Windows.  A ação do alerta está configurada para executar o nosso runbook utilizado neste exemplo e executado na Função de Trabalho de Runbook Híbrida, que está ativada nos sistemas de destino.   

A atividade do código do runbook **Get Service Name from LA** vai converter a cadeia em formato JSON num tipo de objeto e filtra o item *SvcDisplayName_CF* para extrair o nome a apresentar do serviço do Windows e transmiti-lo para a atividade seguinte, que vai verificar se o serviço está parado antes de tentar reiniciá-lo.  *SvcDisplayName_CF* é um [campo personalizado](../log-analytics/log-analytics-custom-fields.md) criado no Log Analytics para demonstrar este exemplo.

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

Quando o serviço para, a regra do alerta no Log Analytics deteta uma correspondência, aciona o runbook e envia o contexto do alerta para este. O runbook toma medidas para verificar se o serviço está parado e, se for esse o caso, tenta reiniciá-lo, verificar se foi iniciado corretamente e produz os resultados.     

Em alternativa, se a sua conta de Automatização não estiver ligada à área de trabalho do OMS, configuraria a regra de alerta com uma ação de webhook para acionar o runbook e configuraria o runbook para converter a cadeia em formato JSON e filtrar em *.SearchResult*, seguindo a orientação mencionada anteriormente.    

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os alertas no Log Analytics e como criá-los, veja [Alerts in Log Analytics (Alertas no Log Analytics)](../log-analytics/log-analytics-alerts.md).

* Para saber como utilizar webhooks para acionar runbooks, veja [Azure Automation webhooks (Webhooks da Automatização do Azure)](automation-webhooks.md).



<!--HONumber=Feb17_HO2-->


