---
title: "Ver registos de diagnóstico para o Azure Data Lake Store | Microsoft Docs"
description: "Compreender como o programa de configuração e aceder a registos de diagnóstico para o Azure Data Lake Store "
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: f6496fb62670c480ce543a51225856f0fb5d89b5
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Aceder a registos de diagnóstico para o Azure Data Lake Store
Saiba mais ativar o diagnóstico de registo para a sua conta do Data Lake Store e ver os registos recolhidos para a sua conta.

As organizações podem ativar o registo de diagnóstico para a conta do Azure Data Lake Store recolher registos de auditoria de acesso de dados que fornece informações como a lista de utilizadores que acedem aos dados, frequência os dados são acedidos, quantidade de dados é armazenado na conta, etc.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Conta do Azure Data Lake Store**. Siga as instruções em [Introdução ao Azure Data Lake Store com o Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Ativar o registo de diagnóstico para a sua conta do Data Lake Store
1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Abra a sua conta do Data Lake Store e, no painel de conta do Data Lake Store, clique em **definições**e, em seguida, clique em **registos de diagnóstico**.
3. No **registos de diagnóstico** painel, clique em **ative os diagnósticos**.

    ![Ativar o registo de diagnóstico](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "ativar registos de diagnóstico")

3. No **diagnóstico** painel, efetue as alterações seguintes para configurar o registo de diagnóstico.
   
    ![Ativar o registo de diagnóstico](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "ativar registos de diagnóstico")
   
   * Para **nome**, introduza um valor para a configuração de registo de diagnóstico.
   * Pode optar por loja/processe os dados de formas diferentes.
     
        * Selecione a opção para **arquivo para uma conta de armazenamento** para armazenar os registos para uma conta de armazenamento do Azure. Utilize esta opção se pretende arquivar os dados que serão processado de lote numa data posterior. Se selecionar esta opção tem de fornecer uma conta de armazenamento do Azure para guardar os registos para.
        
        * Selecione a opção para **fluxo para um hub de eventos** aos dados de registo de fluxo para um Hub de eventos do Azure. Provavelmente irá utilizar esta opção se tiver um pipeline de processamento a jusante para analisar os registos de entrada em tempo real. Se selecionar esta opção, tem de fornecer os detalhes para o Hub de eventos do Azure que pretende utilizar.

        * Selecione a opção para **enviar ao Log Analytics** para utilizar o serviço de análise de registos do Azure para analisar os dados de registo gerado. Se selecionar esta opção, tem de fornecer os detalhes para a área de trabalho do Operations Management Suite que teria de utilizar a análise de registos de executar.
     
   * Especifique se pretende obter registos de auditoria ou registos de pedidos ou ambos.
   * Especifique o número de dias para o qual os dados devem ser retidos. Retenção só é aplicável se estiver a utilizar a conta de armazenamento do Azure para arquivar dados de registo.
   * Clique em **Guardar**.

Assim que tiver ativado as definições de diagnóstico, pode ver os registos no **os registos de diagnóstico** separador.

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Ver registos de diagnóstico para a sua conta do Data Lake Store
Existem duas formas para ver os dados de registo para a sua conta do Data Lake Store.

* Da vista de definições de conta de Data Lake Store
* Da conta do Storage do Azure onde os dados são armazenados

### <a name="using-the-data-lake-store-settings-view"></a>Utilizar a vista de definições do Data Lake Store
1. Da sua conta do Data Lake Store **definições** painel, clique em **os registos de diagnóstico**.
   
    ![Registo de diagnóstico de vista](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "ver registos de diagnóstico") 
2. No **os registos de diagnóstico** painel, deverá ver os registos categorizados pelo **registos de auditoria** e **pedido registos**.
   
   * Registos de pedidos capturam todos os pedidos de API efetuados na conta de Data Lake Store.
   * Os registos de auditoria são semelhantes aos registos de pedidos mas fornece uma repartição muito mais detalhada das operações que está a ser executadas na conta de Data Lake Store. Por exemplo, uma chamada de API de carregamento único nos registos de pedido poderá resultar em várias operações de "Acrescentar" nos registos de auditoria.
3. Para transferir os registos, clique em de **transferir** ligação em relação a cada entrada de registo.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Da conta do Storage do Azure que contém dados de registo
1. Abrir o painel de conta de armazenamento do Azure associado ao Data Lake Store para o registo e, em seguida, clique em Blobs. O **serviço Blob** painel apresenta uma lista de dois contentores.
   
    ![Registo de diagnóstico de vista](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "ver registos de diagnóstico")
   
   * O contentor **insights-registos de auditoria** contém os registos de auditoria.
   * O contentor **pedidos de registos insights** com os registos de pedido.
2. Dentro destes contentores, os registos são armazenados sob a seguinte estrutura.
   
    ![Registo de diagnóstico de vista](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "ver registos de diagnóstico")
   
    Por exemplo, pode ser o caminho completo para um registo de auditoria`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Da mesma forma, pode ser o caminho completo para um registo de pedido`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreender a estrutura dos dados de registo
Os registos de auditoria e pedido são num formato JSON. Nesta secção, observe a estrutura de JSON para o pedido e registos de auditoria.

### <a name="request-logs"></a>Registos de pedido
Eis uma entrada de exemplo no registo de pedido formatada em JSON. Cada blob tem um objeto de raiz denominado **registos** que contém uma matriz de objetos de registo.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema de registo de pedido
| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |O carimbo (em UTC) do registo |
| resourceId |Cadeia |O ID do recurso que demorou a operação de colocar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **pedidos**. |
| operationName |Cadeia |Nome da operação que tem sessão iniciada. Por exemplo, getfilestatus. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| callerIpAddress |Cadeia |O endereço IP do cliente que efetua o pedido |
| correlationId |Cadeia |O id do registo que podem ser utilizado para agrupar um conjunto de entradas de registo relacionados |
| identidade |Objeto |A identidade que gerou o registo |
| propriedades |JSON |Consulte abaixo para obter detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades de registo de pedido
| Nome | Tipo | Descrição |
| --- | --- | --- |
| httpMethod |Cadeia |O método de HTTP utilizado para a operação. Por exemplo, GET. |
| Caminho |Cadeia |O caminho a operação foi efetuado em |
| RequestContentLength |Int |O comprimento do conteúdo do pedido HTTP |
| clientRequestId |Cadeia |O Id que identifica exclusivamente este pedido |
| StartTime |Cadeia |A hora em que o servidor recebeu o pedido |
| endTime |Cadeia |A hora em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria
Eis uma entrada de exemplo no registo de auditoria formatada em JSON. Cada blob tem um objeto de raiz denominado **registos** que contém uma matriz de objetos de registo

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de registo de auditoria
| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |O carimbo (em UTC) do registo |
| resourceId |Cadeia |O ID do recurso que demorou a operação de colocar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **auditoria**. |
| operationName |Cadeia |Nome da operação que tem sessão iniciada. Por exemplo, getfilestatus. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| correlationId |Cadeia |O id do registo que podem ser utilizado para agrupar um conjunto de entradas de registo relacionados |
| identidade |Objeto |A identidade que gerou o registo |
| propriedades |JSON |Consulte abaixo para obter detalhes |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria
| Nome | Tipo | Descrição |
| --- | --- | --- |
| StreamName |Cadeia |O caminho a operação foi efetuado em |

## <a name="samples-to-process-the-log-data"></a>Exemplos para processar os dados de registo
O Azure Data Lake Store fornece um exemplo sobre como processar e analisar os dados de registo. Pode encontrar o exemplo em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Consultar também
* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)

