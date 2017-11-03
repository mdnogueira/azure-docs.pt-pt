---
title: "Os registos de diagnóstico do Azure | Microsoft Docs"
description: "Cliente pode ativar a análise de registos para a CDN do Azure."
services: cdn
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: 3e8727e80571be70124fb439f4c7e448f521b692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Com os registos de diagnóstico do Azure, pode ver a análise de núcleo e guardá-las para um ou mais destinos, incluindo:

 - Conta de armazenamento do Azure
 - Azure Event Hubs
 - [Repositório de análise de registos do OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Esta funcionalidade está disponível para todos os pontos finais da CDN que pertencem da Verizon (Standard e Premium) e perfis da CDN Akamai (Standard). 

Registos de diagnóstico do Azure permitem-lhe exportar métricas de utilização básica do ponto final de CDN para uma variedade de origens de modo a que pode aceder aos mesmos de forma personalizada. Por exemplo, pode efetuar os seguintes tipos de exportação de dados:

- Exporte dados para armazenamento de BLOBs, exportar para CSV e gerar gráficos no Excel.
- Exportar dados para Event Hubs e estar relacionados com a dados a partir de outros serviços do Azure.
- Exportar dados para análise de registo e ver os dados no seu próprio espaço de trabalho do OMS

A figura seguinte mostra uma vista de análise de núcleo CDN típica de dados.

![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1 - vista de análise de núcleo CDN*

Para mais informações sobre os registos de diagnóstico, consulte [os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-azure-portal"></a>Ativar o registo com o portal do Azure

Siga estes ativar passos registo com a análise de núcleo CDN:

Inicie sessão no [Portal do Azure](http://portal.azure.com). Se ainda não tiver ativado para o fluxo de trabalho CDN [ativar a CDN do Azure](cdn-create-new-endpoint.md) antes de continuar.

1. No portal, navegue para **perfil da CDN**.
2. Selecione um perfil CDN, em seguida, selecione o ponto final da CDN para a qual pretende ativar **registos de diagnóstico**.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selecione **registos de diagnóstico** no **monitorização** secção.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Ativar o registo com o Storage do Azure
    
1. Para utilizar o armazenamento do Azure para armazenar os registos, selecione **arquivo para uma conta de armazenamento**, selecione **CoreAnalytics**e, em seguida, escolha o número de dias de retenção em **retenção (dias)**. Uma retenção de zero dias armazena os registos indefinidamente. 
2. Introduza um nome para a definição, em seguida, clique em **conta de armazenamento**. Depois de selecionar uma conta de armazenamento, clique em **guardar**.

![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Figura 2 - registo com o Storage do Azure*

### <a name="logging-with-oms-log-analytics"></a>Registo de análise de registos do OMS

Para utilizar a análise de registos do OMS para armazenar os registos, siga estes passos:

1. Do **registos de diagnóstico** painel, selecione **enviar ao Log Analytics**. 

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Clique em **configurar** para configurar o registo de análise do registo. Na caixa de diálogo de áreas de trabalho do OMS, pode selecionar uma área de trabalho anterior ou crie um novo.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Clique em **criar nova área de trabalho**.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/07_Create-new.png)

4. Introduza um novo nome de área de trabalho do OMS. Um nome de área de trabalho do OMS tem de ser exclusivos e conter apenas letras, números e hífenes; não são permitidos espaços e carateres de sublinhado. 
5. Em seguida, selecione uma subscrição existente, o grupo de recursos (novo ou existente), a localização e o escalão de preço. Também tem a opção de afixação esta configuração ao dashboard. Clique em **OK** para concluir a configuração.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  Depois de criar a sua área de trabalho, são devolvidos para os windows de registos de diagnóstico. Confirme o nome da sua área de trabalho de análise de registo novo.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Assim que tiver configurado a configuração de análise do registo, certifique-se de que selecionou **CoreAnalytics**.

6. Clique em **Guardar**.

7. Para ver a nova área de trabalho do OMS, aceda ao seu dashboard do portal do Azure e clique no nome da sua área de trabalho de análise do registo. Clique no mosaico do Portal do OMS para ver a sua área de trabalho no repositório de OMS. 

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    O repositório do OMS está agora pronto para iniciar a sessão de dados. Para consumir dados, tem de utilizar um [OMS solução](#consuming-oms-log-analytics-data), abrangidas neste artigo.

Para obter mais informações sobre os atrasos de dados de registo, consulte [atrasos de dados de registo](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Ativar o registo com o PowerShell

O exemplo seguinte mostra como ativar os registos de diagnóstico através de Cmdlets do PowerShell do Azure.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Ativar o diagnóstico inicia sessão numa conta do Storage

Primeiro início de sessão e selecionar uma subscrição:

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Para ativar os registos de diagnóstico numa conta do Storage, utilize este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Para ativar registos de diagnóstico numa área de trabalho do OMS, utilize este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumir os registos de diagnóstico do armazenamento do Azure
Esta secção descreve o esquema da análise de núcleo CDN, como está organizada dentro de uma conta de armazenamento do Azure e fornece o código de exemplo para transferir os registos de um ficheiro CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Utilizar o Explorador de armazenamento do Microsoft Azure
Antes de poder aceder os dados de análise de núcleo da conta do Storage do Azure, primeiro precisa de uma ferramenta para aceder ao conteúdo numa conta do storage. Apesar de existirem várias ferramentas disponíveis no mercado, que recomendamos é o Explorador de armazenamento do Microsoft Azure. Para transferir a ferramenta, consulte [Explorador de armazenamento do Azure](http://storageexplorer.com/). Depois de transferir e instalar o software, configure-a para utilizar a mesma conta de armazenamento do Azure que foi configurada como um destino para os registos de diagnóstico da CDN.

1.  Abra **Explorador de armazenamento do Microsoft Azure**
2.  Localizar a conta de armazenamento
3.  Vá para o **"Contentores de BLOBs"** nós sob este armazenamento conta e expanda o nó
4.  Selecione o contentor com o nome **"insights-registos-coreanalytics"** e faça duplo clique
5.  Resulta Mostrar cópias de segurança no painel da direita, começando pelo primeiro nível, que se parece com **"resourceId ="**. Continuar a clicar em até até ver o ficheiro **PT1H.json**. Consulte a nota seguinte para obter explicações do caminho.
6.  Cada blob **PT1H.json** representa os registos de análise para uma hora para um ponto final de CDN específico ou o domínio personalizado.
7.  O esquema do conteúdo deste ficheiro JSON está descrito na secção de esquema de registos de análise de núcleo


> [!NOTE]
> **Formato de caminho do blob**
> 
> Registos de análise do Core são gerados a cada hora e os dados são recolhidos e armazenados dentro de um único Azure blob como um payload JSON. Porque a ferramenta Explorador de armazenamento interpreta '/' como um separador de diretório e mostra a hierarquia, o caminho para o blob do Azure é apresentado como se houver uma estrutura hierárquica e representa o nome do blob. O nome do blob segue a seguinte convenção de nomenclatura: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descrição de campos:**

|valor|descrição|
|-------|---------|
|ID da subscrição    |ID de subscrição do Azure no formato Guid.|
|Recurso |Nome do nome de grupo do grupo de recursos aos quais pertencem os recursos da CDN.|
|Nome do perfil |Nome do perfil CDN|
|Nome do ponto final |Nome do ponto final de CDN|
|ano|  representação de 4 dígitos do ano, por exemplo, 2017|
|Mês| representação de 2-dígitos do número de meses. 01 = Janeiro... 12 = Dezembro|
|Dia|   representação de 2-dígitos do dia do mês|
|PT1H.JSON| Armazenar os dados de análise real do ficheiro JSON|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportar os dados de análise de núcleo para um ficheiro CSV

Para facilitar a análise de núcleo de acesso, código de exemplo para uma ferramenta é fornecido. Esta ferramenta permite transferir os ficheiros JSON para um formato de ficheiro flat separados por vírgulas, que pode ser utilizado para criar facilmente gráficos ou outras agregações.

Eis como pode utilizar a ferramenta:

1.  Visite a ligação do github: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Transferir o código.
3.  Siga as instruções para compilar e configurar.
4.  Execute a ferramenta.
5.  O ficheiro CSV resultante mostra os dados de análise numa hierarquia simples simple.

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>Consumir os registos de diagnóstico de um repositório de análise de registos do OMS
Análise de registos é um serviço no Operations Management Suite (OMS) que monitoriza a sua nuvem e no local ambientes para manter a respetiva disponibilidade e desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens. 

Para utilizar a análise de registos, terá [ativar o registo](#enable-logging-with-azure-storage) para o repositório de análise de registos do Azure OMS, que é abordado anteriormente neste artigo.

### <a name="using-the-oms-repository"></a>Utilizar o repositório do OMS

 O diagrama seguinte mostra a arquitetura de entradas e saídas do repositório:

![Repositório de análise de registo do OMS](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3 - repositório de análise do registo*

Pode visualizar os dados de diversas formas utilizando soluções de gestão. Pode obter soluções de gestão do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Pode instalar as soluções de gestão do Azure marketplace clicando a **obtê-lo agora** ligação na parte inferior de cada solução.

### <a name="adding-an-oms-cdn-management-solution"></a>Adicionar uma solução de gestão da CDN do OMS

Siga estes passos para adicionar uma solução de gestão:

1.   Se ainda não o fez, inicie sessão no portal do Azure com a sua subscrição do Azure e aceda ao Dashboard.
    ![Dashboard do Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. No **novo** painel em **Marketplace**, selecione **monitorização + gestão**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. No **monitorização + gestão** painel, clique em **ver todos os**.

    ![Ver tudo](./media/cdn-diagnostics-log/15_See-all.png)

4.  Procure CDN na caixa de pesquisa.

    ![Ver tudo](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Selecione **análise de núcleo da CDN do Azure**. 

    ![Ver tudo](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  Depois de clicar em **criar**, será pedido para criar uma área de trabalho do OMS nova ou utilize uma já existente. 

    ![Ver tudo](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Selecione a área de trabalho criado antes de. Em seguida, terá de adicionar uma conta de automatização.

    ![Ver tudo](./media/cdn-diagnostics-log/19_Add-automation.png)

8. O ecrã seguinte mostra a forma de conta de automatização, terá de preencher enviados. 

    ![Ver tudo](./media/cdn-diagnostics-log/20_Automation.png)

9. Assim que tiver criado a conta de automatização, está pronto para adicionar a sua solução. Clique no botão **Criar**.

    ![Ver tudo](./media/cdn-diagnostics-log/21_Ready.png)

10. A solução foi adicionada à sua área de trabalho. Regressar ao dashboard do portal do Azure.

    ![Ver tudo](./media/cdn-diagnostics-log/22_Dashboard.png)

    Clique em da área de trabalho de análise de registos que criou para aceder à sua área de trabalho. 

11. Clique em de **Portal do OMS** mosaico para obter a sua solução de novo no portal do OMS.

    ![Ver tudo](./media/cdn-diagnostics-log/23_workspace.png)

12. O portal do OMS deverá agora parecer-semelhante do ecrã seguinte:

    ![Ver tudo](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Clique dos mosaicos para ver várias vistas sobre os seus dados.

    ![Ver tudo](./media/cdn-diagnostics-log/25_Interior-view.png)

    Pode se deslocar para a esquerda ou direita para ver mais mosaicos que representa vistas individuais para os dados. 

    Clicar nos mosaicos dá-lhe obter mais detalhes sobre os dados.

     ![Ver tudo](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ofertas e escalões de preços

Pode ver ofertas e escalões de preços para soluções de gestão do OMS [aqui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Personalizar vistas

Pode personalizar a vista sobre os seus dados através da utilização de **estruturador de vistas**. Para começar a estruturar, aceda à sua área de trabalho do OMS e clique em de **estruturador de vistas** mosaico.

![Estruturador de Vista](./media/cdn-diagnostics-log/27_Designer.png)

Pode arrastar e largar os tipos de gráficos e preencha os detalhes de dados que pretende analisar.

![Estruturador de Vista](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Atrasos de dados de registo

Atrasos de dados de registo da Verizon | Atrasos de dados de registo Akamai
--- | ---
Dados de registo da Verizon estão atrasados 1 hora em demorar até 2 horas para iniciar a apresentação após a conclusão da propagação de ponto final. | Dados de registo da Akamai estão atrasados por 24 horas; Se foi criada há mais de 24 horas, que demora até 2 horas para iniciar a apresentação. Se tiver sido recentemente criado, pode demorar até 25 horas para os registos para iniciar a apresentação.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de registo de diagnóstico para análise de núcleo CDN

Estamos atualmente oferecem apenas core registos de análise, que contêm as métricas que mostra as estatísticas de resposta HTTP e estatísticas de saída visto da CDN POPs/margens.

### <a name="core-analytics-metrics-details"></a>Detalhes de métricas de análise de núcleo
A tabela seguinte mostra uma lista de haver métricas disponíveis nos registos de análise de núcleo. Nem todas as métricas estão disponíveis de todos os fornecedores, apesar destas diferenças são mínimas. A tabela seguinte mostra também se uma métrica indicada está disponível a partir de um fornecedor. Tenha em atenção que as métricas estão disponíveis para apenas esses pontos finais da CDN que tenham tráfego nos mesmos.


|Métrica                     | Descrição   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Número total de pedidos de pedido durante este período| Sim  |Sim   |
| RequestCountHttpStatus2xx |Contagem de todos os pedidos que resultaram num código de HTTP 2xx (por exemplo, 200, 202)              | Sim  |Sim   |
| RequestCountHttpStatus3xx | Contagem de todos os pedidos que resultaram num código de HTTP 3xx (por exemplo, 300, 302)              | Sim  |Sim   |
| RequestCountHttpStatus4xx |Contagem de todos os pedidos que resultaram num código de HTTP 4xx (por exemplo, 400, 404)               | Sim   |Sim   |
| RequestCountHttpStatus5xx | Contagem de todos os pedidos que resultaram num código de HTTP 5xx (por exemplo, 500, 504)              | Sim  |Sim   |
| RequestCountHttpStatusOthers |  Contagem de todos os outros códigos HTTP (fora 2xx-5xx) | Sim  |Sim   |
| RequestCountHttpStatus200 | Contagem de todos os pedidos que resultaram numa resposta de código HTTP 200              |Não   |Sim   |
| RequestCountHttpStatus206 | Contagem de todos os pedidos que resultaram numa resposta código 206 HTTP              |Não   |Sim   |
| RequestCountHttpStatus302 | Contagem de todos os pedidos que resultaram numa resposta de código HTTP 302              |Não   |Sim   |
| RequestCountHttpStatus304 |  Contagem de todos os pedidos que resultaram numa resposta código 304 HTTP             |Não   |Sim   |
| RequestCountHttpStatus404 | Contagem de todos os pedidos que resultaram numa resposta de código HTTP 404              |Não   |Sim   |
| RequestCountCacheHit |Contagem de todos os pedidos que resultaram num de acertos na Cache. O recurso tiver sido servido diretamente a partir do POP ao cliente.               | Sim  |Não   |
| RequestCountCacheMiss | Contagem de todos os pedidos que resultaram numa Cache de falha de acerto na. Isto significa que o elemento não foi encontrado no POP mais próximo para o cliente e, por conseguinte, foi obtido da origem.              |Sim   | Não  |
| RequestCountCacheNoCache | Contagem de todos os pedidos para um recurso que impedido de ser colocadas em cache devido a uma configuração de utilizador na extremidade.              |Sim   | Não  |
| RequestCountCacheUncacheable | Contagem de todos os pedidos ativos que são impedidos de ser colocadas em cache, o elemento Cache-Control e cabeçalhos de expira, que indicam que este deve não ser colocadas em cache num POP ou pelo cliente HTTP                |Sim   |Não   |
| RequestCountCacheOthers | Contagem de todos os pedidos com o estado de cache não abrangido por acima.              |Sim   | Não  |
| EgressTotal | Transferência de dados de saída em GB              |Sim   |Sim   |
| EgressHttpStatus2xx | Dados de saída transferência * para respostas com códigos de estado HTTP 2xx em GB            |Sim   |Não   |
| EgressHttpStatus3xx | Transferência de dados de saída para as respostas com códigos de estado HTTP 3xx em GB              |Sim   |Não   |
| EgressHttpStatus4xx | Transferência de dados de saída para as respostas com códigos de estado HTTP 4xx em GB               |Sim   | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para as respostas com códigos de estado HTTP 5xx em GB               |Sim   |  Não |
| EgressHttpStatusOthers | Transferência de dados de saída para as respostas com outros códigos de estado HTTP em GB                |Sim   |Não   |
| EgressCacheHit |  Transferência de dados de saída para as respostas que foram fornecidas diretamente a partir da cache do CDN na CDN POPs/margens  |Sim   |  Não |
| EgressCacheMiss | Transferência de dados de saída para as respostas que não foram encontrado no servidor mais próximo POP e obtidas a partir do servidor de origem              |Sim   |  Não |
| EgressCacheNoCache | Transferem de dados de saída para recursos que são impedidos de ser colocadas em cache devido a uma configuração de utilizador na extremidade.                |Sim   |Não   |
| EgressCacheUncacheable | Transferem de dados de saída para recursos que são impedidos de ser colocadas em cache por Cache-Control e/ou cabeçalhos expira o elemento. Indica que este deve não ser colocadas em cache num POP ou pelo cliente HTTP.                   |Sim   | Não  |
| EgressCacheOthers |  Transferências de dados de saída para obter outros cenários de cache.             |Sim   | Não  |

* Transferência de dados saída refere-se ao tráfego entregue dos servidores POP do CDN ao cliente.


### <a name="schema-of-the-core-analytics-logs"></a>Esquema dos registos de análise de núcleo 

Todos os registos são armazenados no formato JSON e cada entrada tem campos de cadeia, de acordo com o esquema seguinte:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Em que a hora representa a hora de início do limite de hora para o qual as estatísticas é comunicada. Quando uma métrica não é suportada por um fornecedor CDN, em vez de um valor de duplo ou número inteiro, há um valor nulo. Este valor nulo indica a ausência de uma métrica e é diferente do que um valor de 0. Não há um conjunto destas métricas por domínio configurado no ponto final.

Propriedades de exemplo:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Recursos adicionais

* [Registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Análise de núcleo através do portal suplementar CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Análise de registos do OMS do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-overview)
* [Análise de registos do Azure REST API](https://docs.microsoft.com/en-us/rest/api/loganalytics)







