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
ms.date: 06/16/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 421bdfb3132bc8c9f193bcca8d55c9cf9eba1c3b
ms.contentlocale: pt-pt
ms.lasthandoff: 07/04/2017


---

<a id="what-is-stream-analytics" class="xliff"></a>

# O que é o Stream Analytics?

O Azure Stream Analytics é um motor totalmente gerido de processamento de eventos que lhe permite configurar cálculos de análise em tempo real em dados de transmissão. Os dados podem ser provenientes de dispositivos, sensores, Web sites, feeds de redes sociais, aplicações, sistemas de infraestruturas, entre outras origens. 

<a id="what-can-i-use-stream-analytics-for" class="xliff"></a>

## Para que posso utilizar o Stream Analytics?

Com o Stream Analytics, pode examinar grandes volumes de dados transmitidos de dispositivos ou processos, extrair informações do fluxo de dados e procurar padrões, tendências e relações. Com base no que dizem os dados, pode, depois, realizar tarefas de aplicação. Por exemplo, pode gerar alertas, iniciar fluxos de trabalho de automatização, enviar informações para ferramentas de relatórios, como o Power BI, ou arquivar dados para investigar mais tarde. 

Os exemplos de cenários do Stream Analytics incluem:

* Análise e alertas personalizados e em tempo real sobre negociações de valores mobiliários, oferecidos por empresas de serviços financeiros.
* Deteção de fraudes em tempo real, com base em exames aos dados de transações. 
* Serviços de proteção de dados e identidades.
* Análise de dados gerados por sensores e atuadores incorporados em objetos físicos (Internet das Coisas, ou IoT).
* Análise clickstream da Web.
* Aplicações de gestão de relações com os clientes (CRM), como emissão de alertas quando a experiência do cliente dentro de um período de tempo se degrada.

<a id="how-does-stream-analytics-work" class="xliff"></a>

## Como funciona o Stream Analytics?

O diagrama seguinte ilustra o pipeline do Stream Analytics e mostra como os dados são ingeridos, analisados e, depois, enviados para apresentação ou ação. 

