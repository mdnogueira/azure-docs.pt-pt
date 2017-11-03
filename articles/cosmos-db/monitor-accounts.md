---
title: Monitorizar os pedidos de base de dados do Azure Cosmos e armazenamento | Microsoft Docs
description: "Saiba como monitorizar a sua conta de base de dados do Azure Cosmos para as métricas de desempenho, tais como pedidos e erros de servidor e a métrica de utilização, tais como o consumo de armazenamento."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mimig
ms.openlocfilehash: 2e5cce26bc8bebbe6b9f8ba3c3d03e8c3db8c87c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-cosmos-db"></a>Monitorizar Cosmos BD do Azure
Pode monitorizar as contas de base de dados do Azure Cosmos no [portal do Azure](https://portal.azure.com/). Para cada conta de base de dados do Azure Cosmos, um conjunto completo de métricas está disponível para monitorizar o débito, armazenamento, disponibilidade, latência e consistência.

Podem ser revistas métricas na página de conta, a página de métricas novo, ou no Monitor do Azure.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Veja as métricas de desempenho na página de métricas
1. No [portal do Azure](https://portal.azure.com/), clique em **mais serviços**, desloque-se para **bases de dados**, clique em **Azure Cosmos DB**e, em seguida, clique no nome da conta do Azure Cosmos DB para o qual gostaria de ver as métricas de desempenho.
2. Quando a nova página for carregada, no menu de recursos, em **monitorização**, clique em **métricas**.
3. Quando é aberta a página as métricas, selecione a coleção para rever a partir de **Collection(s)** pendente.

   O portal do Azure apresenta o conjunto de métricas de coleção disponíveis. Tenha em atenção que as métricas de débito, armazenamento, disponibilidade, latência e consistência são fornecidas nos separadores separados. Para obter detalhes adicionais sobre as métricas fornecidas, clique na seta para duplo na parte superior direita do painel cada métricas.

   ![Captura de ecrã da lente monitorização, que mostra o conjunto de métricas](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Veja as métricas de desempenho utilizando a monitorização do Azure
1. No [portal do Azure](https://portal.azure.com/), clique em **Monitor** na barra da esquerda.
2. No menu de recursos, clique em **métricas**.
3. No **Monitor - métricas** janela, no **grupo de recursos** menu pendente, selecione o grupo de recursos associado à conta de base de dados do Azure Cosmos que pretende monitorizar. 
4. No **recursos** menu pendente, selecione a base de dados de conta para monitorizar.
5. Na lista de **as métricas disponíveis**, selecione as métricas para apresentar. Utilize o botão CTRL para seleção múltipla. 

## <a name="view-performance-metrics-on-the-account-page"></a>Veja as métricas de desempenho na página de conta
1. No [portal do Azure](https://portal.azure.com/), clique em **mais serviços**, desloque-se para **bases de dados**, clique em **Azure Cosmos DB**e, em seguida, clique no nome da conta do Azure Cosmos DB para o qual gostaria de ver as métricas de desempenho.
2. O **monitorização** lente apresenta os seguintes mosaicos por predefinição:
   
   * Total de pedidos para o dia atual.
   * Armazenamento utilizado.
   
   ![Captura de ecrã da lente monitorização, que mostra os pedidos e a utilização do armazenamento](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Clicar na seta para duplo na parte superior direita do **pedidos** mosaico abre um detalhadas **métrica** página.
4. O **métrica** página mostra detalhes sobre o total de pedidos. 

## <a name="set-up-alerts-in-the-portal"></a>Configurar alertas no portal
1. No [portal do Azure](https://portal.azure.com/), clique em **mais serviços**, clique em **Azure Cosmos DB**e, em seguida, clique no nome da conta do Azure Cosmos DB para o qual pretende configurar o desempenho métricas alertas.
2. No menu de recursos, clique em **regras de alerta** para abrir a página de regras de alertas.  
   ![Captura de ecrã do alerta regras parte selecionado](./media/monitor-accounts/madocdb10.5.png)
3. No **regras de alerta** página, clique em **Adicionar alerta**.  
   ![Captura de ecrã da página de regras de alertas, com o botão Adicionar alerta realçado](./media/monitor-accounts/madocdb11.png)
4. No **adicionar uma regra de alerta** página, especifique:
   
   * O nome da regra de alerta que estiver a configurar.
   * Uma descrição da nova regra de alerta.
   * A métrica para a regra de alerta.
   * A condição, limiar e período que determinam quando ativa o alerta. Por exemplo, um erro de servidor contam maior 5 nos últimos 15 minutos.
   * Indica se o administrador de serviço e coadministrators são enviados por e-mail quando o alerta é acionado.
   * Endereços de e-mail adicionais para notificações de alerta.  
     ![Captura de ecrã de adicionar uma página de regra de alerta](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorizar a base de dados do Azure Cosmos através de programação
Métricas de nível de conta disponíveis no portal, tais como contra as pedidos total e a utilização do armazenamento da conta, não estão disponíveis através das APIs do DocumentDB. No entanto, pode obter dados de utilização ao nível da coleção com as APIs do DocumentDB. Para obter dados ao nível da coleção, efetue o seguinte:

* Para utilizar a API REST, [efetuar uma ação obter na coleção de](https://msdn.microsoft.com/library/mt489073.aspx). As informações de quota e utilização para a coleção são devolvidas nos cabeçalhos x-ms-resource-quota e x-ms--a utilização de recursos na resposta.
* Para utilizar o SDK .NET, utilize o [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) método, que devolve um [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém um número de propriedades de utilização, tais como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**e muito mais.

Para aceder a métricas adicionais, utilize o [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). É possível obter as definições de métrica disponíveis, chamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

As consultas para obter métricas individuais, utilize o seguinte formato:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Para obter mais informações, consulte [obter métricas de recurso através da API de REST do Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Tenha em atenção que "Informações do Azure" nome foi mudada "Monitor de Azure".  Esta entrada de blogue refere-se para o nome anterior.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o planeamento de capacidade de BD do Cosmos do Azure, consulte o [Calculadora de Planeador de capacidade de base de dados do Azure Cosmos](https://www.documentdb.com/capacityplanner).

