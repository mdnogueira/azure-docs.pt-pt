---
title: "Monitorizar os registos de acesso, registos de desempenho, estado de funcionamento de back-end e métricas de Gateway de aplicação | Microsoft Docs"
description: "Saiba como ativar e gerir registos de acesso e registos de desempenho para o Gateway de aplicação"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager
ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: amitsriva
ms.openlocfilehash: 12c252340b82aba5ee69b12db83353750782e7c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Estado de funcionamento de back-end, os registos de diagnóstico e métricas de Gateway de aplicação

Ao utilizar o Gateway de aplicação do Azure, pode monitorizar os recursos das seguintes formas:

* [Estado de funcionamento de back-end](#back-end-health): Gateway de aplicação fornece a capacidade de monitorizar o estado de funcionamento dos servidores nos agrupamentos de back-end através do portal do Azure e através do PowerShell. Também pode encontrar o estado de funcionamento dos agrupamentos de back-end através de registos de diagnóstico de desempenho.

* [Os registos](#diagnostic-logs): permitir que os registos de desempenho, acesso e outros dados a ser guardado ou consumido de um recurso para efeitos de monitorização.

* [Métricas](#metrics): Gateway de aplicação não tem atualmente uma métrica. Esta métrica mede o débito do gateway de aplicação em bytes por segundo.

## <a name="back-end-health"></a>Estado de funcionamento de back-end

Gateway de aplicação fornece a capacidade de monitorizar o estado de funcionamento dos membros individuais dos conjuntos de back-end através do portal, PowerShell e a interface de linha de comandos (CLI). Também pode encontrar um Estado de funcionamento agregado resumo dos conjuntos de back-end através de registos de diagnóstico de desempenho. 

O relatório de estado de funcionamento de back-end reflete o resultado da sonda de estado de funcionamento do Gateway de aplicação para as instâncias de back-end. Quando pesquisar é efetuada com êxito e o back-end pode receber tráfego, que é considerado em bom estado. Caso contrário, considera-se danificado.

> [!IMPORTANT]
> Se existir um grupo de segurança de rede (NSG) numa sub-rede de Gateway de aplicação, abra o intervalos de portas 65503 65534 a sub-rede do Gateway de aplicação para o tráfego de entrada. Estas portas são necessárias para o estado de funcionamento do back-end API para trabalhar.


### <a name="view-back-end-health-through-the-portal"></a>Ver estado de funcionamento de back-end através do portal

No portal do Estado de funcionamento de back-end é fornecido automaticamente. Num gateway de aplicação existente, selecione **monitorização** > **estado de funcionamento de back-end**. 

Cada membro do conjunto de back-end está listado nesta página (quer seja um NIC, IP ou FQDN). Nome do conjunto de back-end, porta, o nome de definições HTTP de back-end e estado de funcionamento são apresentadas. Os valores válidos para o estado de funcionamento são **bom estado de funcionamento**, **mau estado de funcionamento**, e **desconhecido**.

> [!NOTE]
> Se vir um Estado de funcionamento de back-end das **desconhecido**, certifique-se de que o acesso ao back-end não é bloqueado por uma regra NSG, uma rota definida pelo utilizador (UDR) ou um DNS personalizado na rede virtual.

![Estado de funcionamento de back-end][10]

### <a name="view-back-end-health-through-powershell"></a>Ver estado de funcionamento de back-end através do PowerShell

O código do PowerShell seguinte mostra como visualizar o estado de funcionamento de back-end utilizando a `Get-AzureRmApplicationGatewayBackendHealth` cmdlet:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli-20"></a>Ver estado de funcionamento de back-end através do Azure CLI 2.0

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultados

O fragmento seguinte mostra um exemplo de resposta:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Registos de diagnóstico

Pode utilizar diferentes tipos de registos no Azure para gerir e resolver problemas de gateways de aplicação. Pode aceder a algumas destes registos através do portal. Todos os registos podem ser extraídos de Blob storage do Azure e visualizados em diferentes ferramentas, tal como [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel e o Power BI. Pode saber mais sobre os diferentes tipos de registos na lista seguinte:

* **Registo de atividade**: pode utilizar [registos de atividade do Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecido como registos de auditoria e registos operacionais) para ver todas as operações que são enviadas para a sua subscrição do Azure e o respetivo estado. Entradas de registo de atividade são recolhidas por predefinição, e pode visualizá-las no portal do Azure.
* **Registo de acesso**: pode utilizar este registo para ver os padrões de acesso de Gateway de aplicação e analisar informações importantes, incluindo o IP, URL pedido, latência de resposta, código de retorno e bytes da função invocadora e terminar. Um registo de acesso é recolhido a cada 300 segundos. Este registo contém um registo por instância de Gateway de aplicação. A instância de Gateway de aplicação pode ser identificada pela propriedade instanceId.
* **Registo de desempenho**: pode utilizar este registo para ver como instâncias de Gateway de aplicação estiver a efetuar. Este registo captura informações de desempenho para cada instância, incluindo o total de pedidos servidos, débito em bytes, total de pedidos servidos, contagem de pedidos falhados e contagem de instâncias de back-end de bom estado de funcionamento e mau estado de funcionamento. Um registo de desempenho é recolhido a cada 60 segundos.
* **Registo de firewall**: pode utilizar este registo para ver os pedidos que são registados através do modo de deteção ou prevenção de um gateway de aplicação que está configurado com a firewall de aplicação web.

> [!NOTE]
> Os registos estão disponíveis apenas para os recursos implementados no modelo de implementação Azure Resource Manager. Não é possível utilizar os registos de recursos de no modelo de implementação clássica. Para uma melhor compreensão dos dois modelos, consulte o [Gestor de recursos de compreender implementação clássica e](../azure-resource-manager/resource-manager-deployment-model.md) artigo.

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: contas do Storage são melhor utilizadas para os registos quando os registos são armazenados durante um longo período de tempo e revistos quando necessário.
* **Os Event hubs**: os Event hubs são uma excelente opção para integrar com outras informações de segurança e das ferramentas de gestão de eventos (SEIM) para obter alertas nos seus recursos.
* **Análise de registo**: análise de registos melhor é utilizada para a monitorização em tempo real gerais da sua aplicação ou ao procurar tendências.

### <a name="enable-logging-through-powershell"></a>Ativar o registo através do PowerShell

Registo de atividade é ativado automaticamente para todos os recursos do Resource Manager. Tem de ativar o acesso e o registo para iniciar a recolha de dados disponíveis através desses registos de desempenho. Para ativar o registo, utilize os seguintes passos:

1. Tenha em atenção o ID de recurso da sua conta do storage, onde os dados de registo são armazenados. Este valor é o formato: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>. Pode utilizar qualquer conta de armazenamento na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações.

    ![Portal: ID de recurso conta de armazenamento](./media/application-gateway-diagnostics/diagnostics1.png)

2. Tenha em atenção o ID de recurso do gateway de aplicação para o qual o registo está ativado. Este valor é o formato: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/applicationGateways/\<nome do gateway de aplicação\>. Pode utilizar o portal para encontrar estas informações.

    ![Portal: ID de recurso para o gateway de aplicação](./media/application-gateway-diagnostics/diagnostics2.png)

3. Ative o registo de diagnóstico, utilizando o cmdlet PowerShell seguinte:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Registos de atividade não necessitam de uma conta de armazenamento separada. A utilização do armazenamento de acesso e o registo de desempenho incorreu encargos de serviço.

### <a name="enable-logging-through-the-azure-portal"></a>Ativar o registo através do portal do Azure

1. No portal do Azure, localizar o recurso e clique em **registos de diagnóstico**.

   Gateway de aplicação, estão disponíveis três registos:

   * Registo de acesso
   * Registo de desempenho
   * Registo de firewall

2. Para iniciar a recolha de dados, clique em **ative os diagnósticos**.

   ![Ativar o diagnóstico][1]

3. O **as definições de diagnóstico** painel fornece as definições para os registos de diagnóstico. Neste exemplo, análise de registos armazena os registos. Clique em **configurar** em **Log Analytics** para configurar a sua área de trabalho. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![Iniciar o processo de configuração][2]

4. Escolha uma área de trabalho do Operations Management Suite (OMS) existente ou crie um novo. Este exemplo utiliza um existente.

   ![Opções para áreas de trabalho do OMS][3]

5. Confirme as definições e clique em **guardar**.

   ![Painel de definições de diagnóstico com seleções][4]

### <a name="activity-log"></a>Registo de atividades

Por predefinição, o Azure gera o registo de atividade. Os registos são preservados durante 90 dias no arquivo de registos de eventos do Azure. Saiba mais sobre estes registos o lendo o [ver eventos e registo de atividade](../monitoring-and-diagnostics/insights-debugging-with-events.md) artigo.

### <a name="access-log"></a>Registo de acesso

O registo de acesso é gerado apenas se tiver ativado, cada instância de Gateway de aplicação, conforme especificado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. Cada acesso do Gateway de aplicação é registado no formato JSON, conforme mostrado no exemplo seguinte:


|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância de Gateway de aplicação que serviu o pedido.        |
|ClientIP     | IP de origem para o pedido.        |
|clientPort     | Porta de origem para o pedido.       |
|httpMethod     | Método HTTP utilizado por pedido.       |
|requestUri     | URI do pedido recebido.        |
|RequestQuery     | **Encaminhados por servidor**: instância de conjunto de Back-end que lhe foi enviada o pedido. </br> **X-AzureApplicationGateway-registo-ID**: ID de correlação utilizada no pedido. Pode ser utilizado para resolver problemas de tráfego em servidores de back-end. </br>**Estado do servidor**: código de resposta HTTP recebido de Gateway de aplicação de back-end.       |
|UserAgent     | Agente de utilizador do cabeçalho do pedido HTTP.        |
|httpStatus     | Código de estado HTTP devolvido para o cliente do Gateway de aplicação.       |
|httpVersion     | Versão HTTP do pedido.        |
|receivedBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Período de tempo (em milissegundos) que demora para um pedido de processamento e a respetiva resposta para serem enviados. Esta é calculada como o intervalo de tempo quando o Gateway de aplicação recebe o primeiro byte de um pedido HTTP para a hora quando a resposta enviar a operação depois de concluída. É importante ter em atenção que o campo Time-Taken inclui normalmente o tempo que os pacotes de pedido e resposta são estiverem em deslocação através da rede. |
|sslEnabled| Indica se a comunicação para os conjuntos de back-end utilizados SSL. Os valores válidos são e desativar.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>Registo de desempenho

O registo de desempenho é gerado apenas se tiver ativado, cada instância de Gateway de aplicação, conforme especificado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. Os dados de registo de desempenho são gerados em intervalos de 1 minuto. Os seguintes dados são registados:


|Valor  |Descrição  |
|---------|---------|
|instanceId     |  Instância de Gateway de aplicação para o desempenho de dados está a ser gerados. Para um gateway de aplicação de várias instâncias, há uma linha por instância.        |
|healthyHostCount     | Número de anfitriões bom estado de funcionamento no conjunto de back-end.        |
|unHealthyHostCount     | Número de anfitriões mau estado de funcionamento no conjunto de back-end.        |
|requestCount     | Número de pedidos servidos.        |
|latência | Latência (em milissegundos) de pedidos da instância para o back-end que serve os pedidos. |
|failedRequestCount| Número de pedidos falhados.|
|Débito| Débito médio desde o último registo, medido em bytes por segundo.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Latência é calculada desde o momento em que o primeiro byte do pedido HTTP é recebeu a hora do último byte da resposta HTTP é enviado. É a soma do tempo de processamento de Gateway de aplicação, o custo de rede para o back-end e o tempo que demora o back-end para processar o pedido.

### <a name="firewall-log"></a>Registo de firewall

O registo de firewall é gerado apenas se tiver ativado-la para cada gateway de aplicação, conforme especificado nos passos anteriores. Este registo também requer que a firewall de aplicação web está configurada num gateway de aplicação. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. Os seguintes dados são registados:


|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância de Gateway de aplicação para que firewall dados está a ser gerados. Para um gateway de aplicação de várias instâncias, há uma linha por instância.         |
|clientIp     |   IP de origem para o pedido.      |
|clientPort     |  Porta de origem para o pedido.       |
|requestUri     | URL do pedido recebido.       |
|ruleSetType     | Tipo de conjunto de regras. O valor disponível é OWASP.        |
|ruleSetVersion     | O conjunto de regras de versão utilizada. Os valores disponíveis são 2.2.9 e 3.0.     |
|ruleId     | ID de regra do evento de acionamento.        |
|Mensagem     | Mensagem amigável de utilizador para o evento de acionamento. Obter mais detalhes são fornecidos na secção de detalhes.        |
|Ação     |  Ação tomada no pedido. Os valores disponíveis são bloqueado e permitido.      |
|site     | Site para o qual o registo foi gerado. Atualmente, apenas Global está listado porque as regras são globais.|
|Detalhes     | Detalhes do evento de acionamento.        |
|details.Message     | Descrição da regra.        |
|details.data     | Foram encontrados pedido que correspondeu à regra de dados específicos.         |
|details.File     | Ficheiro de configuração que continha a regra.        |
|details.line     | Número de linha no ficheiro de configuração que acionou o evento.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividade

Pode ver e analisar dados de registo de atividade, utilizando qualquer um dos seguintes métodos:

* **Ferramentas do Azure**: obter as informações do registo de atividade através do Azure PowerShell, a CLI do Azure, a API de REST do Azure ou o portal do Azure. Instruções passo a passo para cada método passo são detalhadas no [operações de atividade com o Resource Manager](../azure-resource-manager/resource-group-audit.md) artigo.
* **Power BI**: Se ainda não tiver um [Power BI](https://powerbi.microsoft.com/pricing) conta, pode experimentar, gratuitamente. Utilizando o [conteúdo de registos de atividade do Azure pack para o Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), pode analisar os dados com dashboards pré-configuradas que podem ser utilizados como está, ou personalizar.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Ver e analisar o acesso, o desempenho e a firewall registos

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) pode recolher os ficheiros de registo de eventos e contadores da sua conta de armazenamento de Blobs. Inclui visualizações e capacidades de pesquisa poderosa para analisar os registos.

Também pode ligar à sua conta do storage e obter as entradas de registo JSON para os registos de acesso e o desempenho. Depois de transferir os ficheiros JSON, pode convertê-las em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com conceitos básicos do alterar os valores de constantes e variáveis em c# e o Visual Studio, pode utilizar o [iniciar ferramentas de conversor](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis a partir do GitHub.
> 
> 

## <a name="metrics"></a>Métricas

As métricas são uma funcionalidade de certos recursos do Azure, onde pode ver os contadores de desempenho no portal. Gateway de aplicação, uma métrica está actualmente disponível. Esta métrica é o débito e pode vê-lo no portal. Navegue para um gateway de aplicação e clique em **métricas**. Para ver os valores, selecione o débito no **as métricas disponíveis** secção. Na imagem seguinte, pode ver um exemplo com os filtros que pode utilizar para apresentar os dados em intervalos de tempo diferente.

![Vista de métrica com filtros][5]

Para ver uma lista atual de métricas, consulte [suportado métricas com a monitorização do Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

### <a name="alert-rules"></a>Regras de alertas

Pode começar a regras de alertas com base nas métricas para um recurso. Por exemplo, um alerta pode chamar um webhook ou um administrador de e-mail, se o débito do gateway de aplicação é acima, abaixo ou com um limiar durante um período especificado.

O exemplo seguinte explica-lhe criar uma regra de alerta que envia uma mensagem de e-mail a um administrador depois de falhas de débito um limiar:

1. Clique em **Adicionar alerta métrica** para abrir o **Adicionar regra** painel. Também pode contactar este painel a partir do painel de métricas.

   ![Botão "Adicionar alerta métrica"][6]

2. No **Adicionar regra** painel, preencha o nome, a condição, notificar secções e clique em **OK**.

   * No **condição** Seletor, selecione uma das quatro valores: **maior**, **igual ou maior do que**, **inferior a**, ou **menor ou igual a**.

   * No **período** Seletor, selecione um período de 5 minutos para 6 horas.

   * Se selecionar **proprietários, contribuintes e leitores de E-Mail**, mensagem de correio eletrónico pode ser dinâmica com base nos utilizadores que têm acesso a esse recurso. Caso contrário, pode fornecer uma lista separada por vírgulas de utilizadores no **administrador adicionais email(s)** caixa.

   ![Adicionar o painel de regra][7]

Se o limiar é quebrado, chega uma mensagem de e-mail que é semelhante na imagem seguinte:

![Correio eletrónico para o limiar infringido][8]

É apresentada uma lista de alertas depois de criar um alerta de métrico. Fornece uma descrição geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre as notificações de alerta, consulte o artigo [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Para saber mais sobre webhooks e como pode utilizá-los com alertas, visite [configurar um webhook num alerta métrico Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="next-steps"></a>Passos seguintes

* Visualizar registos de eventos e contadores utilizando [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Visualizar o registo de atividade do Azure com Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogue.
* [Ver e analisar registos de atividade do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogue.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
