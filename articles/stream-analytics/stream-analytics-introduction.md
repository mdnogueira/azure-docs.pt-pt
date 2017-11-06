---
title: "Introdução ao Stream Analytics | Microsoft Docs"
description: "Saiba mais sobre o Stream Analytics, um serviço gerido que o ajuda a analisar os dados de transmissão a partir da Internet de Coisas (IoT) em tempo real."
keywords: "análise como um serviço, serviços geridos, processamento de fluxo, stream analytics, o que é o stream analytics"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/17/2017
ms.author: samacha
ms.openlocfilehash: 889aaa697a04a81075b85c834d534bfeb4faf79b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="what-is-stream-analytics"></a>O que é o Stream Analytics?

O Azure Stream Analytics é um motor de processamento de eventos gerido que lhe permite configurar cálculos de análise em tempo real em dados de transmissão. Os dados podem ser provenientes de dispositivos, sensores, Web sites, feeds de redes sociais, aplicações, sistemas de infraestruturas, entre outras origens. 

Utilize o Stream Analytics para examinar grandes volumes de dados transmitidos de dispositivos ou processos, extrair informações desse fluxo de dados e identificar padrões, tendências e relações. Utilize esses padrões para acionar outros processos ou ações, como alertas, fluxos de trabalho de automatização, informações de feeds para uma ferramenta de relatórios ou armazene-os para investigação posterior. 

Alguns exemplos:

* Análise e alertas de negociações de valores mobiliários.
* Deteção de fraudes, dados e identificação de proteções. 
* Sensor incorporado e análise do atuador.
* Análise clickstream da Web.

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Este diagrama ilustra o pipeline do Stream Analytics e mostra como os dados são ingeridos, analisados e, depois, enviados para apresentação ou ação. 

![Pipeline do Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

O Stream Analytics começa com uma origem de dados de transmissão. Os dados podem ser ingeridos no Azure a partir de um dispositivo através de um hub de eventos do Azure ou do Hub IoT. Também podem ser extraídos de arquivos de dados, como o Armazenamento de Blobs do Azure. 

Para examinar o fluxo, crie uma *tarefa* do Stream Analytics que especifique a origem dos dados. A tarefa também especifica uma *transformação*; como procurar dados, padrões ou relações. Nesta tarefa, o Stream Analytics suporta uma linguagem de consultas do tipo SQL para filtrar, ordenar, agregar e associar dados de transmissão ao longo de um período de tempo.

Por fim, a tarefa especifica uma saída para esses dados transformados. Pode controlar o que fazer em resposta às informações que analisou. Por exemplo, em resposta a uma análise, pode:

* Enviar um comando para alterar as definições do dispositivo. 
* Enviar dados para uma fila monitorizada para mais ações com base em averiguações. 
* Enviar dados para um dashboard do Power BI.
* Enviar dados para armazenamento, como o Data Lake Store, a Base de Dados SQL do Azure ou o armazenamento de Blobs do Azure.

Pode ajustar o número de eventos processados por segundo durante a execução da tarefa. Também pode produzir registos de diagnósticos para resolução de problemas.

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens

O Stream Analytics foi concebido para ser fácil de utilizar, flexível e dimensionável para qualquer tamanho de tarefa.

### <a name="connect-inputs-and-outputs"></a>Ligar entradas e saídas

O Stream Analytics liga-se diretamente aos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e ao [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) para ingestão de transmissões, e ao [serviço de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) para ingestão de dados históricos. Pode combinar dados de hubs de eventos com o Stream Analytics com outras origens de dados e motores de processamento. A entrada da tarefa também pode incluir dados de referência (dados estáticos ou com poucas alterações). Pode associar os dados de transmissão a estes dados de referência para realizar operações de pesquisa tal e qual como faria com as consultas de bases de dados.

Encaminhar o resultado da tarefa do Stream Analytics em várias direções. Pode escrever no armazenamento, como o Blob do Azure, a Base de Dados SQL do Azure, o Azure Data Lake Store ou o Azure Cosmos DB. A partir daí, pode executar análises de lotes com o Azure HDInsight. Também pode enviar a saída para outro serviço, para ser consumida por um processo diferente, como hubs de eventos, Azure Service Bus e filas, ou para o Power BI para visualização.

### <a name="simple-to-use"></a>Fácil de utilizar

Para definir as transformações, utilize uma [linguagem de consultas do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) não declarativa, a qual lhe permite criar análises sofisticadas sem programação. A linguagem de consultas utiliza os dados de transmissão como a respetiva entrada. Em seguida, pode filtrar e ordenar os dados, agregar valores, realizar cálculos, associar dados (num fluxo ou a dados de referência) e utilizar funções geoespaciais. Pode editar as consultas no portal com IntelliSense e verificação de sintaxe e pode testá-las com dados de exemplo que podem ser extraídos da transmissão em direto.

### <a name="extensible-query-language"></a>Linguagem de consultas extensível

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Pode definir chamadas de funções no serviço Azure Machine Learning, para tirar partido das soluções do mesmo. Também pode integrar funções JavaScript definidas pelo utilizador (UDFs), de modo a realizar cálculos complexos como parte de consultas do Stream Analytics.

### <a name="scalable"></a>Escalável

O Stream Analytics pode processar até 1 GB de dados de entrada por segundo. A integração com os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e com o [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) permite que as tarefas ingiram milhões de eventos por segundo provenientes de dispositivos ligados, clickstreams e ficheiros de registo, entre outros. Ao utilizar a funcionalidade de partições dos hubs de eventos, pode particionar cálculos em passos lógicos, tendo a possibilidade de particioná-los ainda mais para aumentar a escalabilidade.

### <a name="low-cost"></a>Baixo custo

Como serviço cloud, o Stream Analytics está otimizado para custo. Pague consoante o que utilizar, com base na utilização por unidade de transmissão e na quantidade de dados processados. A utilização é obtida consoante o volume de eventos processados e a quantidade de poder de computação aprovisionada no cluster da tarefa.

### <a name="reliable"></a>Fiável

Enquanto serviço gerido, o Stream Analytics ajuda a evitar perdas de dados e proporciona continuidade do negócio. Se ocorrerem falhas, o serviço fornece capacidades de recuperação incorporadas. Com a capacidade de manter internamente o estado, o serviço fornece resultados repetíveis que garantem que é possível arquivar eventos e voltar a aplicar o processamento no futuro, obtendo sempre os mesmos resultados. Desta forma, pode voltar atrás no tempo e investigar cálculos durante a realização de análises de causas de problemas, análises de hipóteses e assim sucessivamente.

## <a name="next-steps"></a>Passos seguintes

* Para começar, [experimente entradas e consultas a partir de dispositivos IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Crie uma [solução do Stream Analytics ponto a ponto](stream-analytics-real-time-fraud-detection.md) que examine metadados de telefones para procurar chamadas fraudulentas.
* Encontre respostas para as perguntas sobre o Stream Analytics no [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

