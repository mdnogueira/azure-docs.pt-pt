---
title: "Ver registos de diagnóstico para o Azure Data Lake Analytics | Microsoft Docs"
description: "Compreender como o programa de configuração e aceder a registos de diagnóstico para o Azure Data Lake analytics "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/08/2017
ms.author: larryfr
ms.openlocfilehash: 5bab7a0646d34de3b6d71370a0fa4216845ee6a2
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Aceder a registos de diagnóstico para o Azure Data Lake Analytics

Registo de diagnóstico permite-lhe recolher registos de auditoria de acesso de dados. Estes registos fornecem informações tais como:

* Uma lista de utilizadores que os dados acedidos.
* Frequência os dados são acedidos.
* Quantidade de dados é armazenado na conta.

## <a name="enable-logging"></a>Ativar registo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Abra a sua conta do Data Lake Analytics e selecione **registos de diagnóstico** do __Monitor__ secção. Em seguida, selecione __ative os diagnósticos__.

    ![Ative os diagnósticos para recolher a auditoria e registos de pedidos](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. De __as definições de diagnóstico__, introduza um __nome__ para esta configuração de registo e as opções de registo, em seguida, selecione.

    ![Ative os diagnósticos para recolher a auditoria e registos de pedidos](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "ativar registos de diagnóstico")

   * Pode optar por loja/processe os dados de três formas diferentes.

     * Selecione __arquivo para uma conta de armazenamento__ para armazenar os registos de uma conta de armazenamento do Azure. Utilize esta opção se pretende arquivar os dados. Se selecionar esta opção, tem de fornecer uma conta de armazenamento do Azure para guardar os registos para.

     * Selecione **fluxo para um Hub de eventos** aos dados de registo de fluxo para um Hub de eventos do Azure. Utilize esta opção se tiver um pipeline de processamento a jusante que está a analisar os registos de entrada em tempo real. Se selecionar esta opção, tem de fornecer os detalhes para o Hub de eventos do Azure que pretende utilizar.

     * Selecione __enviar ao Log Analytics__ para enviar os dados para o serviço de análise de registos. Utilize esta opção se pretender utilizar a análise de registos para recolher e analisar registos.
   * Especifique se pretende obter registos de auditoria ou registos de pedidos ou ambos.  Um registo de pedido captura todos os pedidos de API. Um registo de auditoria regista todas as operações que são acionadas por esse pedido de API.

   * Para __arquivo para uma conta de armazenamento__, especifique o número de dias a manter os dados.

   * Clique em __Guardar__.

        > [!NOTE]
        > Tem de selecionar um __arquivo para uma conta de armazenamento__, __fluxo para um Hub de eventos__ ou __enviar ao Log Analytics__ antes de clicar no __guardar__ botão.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Utilizar a conta de armazenamento do Azure que contém dados de registo

1. Para apresentar os contentores de BLOBs que contêm dados de registo, abra a conta de armazenamento do Azure utilizada para o Data Lake Analytics para o registo e, em seguida, clique em __Blobs__.

   * O contentor **insights-registos de auditoria** contém os registos de auditoria.
   * O contentor **pedidos de registos insights** com os registos de pedido.

2. Dentro os contentores, os registos são armazenados sob a seguinte estrutura de ficheiros:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > O `##` entradas no caminho de contenham o ano, mês, dia e hora em que o registo foi criado. O Data Lake Analytics cria a um ficheiro de cada hora, por isso, `m=` sempre contém um valor de `00`.

    Por exemplo, pode ser o caminho completo para um registo de auditoria:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Da mesma forma, pode ser o caminho completo para um registo de pedido:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estrutura do registo

Os registos de auditoria e pedido são num formato JSON structured.

### <a name="request-logs"></a>Registos de pedido

Eis uma entrada de exemplo no registo de pedido formatada em JSON. Cada blob tem um objeto de raiz denominado **registos** que contém uma matriz de objetos de registo.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema de registo de pedido

| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |O carimbo (em UTC) do registo |
| resourceId |Cadeia |O identificador do recurso que demorou a operação de colocar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **pedidos**. |
| operationName |Cadeia |Nome da operação que tem sessão iniciada. Por exemplo, GetAggregatedJobHistory. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| callerIpAddress |Cadeia |O endereço IP do cliente que efetua o pedido |
| correlationId |Cadeia |O identificador do registo. Este valor pode ser utilizado para agrupar um conjunto de entradas de registo relacionados. |
| identidade |Objeto |A identidade que gerou o registo |
| propriedades |JSON |Consulte a secção seguinte (esquema de propriedades de registo de pedido) para obter detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades de registo de pedido

| Nome | Tipo | Descrição |
| --- | --- | --- |
| httpMethod |Cadeia |O método de HTTP utilizado para a operação. Por exemplo, GET. |
| Caminho |Cadeia |O caminho a operação foi efetuado em |
| RequestContentLength |Int |O comprimento do conteúdo do pedido HTTP |
| clientRequestId |Cadeia |O identificador que identifica exclusivamente este pedido |
| StartTime |Cadeia |A hora em que o servidor recebeu o pedido |
| endTime |Cadeia |A hora em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria

Eis uma entrada de exemplo no registo de auditoria formatada em JSON. Cada blob tem um objeto de raiz denominado **registos** que contém uma matriz de objetos de registo.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de registo de auditoria

| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |O carimbo (em UTC) do registo |
| resourceId |Cadeia |O identificador do recurso que demorou a operação de colocar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **auditoria**. |
| operationName |Cadeia |Nome da operação que tem sessão iniciada. Por exemplo, JobSubmitted. |
| resultType |Cadeia |Subestado para o estado da tarefa (Oeprationname). |
| resultSignature |Cadeia |Detalhes adicionais sobre o estado da tarefa (Oeprationname). |
| identidade |Cadeia |O utilizador que a operação solicitada. Por exemplo, susan@contoso.com. |
| propriedades |JSON |Consulte a secção seguinte (esquema de propriedades de registo de auditoria) para obter detalhes |

> [!NOTE]
> **resultType** e **resultSignature** fornecem informações sobre o resultado de uma operação e conter apenas um valor se foi concluída uma operação. Por exemplo, contêm apenas um valor quando **operationName** contém um valor de **JobStarted** ou **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria

| Nome | Tipo | Descrição |
| --- | --- | --- |
| JobId |Cadeia |O ID de atribuído ao trabalho |
| JobName |Cadeia |O nome fornecido para a tarefa |
| JobRunTime |Cadeia |O tempo de execução utilizado para processar a tarefa |
| SubmitTime |Cadeia |O tempo (em UTC) que a tarefa foi submetida |
| StartTime |Cadeia |O tempo que a tarefa começou a ser executada após submissão (em UTC) |
| endTime |Cadeia |O tempo que a tarefa foi concluída |
| Paralelismo |Cadeia |O número de unidades do Data Lake Analytics solicitado para esta tarefa durante a submissão |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, e **paralelismo** fornecem informações sobre uma operação. Estas entradas contenham apenas um valor se que a operação foi iniciada ou foi concluída. Por exemplo, **SubmitTime** contém apenas um valor após **operationName** tem o valor **JobSubmitted**.

## <a name="process-the-log-data"></a>Processar os dados de registo

Azure Data Lake Analytics fornece um exemplo sobre como processar e analisar os dados de registo. Pode encontrar o exemplo em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
