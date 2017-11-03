---
title: "Resolver problemas com registos de diagnóstico do Azure Stream Analytics | Microsoft Docs"
description: "Saiba como analisar os registos de diagnóstico de tarefas do Stream Analytics no Microsoft Azure."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: c9772df2c216d465ca6e90e69bce011969dd4f02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Resolver problemas relacionados com o Azure Stream Analytics, utilizando os registos de diagnóstico

Ocasionalmente, uma tarefa do Azure Stream Analytics inesperadamente deixa de processamento. É importante ser capaz de resolver este tipo de evento. O evento pode ser causado por um resultado de consulta inesperada, conectividade a dispositivos ou uma interrupção inesperada do serviço. Os registos de diagnóstico no Stream Analytics podem ajudar a identificar a causa de problemas quando ocorrer e reduzir o tempo de recuperação.

## <a name="log-types"></a>Tipos de registo

Do Stream Analytics oferece dois tipos de registos: 
* [Registos de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (always on). Registos de atividade dê aprofundadas operações executadas no tarefas.
* [Registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurável). Registos de diagnóstico fornecer mais rica aprofundadas tudo o que acontece com uma tarefa. Diagnóstico regista início quando a tarefa é criada e de fim quando a tarefa é eliminada. Estes incluem eventos quando a tarefa é atualizada e enquanto estiver em execução.

> [!NOTE]
> Pode utilizar os serviços como o Storage do Azure, Event Hubs do Azure e Log Analytics do Azure para analisar dados nonconforming. São-lhe cobrados com base no modelo de preços para esses serviços.
>

## <a name="turn-on-diagnostics-logs"></a>Ativar registos de diagnóstico

Os registos de diagnóstico estão **desativar** por predefinição. Para ativar registos de diagnóstico, conclua estes passos:

1.  Inicie sessão no portal do Azure e aceda ao painel de tarefa de transmissão em fluxo. Em **monitorização**, selecione **registos de diagnóstico**.

    ![Navegação do painel para registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Selecione **ative os diagnósticos**.

    ![Ativar registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  No **as definições de diagnóstico** página, para **estado**, selecione **no**.

    ![Alterar Estado para os registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Configure o arquivo de destino (conta de armazenamento, hub de eventos, análise de registos) que pretende. Em seguida, selecione as categorias de registos que pretende recolher (criação, de execução). 

5.  Guarde a nova configuração de diagnóstico.

A configuração de diagnósticos demora cerca de 10 minutos, entre em vigor. Depois disso, os registos de iniciar a apresentação do arquivo de destino configurado (pode vê-los no **registos de diagnóstico** página):

![Navegação do painel para registos de diagnóstico - destinos de arquivo](./media/stream-analytics-job-diagnostic-logs/image4.png)

Para obter mais informações sobre a configuração de diagnósticos, consulte [recolher e consumir dados de diagnóstico dos seus recursos do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Categorias de registo de diagnóstico

Atualmente, estamos capturar duas categorias de registos de diagnóstico:

* **Criação**. Capturas de registar eventos relacionados com operações de criação da tarefa: tarefa de criação, adição e eliminar entradas e saídas, adicionar e atualizar a consulta, iniciar e parar a tarefa.
* **Execução**. Captura de eventos que ocorrem durante a execução da tarefa:
    * Erros de conectividade
    * Erros de processamento de dados, incluindo:
        * Eventos que não está em conformidade com a definição de consulta (tipos de campo não correspondentes e os valores, em falta campos e assim sucessivamente)
        * Erros de avaliação da expressão
    * Outros eventos e erros

## <a name="diagnostics-logs-schema"></a>Esquema de registos de diagnóstico

Todos os registos são armazenados no formato JSON. Cada entrada tem os seguintes campos de cadeia comuns:

Nome | Descrição
------- | -------
hora | Timestamp (em UTC) do registo.
resourceId | ID do recurso que a operação demorou local, em maiúsculas. Inclui o ID de subscrição, o grupo de recursos e o nome da tarefa. Por exemplo,   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMINGJOBS/STREAMANALYTICS/MYSTREAMINGJOB**.
categoria | Iniciar ou categoria, **execução** ou **criação**.
operationName | Nome da operação que tem sessão iniciada. Por exemplo, **enviar eventos: falha de escrita de saída do SQL Server para mysqloutput**.
status | Estado da operação. Por exemplo, **falha** ou **com êxito**.
nível | Nível de registo. Por exemplo, **erro**, **aviso**, ou **informativo**.
propriedades | Detalhe de entrada específico, serializado como uma cadeia JSON do registo. Para obter mais informações, consulte as secções seguintes.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades de registo de execução

Registos de execução tem informações sobre eventos que ocorreram durante a execução da tarefa de Stream Analytics. O esquema das propriedades varia consoante o tipo de evento. Atualmente, temos os seguintes tipos de registos de execução:

### <a name="data-errors"></a>Erros de dados

Qualquer erro que ocorre durante a tarefa está a processar dados é nesta categoria de registos. Frequentemente, estes registos são criados durante a leitura de dados serialização e operações de escrita. Estes registos não incluem erros de conectividade. Erros de conectividade são tratados como eventos genéricos.

Nome | Descrição
------- | -------
Origem | Nome da tarefa de entrada ou saída onde ocorreu o erro.
Mensagem | Mensagem associada com o erro.
Tipo | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError**, ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém os dados que são útil para localizar com precisão a origem do erro. Sujeitos a truncagem, dependendo do tamanho.

Consoante o **operationName** valor, erros de dados têm o esquema seguinte:
* **Serializar eventos**. Serializar eventos ocorrem durante o evento operações de leitura. Ocorrem quando os dados de entrada não satisfazem o esquema da consulta por um destes motivos:
    * *Incompatibilidade de tipo durante o evento (Alemanha) serializar*: identifica o campo que está a causar o erro.
    * *Não é possível ler um evento, a serialização inválido*: apresenta uma lista de informações sobre a localização dos dados de entrada onde ocorreu o erro. Inclui o nome do blob de entrada de blob, o desvio e um exemplo dos dados.
* **Enviar eventos**. Envie eventos ocorrem durante operações de escrita. Estes identificam o evento de transmissão em fluxo que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Eventos genéricos abrangem tudo o resto.

Nome | Descrição
-------- | --------
Erro | (opcional) Informações de erro. Normalmente, isto é informações de exceção, se estiver disponível.
Mensagem| Mensagem do registo.
Tipo | Tipo de mensagem. É mapeado para categorização interno de erros. Por exemplo, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de correlação | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identificam de forma a execução da tarefa. Todas as entradas de registo de execução desde o momento em que a tarefa é iniciada até interrompe a tarefa tem o mesmo **ID de correlação** valor.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da REST API de gestão de análise de fluxo](https://msdn.microsoft.com/library/azure/dn835031.aspx)
