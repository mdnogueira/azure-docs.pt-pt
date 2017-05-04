---
title: "Introdução ao Stream Analytics | Microsoft Docs"
description: "Saiba mais sobre o Stream Analytics, um serviço gerido que o ajuda a analisar os dados de transmissão a partir da Internet de Coisas (IoT) em tempo real."
keywords: "análise como um serviço, serviços geridos, processamento de fluxo, stream analytics, o que é o stream analytics"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: a6b1df79f4fc6b09b681755f5adbb7f56faaf225
ms.lasthandoff: 05/01/2017


---
# <a name="what-is-stream-analytics"></a>O que é o Stream Analytics?
O Azure Stream Analytics é um motor de processamento de eventos totalmente gerido, em tempo real e economicamente acessível, que o ajuda a desbloquear os conhecimentos aprofundados sobre os dados. O Stream Analytics facilita a configuração das computações analíticas em tempo real da transmissão de dados a partir de dispositivos, sensores, Web sites, redes sociais, aplicações, sistemas de infraestruturas e muito mais.

Com apenas alguns cliques no Portal do Azure, pode criar uma tarefa do Stream Analytics especificando a origem de entrada de dados de transmissão, o sink de saída para os resultados da tarefa e uma transformação de dados que é expressa numa linguagem como o SQL. Pode monitorizar e ajustar a escala/velocidade da tarefa no Portal do Azure ao escalar os eventos processados por segundo de alguns quilobytes para um gigabyte ou mais.

O Stream Analytics tira partido dos anos de trabalho da Microsoft Research no desenvolvimento de motores de transmissão para processamentos sensíveis ao tempo, bem como integrações de idioma para obter especificações intuitivas do mesmo.

## <a name="what-can-i-use-stream-analytics-for"></a>Para que posso utilizar o Stream Analytics?
Grandes quantidades de dados estão constantemente a ser transmitidas a alta velocidade. As organizações que podem processar e atuar sobre estes dados de transmissão em tempo real podem melhorar significativamente a eficiência e destacarem-se no mercado. Existem cenários de análise de transmissão em tempo real em todas as indústrias: análise de negociação de cotações e alertas personalizados e em tempo real oferecidos pelas empresas de serviços financeiros; deteção de fraudes em tempo real; serviços de proteção de dados e identidades; e análise de dados gerados por sensores e atuadores incorporados em objetos físicos (Internet das Coisas ou IoT); análise de clickstream da Web; e aplicações de gestão da relação com o cliente (CRM) que emitem alertas quando a experiência do cliente está degradada num determinado período de tempo. As empresas procuram a forma mais flexível, fiável e económica de fazer com sucesso essa análise de dados em tempo real e a transmissão de eventos num mundo empresarial moderno e altamente competitivo.

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens
* **Facilidade de utilização:** o Stream Analytics suporta um modelo de consulta simples e declarativo para descrever transformações. Para otimizar a facilidade de utilização, o Stream Analytics utiliza uma variante do T-SQL e remove a necessidade de os clientes lidarem com as complexidades técnicas dos sistemas de processamento da transmissão. Ao utilizar o [idioma de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) no editor de consultas do browser, obterá uma conclusão automática e inteligente para o ajudar a implementar rápida e facilmente as consultas de séries de tempo, incluindo associações temporais, agregados em janelas e outras operações comuns, tais como associações, agregados, projeções e filtros. Além disso, os testes de consultas no browser relativamente ao ficheiro de dados de dados de exemplo permite um desenvolvimento rápido e interativo.  
* **Escalabilidade:** o Stream Analytics é capaz de processar os resultados de um grande evento até 1 GB por segundo. A integração com os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e com os [Hubs IoT do Azure](https://azure.microsoft.com/services/iot-hub/) permite que a solução para a ingestão de milhões de eventos por segundo provenha de dispositivos ligados, clickstreams e ficheiros de registo, entre outros. Para atingir esse objetivo, o Stream Analytics melhora a capacidade de criação de partições de Event Hubs que podem produzir 1 MB/s por partição. Os utilizadores conseguem criar partições num número de passos lógicos de acordo com a definição de consulta, cada uma com a capacidade para ter ainda mais partições para aumentar a escalabilidade.  
* **Fiabilidade, repetibilidade e recuperação rápida:** um serviço gerido na nuvem, o Stream Analytics ajuda a evitar a perda de dados e permite a continuidade do negócio em caso de falhas através de funcionalidades de recuperação incorporadas. Com a capacidade de manter internamente o estado, o serviço fornece resultados repetíveis que garantem que é possível arquivar eventos e voltar a aplicar o processamento no futuro, obtendo sempre os mesmos resultados. Isto permite que os clientes voltem atrás no tempo e investiguem as computações ao efetuar uma análise da causa/raiz, análises das hipóteses, etc.  
* **Baixo custo:** sendo um serviço em nuvem, o Stream Analytics está otimizado para oferecer inicialmente aos utilizadores um custo muito baixo e manter soluções de análise em tempo real. O serviço foi concebido para ser pago por cada utilização, com base na utilização da unidade de transmissão e na quantidade de dados processados pelo sistema. A utilização é obtida consoante o volume de eventos processados e a quantidade de computação aprovisionada no cluster para realizar as respetivas tarefas do Stream Analytics.  
* **Dados de referência:** o Stream Analytics fornece aos utilizadores a capacidade de especificar e utilizar os dados de referência. Estes podem ser dados do histórico ou simplesmente sem transmissão que são alterados com menos frequência ao longo do tempo. O sistema simplifica a utilização de dados de referência que devem ser tratados como qualquer outra transmissão de eventos para juntar a outras transmissões de eventos ingeridos em tempo real para efetuar transformações.  
* **Funções Definidas pelo Utilizador:** o Stream Analytics tem integração com o Machine Learning do Azure para definir chamadas de função no serviço Machine Learning como parte de uma consulta do Stream Analytics. Isto expande as capacidades do Stream Analytics para tirar partido de soluções existentes do Machine Learning do Azure. Para obter mais informações sobre este, reveja o [tutorial de integração do Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).
* **Conectividade:** o Stream Analytics estabelece ligação direta aos Hubs de Eventos do Azure e aos Hubs IoT do Azure para ingestão da transmissão e ao serviço Blob do Azure para a ingestão de dados do histórico. Os resultados podem ser escritos a partir do Stream Analytics para o Armazenamento de Blobs ou Tabelas do Azure, Base de Dados SQL do Azure, Azure Data Lake Stores, DocumentDB, Hubs de Eventos, tópicos ou filas de Service Bus do Azure e Power BI, onde podem ser visualizados e processados por fluxos de trabalho, utilizados na análise em lote através do [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) ou processados novamente como uma série de eventos. Quando utilizar Event Hubs, é possível compor vários Stream Analytics, juntamente com outras origens de dados e motores de processamento, sem perder a natureza transmissão das computações.  

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
Agora já conhece o Stream Analytics, um serviço gerido para a transmissão da análise dos dados a partir da Internet das Coisas. Para obter mais informações sobre este serviço, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-get-started.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


