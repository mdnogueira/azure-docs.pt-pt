---
title: "Utilizar a operação de depuração e os registos de serviço no Stream Analytics | Microsoft Docs"
description: "Registos de operações do Stream Analytics como utilizar"
keywords: "registos do serviço"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: a2ed9676-f0bd-4398-87c8-a592779ac728
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 2ee0871752dc2a3da345339fb826340d44ae48d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Tarefas do Stream Analytics, utilizando o serviço e operação os registos de depuração
Todos os serviços do Azure fornecem as mensagens de registo operacional aos utilizadores para detalhes de registos relacionadas com operações de gestão. No Azure Stream Analytics, estas informações podem ser utilizadas para fins tais como visualizar o estado de tarefa, o progresso da tarefa, de depuração e iniciar as mensagens de falha para acompanhar o progresso de uma tarefa ao longo do tempo, de processamento de saída.

## <a name="find-operation-logs-in-the-azure-portal"></a>Localizar registos de operações no portal do Azure
Podem ser acedidos a registos de operação de duas formas:  

* Dashboard da tarefa de Stream Analytics  
* Serviços de gestão no portal clássico do Azure  

## <a name="dashboard-of-the-stream-analytics-job"></a>Dashboard da tarefa de Stream Analytics
É apresentada uma ligação para os registos de uma tarefa de Stream Analytics correspondentes no separador de Dashboard da tarefa. Se clicar dessa ligação, define os filtros de forma que mostra os registos mais recentes para essa tarefa específica.

  ![Selecione os registos de serviços de gestão](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Serviços de gestão
Para navegar manualmente para os registos de operação de Stream Analytics e a outros serviços no portal clássico do Azure:

1. Clique em **dos serviços de gestão** no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione **Stream Analytics** para **tipo** e o nome da tarefa para **nome do serviço**.  
   
   ![Selecione o do Stream Analytics](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Localizar registos de auditoria no portal do Azure
Localizar registos operacionais para a tarefa de Stream Analytics no portal do Azure, clique em **procurar** e, em seguida, selecione **registos de auditoria**.

  ![Portal do Azure, selecione o Stream Analytics](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Esta ação abre um ecrã que mostra eventos dos últimos sete dias para todos os recursos na sua subscrição.  Pode filtrar para ver eventos de um tipo de especificar ou o período de tempo, clicando no **filtro** comando.

  ![Portal do Azure, selecione o Stream Analytics](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Obter os detalhes de registo
Pode filtrar por intervalo de tempo e o estado para ver os registos para a tarefa.

No portal do Azure, clique em de **detalhes** na parte inferior da janela para ver mais detalhes sobre um evento selecionado. 

  ![Selecionar os detalhes](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

No portal do Azure, clique na entrada de registo para ver os eventos de detalhado no interior-lo.

  ![Portal do Azure, selecione os detalhes](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

A partir daí, pode abrir o **detalhe** apresentar clicando no evento.

  ![Portal do Azure, selecione os detalhes](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Depurar uma falha na tarefa
No portal do Azure, clique no ícone de pesquisa e "Falha" de tipo. Isto proporciona um resultado de todos os registos com falhas. 

  ![Depurar uma falha na tarefa](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

No portal do Azure, pode filtrar por nível de mensagem para ver **crítico** eventos.

  ![Depuração portal do Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Pode selecionar qualquer uma das falhas e clique em de **detalhes** para obter mais informações sobre o erro.  Algumas mensagens de erro também fornecem informações sobre como mitigar o problema. 

  ![Detalhes de operação](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

No caso de ser necessário contactar o [suporte](https://azure.microsoft.com/support/options/) ou fornecem informações para a equipa através de [fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), tenha em atenção os detalhes de operação, especificamente o **ID de correlação**. 

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