![Pipeline do Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

O Stream Analytics começa com uma origem de dados de transmissão. Os dados podem ser ingeridos no Azure a partir de um dispositivo através de um hub de eventos do Azure ou do Hub IoT. Também podem ser extraídos de arquivos de dados, como o Armazenamento de Blobs do Azure. 

Para examinar o fluxo, crie uma *tarefa* do Stream Analytics que especifique a proveniência dos dados. A tarefa também especifica uma *transformação* &mdash; como procurar dados, padrões ou relações. Nesta tarefa, o Stream Analytics suporta uma linguagem de consultas do tipo SQL que lhe permite filtrar, ordenar, agregar e associar dados de transmissão ao longo de um período de tempo.

Por fim, a tarefa especifica uma saída para a qual enviar os dados transformados. Desta forma, pode controlar o que fazer em resposta às informações que analisou. Por exemplo, em resposta a uma análise, pode:

* Enviar um comando para alterar as definições de um dispositivo. 
* Enviar dados para uma fila que é monitorizada por um processo que realiza ações com base no que encontra. 
* Enviar dados para um dashboard do Power BI para criar relatórios.
* Enviar dados para armazenamento, como o Data Lake Store, a Base de Dados SQL do Azure ou o armazenamento de Blobs ou Tabelas do Azure.

Enquanto uma tarefa estiver a decorrer, pode monitorizá-la e ajustar o número de eventos que a mesma processa por segundo. Também pode fazer com que as tarefas produzam registos de diagnósticos, para resolução de problemas.

<a id="key-capabilities-and-benefits" class="xliff"></a>

## Principais capacidades e vantagens

O Stream Analytics foi concebido para ser fácil de utilizar, flexível, dimensionável para qualquer tamanho de tarefa e económico.

<a id="connectivity-to-many-inputs-and-outputs" class="xliff"></a>

### Conectividade para muitas entradas e saídas

O Stream Analytics liga-se diretamente aos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e aos [Hubs IoT do Azure](https://azure.microsoft.com/services/iot-hub/), para ingestão de transmissões, e ao [serviço de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts), para ingestão de dados históricos. Se receber dados de hubs de eventos, pode combinar o Stream Analytics com outras origens de dados e motores de processamento.

A entrada da tarefa também pode incluir dados de referência (dados estáticos ou com poucas alterações). Pode associar os dados de transmissão a estes dados de referência para realizar operações de pesquisa tal e qual como faria com as consultas de bases de dados.

A saída das tarefas do Stream Analytics pode ser encaminhada para muitas direções. Pode ser escrita no armazenamento, como blobs ou tabelas do Armazenamento do Azure, a BD SQL do Azure, o Azure Data Lake Store ou o Azure Cosmos DB. A partir daí, os dados podem ir para análise de lotes através do Azure HDInsight. Pode enviar a saída para outro serviço, para ser consumida por um processo diferente, como os hubs de eventos, tópicos do Azure Service Bus ou filas. Pode enviar a saída para o Power BI, para visualização.

<a id="ease-of-use" class="xliff"></a>

### Facilidade de utilização

Para definir as transformações, utilize uma [linguagem de consultas do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) não declarativa, a qual lhe permite criar análises sofisticadas sem programação. A linguagem de consultas utiliza os dados de transmissão como a respetiva entrada. Em seguida, pode filtrar e ordenar os dados, agregar valores, realizar cálculos, associar dados (num fluxo ou a dados de referência) e utilizar funções geoespaciais. Pode editar as consultas no portal com IntelliSense e verificação de sintaxe e pode testá-las com dados de exemplo que podem ser extraídos da transmissão em direto.

<a id="extensible-query-language" class="xliff"></a>

### Linguagem de consultas extensível

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Pode definir chamadas de funções no serviço Azure Machine Learning, para tirar partido das soluções do mesmo. Também pode integrar funções JavaScript definidas pelo utilizador (UDFs), de modo a realizar cálculos complexos como parte de consultas do Stream Analytics.

<a id="scalability" class="xliff"></a>

### Escalabilidade

O Stream Analytics pode processar até 1 GB de dados de entrada por segundo. A integração com os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e com os [Hubs IoT do Azure](https://azure.microsoft.com/services/iot-hub/) permite que as tarefas ingiram milhões de eventos por segundo provenientes de dispositivos ligados, clickstreams e ficheiros de registo, entre outros. Ao utilizar a funcionalidade de partições dos hubs de eventos, pode particionar cálculos em passos lógicos, tendo a possibilidade de particioná-los ainda mais para aumentar a escalabilidade.

<a id="low-cost" class="xliff"></a>

### Baixo custo

Como serviço cloud, o Stream Analytics está otimizado para ser utilizado a um baixo custo. Paga consoante o que utilizar com base na utilização por unidade de transmissão e a quantidade de dados que o sistema processa. A utilização é obtida consoante o volume de eventos processados e a quantidade de poder de computação aprovisionada no cluster para realizar tarefas do Stream Analytics.

<a id="reliability-quick-recovery-and-repeatability" class="xliff"></a>

### Fiabilidade, recuperação rápida e repetibilidade

Enquanto serviço gerido na cloud, o Stream Analytics ajuda a evitar perdas de dados e proporciona continuidade do negócio. Se ocorrerem falhas, o serviço fornece capacidades de recuperação incorporadas. Com a capacidade de manter internamente o estado, o serviço fornece resultados repetíveis que garantem que é possível arquivar eventos e voltar a aplicar o processamento no futuro, obtendo sempre os mesmos resultados. Desta forma, pode voltar atrás no tempo e investigar cálculos durante a realização de análises de causas de problemas, análises de hipóteses e assim sucessivamente.

<a id="next-steps" class="xliff"></a>

## Passos seguintes

* Para começar, [experimente entradas e consultas a partir de dispositivos IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Crie uma [solução do Stream Analytics ponto a ponto](stream-analytics-real-time-fraud-detection.md) que examine metadados de telefones para procurar chamadas fraudulentas.
* Saiba mais sobre a linguagem de consultas do tipo SQL para o Stream Analytics e sobre conceitos exclusivos, como as [funções de janela](stream-analytics-window-functions.md).
* Saiba como [dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md). 
* Saiba como [integrar o Stream Analytics e o Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).
* Encontre respostas para as suas perguntas sobre o Stream Analytics no [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


