---
title: "Criar alertas para os serviços do Azure - CLI de várias plataformas | Microsoft Docs"
description: "Acionador e-mails, as notificações, chamar URLs de Web sites (webhooks) ou automatização quando forem cumpridas condições que especificar."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 92246a8da73a244a1c9a924bed55711d71a20fd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Criar métricas alertas no Monitor do Azure para serviços do Azure - CLI de várias plataformas
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Descrição geral
Este artigo mostra como configurar alertas de métricas do Azure, utilizando a Interface de linha de comandos de várias plataformas (CLI).

> [!NOTE]
> Monitor do Azure é o novo nome para o que foi chamado "Informações do Azure" até 25th de Setembro de 2016. No entanto, os espaços de nomes e, consequentemente, os comandos abaixo contém ainda as "informações".
>
>

Pode receber um alerta com base na monitorização métricas para ou eventos nos seus serviços do Azure.

* **Valores métricos** -o alerta é acionado quando o valor de uma métrica especificado atravesse um limiar atribuir em qualquer direção. Ou seja, aciona ambas quando a condição for satisfeita primeiro e, em seguida, posteriormente quando condição que é já não está a ser cumprido.    
* **Eventos de registo de atividade** -pode acionar um alerta num *cada* eventos ou apenas quando ocorre um determinados eventos. Para saber mais sobre alertas de registo de atividade [clique aqui](monitoring-activity-log-alerts.md)

Pode configurar um alerta de métrico para fazer o seguinte quando aciona:

* enviar notificações por e-mail para o administrador de serviços e coadministradores
* envie correio eletrónico para e-mails adicionais que especificar.
* Chamar um webhook
* iniciar a execução de um runbook do Azure (apenas a partir do portal do Azure neste momento)

Pode configurar e obter informações sobre regras de alerta métricas utilizando

* [Portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interface de linha de comandos (CLI)](insights-alerts-command-line-interface.md)
* [API de REST de Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Pode receber sempre a ajuda para comandos escrevendo um comando e colocando - ajudar no final. Por exemplo:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Criar regras de alertas utilizando a CLI
1. Execute os pré-requisitos e iniciar sessão no Azure. Consulte [amostras da CLI do Azure Monitor](insights-cli-samples.md). Em suma, instalar a CLI e execute estes comandos. Podem ajudá-lo a sessão iniciada, mostram que subscrição que está a utilizar e prepará-lo para executar comandos de Monitor do Azure.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Para listar as regras existentes num grupo de recursos, utilize o seguinte formato **informações do azure a lista de regras de alertas** *[opções] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Para criar uma regra, tem de ter várias partes importantes de informações pela primeira vez.
  * O **ID de recurso** para o recurso que pretende definir um alerta para
  * O **as definições de métrica** disponíveis para esse recurso

     Uma forma de obter o ID de recurso é utilizar o portal do Azure. Pressupondo que o recurso já está a ser criado, selecione-o no portal. Em seguida, no painel do seguinte, selecione *propriedades* sob o *definições* secção. O *ID de recurso* é um campo no painel seguinte. Outra forma consiste em utilizar o [Explorador de recursos do Azure](https://resources.azure.com/).

     É um id de recurso de exemplo para uma aplicação web

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Para obter uma lista de unidades e as métricas disponíveis para esses métricas para o anterior exemplo de recursos, utilize o seguinte comando da CLI:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* a granularidade também o a medição disponível (intervalos de 1 minuto). Utilizar diferentes granularidades dá-lhe opções de métricas diferentes.
4. Para criar uma regra de alerta baseada em métrica, utilize um comando com o seguinte formato:

    **as informações do Azure métrico conjunto de regras de alertas** *[opções] &lt;ruleName&gt; &lt;localização&gt; &lt;resourceGroup&gt; &lt;windowSize &gt; &lt;operador&gt; &lt;limiar&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt; timeAggregationOperator&gt;*

    O exemplo a seguir configura um alerta no recurso web site. Os acionadores de alerta sempre que recebe consistentemente qualquer tráfego de 5 minutos e novamente quando não recebe nenhum tráfego de 5 minutos.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Para criar o webhook ou enviar correio eletrónico quando um métrico alerta é acionado, crie primeiro o e-mail e/ou webhooks. Em seguida, criar a regra imediatamente posteriormente. Não é possível associar webhook ou e-mails com já criou regras utilizando a CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Pode verificar que os alertas não foi criados corretamente ao observar uma regra de individuais.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Para eliminar regras, utilize um comando com o formato:

    **informações de eliminação de regras de alertas** [opções] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Estes comandos, elimine as regras que criou anteriormente neste artigo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Passos seguintes
* [Obter uma descrição geral da monitorização do Azure](monitoring-overview.md) , incluindo os tipos de informações que pode recolher e monitorizar.
* Saiba mais sobre [configurar webhooks alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [configurar alertas de eventos de registo de atividade](monitoring-activity-log-alerts.md).
* Saiba mais sobre [Runbooks de automatização do Azure](../automation/automation-starting-a-runbook.md).
* Obter um [descrição geral de recolha de registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recolher métricas de alta frequência detalhadas do seu serviço.
* Obter um [descrição geral da coleção de métricas](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e reativa.
