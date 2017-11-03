---
title: "Ligação de dados: entradas de um fluxo de eventos de fluxo de dados | Microsoft Docs"
description: "Saiba mais sobre como configurar uma ligação de dados para o Stream Analytics chamado 'entradas'. Entradas incluem um fluxo de dados de eventos e também referenciam a dados."
keywords: "fluxo de dados, a ligação de dados, a transmissão de eventos"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/05/2017
ms.author: samacha
ms.openlocfilehash: 652137cf7a41f8d90a56aebe9f82fd37d5e4683d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Ligação de dados: saber mais sobre os dados de entradas de fluxo de eventos para o Stream Analytics
A ligação de dados para uma tarefa de Stream Analytics é um fluxo de eventos a partir de uma origem de dados, que é conhecida como a tarefa *entrada*. Do Stream Analytics tem integração de primeira classe com origens de fluxo de dados do Azure, incluindo [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/), [IoT Hub do Azure](https://azure.microsoft.com/services/iot-hub/), e [Blob storage do Azure](https://azure.microsoft.com/services/storage/blobs/). Estas origens de entrada podem ser da mesma subscrição do Azure como a tarefa de análise, ou a partir de uma subscrição diferente.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipos de dados de entrada: dados de referência e de fluxo de dados
Como os dados são forçados a uma origem de dados, tem consumidas pela tarefa de Stream Analytics e processados em tempo real. Entradas são divididas em dois tipos: entradas de dados de referência e entradas de fluxo de dados.

### <a name="data-stream-inputs"></a>Entradas de fluxo de dados
Um fluxo de dados é uma sequência de eventos unbounded ao longo do tempo. Tarefas do Stream Analytics tem de incluir entradas de fluxo de dados de pelo menos um. Os Event Hubs, o IoT Hub e o armazenamento de BLOBs são suportados como origens de entrada de fluxo de dados. Os Event hubs são utilizados para recolher fluxos de eventos de vários dispositivos e serviços. Estes fluxos podem incluir feeds de atividade de redes sociais, as cotações compromissos informações ou dados de sensores. Os hubs IoT estão otimizados para recolher dados de dispositivos ligados em cenários Internet das coisas (IoT).  O blob storage pode ser utilizado como uma origem de entrada para a ingestão de dados do volume como um fluxo, tais como ficheiros de registo.  

### <a name="reference-data"></a>Dados de referência
Stream Analytics suporta também conhecida como de entrada *referência a dados*. Estes são dados auxiliares que é o estático ou que as alterações lentamente. Normalmente, é utilizado para efetuar a correlação e pesquisas. Por exemplo, poderá associar dados na entrada de fluxo de dados para dados em dados de referência, sucederia com executaria uma associação SQL para procurar valores estáticos. Armazenamento de Blobs do Azure está atualmente a única origem de entrada suportada para dados de referência. Os blobs de origem de dados de referência estão limitados a 100 MB de tamanho.

Para saber como criar entradas de dados de referência, consulte [utilizar dados de referência](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Compressão

O Azure Stream Analytics suporta a compressão em todos os fluxo entrada origens de dados (armazenamento de BLOBs, o IoT Hub e Hubs de eventos). Esta funcionalidade adiciona uma nova opção de menu pendente para o **nova entrada** painel no Portal do Azure, permitindo-lhe optar por comprimir os fluxos de dados. Os tipos suportados são atualmente None, GZip e Deflate compressão. 

A compressão não é suportado em conjunto com a serialização de Avro e não é aplicável a referência a dados. 

## <a name="create-data-stream-input-from-event-hubs"></a>Criar entrada de fluxo de dados a partir de Event Hubs

Os Hubs de eventos do Azure fornece altamente dimensionável ingestors de eventos de publicação-subscrição. Um hub de eventos pode recolher milhões de eventos por segundo, para que possa processar e analisar as quantidades enormes de dados produzidos pelos seus dispositivos e aplicações ligados. Os Event Hubs e Stream Analytics em conjunto lhe fornecem uma solução ponto a ponto para a análise em tempo real — Event Hubs permitem-lhe feed eventos no Azure em tempo real e tarefas do Stream Analytics podem processar esses eventos em tempo real. Por exemplo, pode enviar cliques web, as leituras dos sensores ou eventos de registo online para os Event Hubs. Em seguida, pode criar tarefas do Stream Analytics para utilizar os Hubs de eventos como os fluxos de dados de entrada para em tempo real de filtragem, Agregar e correlação.

O carimbo de predefinição de eventos provenientes dos Event Hubs no Stream Analytics é timestamp que o evento de eventos chegou hub, o que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão em fluxo utilizando um timestamp nos eventos payload, tem de utilizar o [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) palavra-chave.

### <a name="consumer-groups"></a>Grupos de consumidores
Deve configurar cada hub de eventos do Stream Analytics de entrada para que o seu próprio grupo de consumidores. Quando uma tarefa contém uma associação automática ou quando tem várias entradas, alguns entrada pode ser lidos por mais do que um leitor downstream. Esta situação tem impacto sobre o número de leitores num grupo de consumidores único. Para evitar exceder o limite de Hubs de eventos de leitores de cinco por grupo de consumidores por partição, é uma melhor prática para designar um grupo de consumidores para cada tarefa de Stream Analytics. Também é um limite de 20 grupos de consumidores do hub de eventos. Para obter mais informações, consulte [guia de programação de Hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Configurar um hub de eventos como um fluxo de dados de entrada
A tabela seguinte explica cada propriedade no **nova entrada** painel no portal do Azure quando configurar um hub de eventos como entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que utilizar na consulta da tarefa para fazer referência a esta entrada. |
| **Espaço de nomes de barramento de serviço** |Um Service Bus do Azure espaço de nomes, que é um contentor para um conjunto de entidades de mensagens. Quando cria um novo hub de eventos, também cria um espaço de nomes do Service Bus. |
| **Nome do hub de eventos** |O nome do hub de eventos para utilizar como entrada. |
| **Nome de política do hub de eventos** |A política de acesso partilhado fornece acesso ao hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. |
| **Grupo de consumidores do hub de eventos** (opcional) |O grupo de consumidores para utilizar para a ingestão de dados do hub de eventos. Não se for especificado nenhum grupo de consumidores, a tarefa de Stream Analytics utiliza o grupo de consumidores predefinido. Recomendamos que utilize um grupo de consumidores distinta para cada tarefa de Stream Analytics. |
| **Formato de serialização de eventos** |O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada. |
| **Codificação** | UTF-8 atualmente é o único formato de codificação suportado. |
| **Compressão** (opcional) | O tipo de compressão (nenhum, GZip ou Deflate) do fluxo de dados de entrada. |

Quando os dados é proveniente de um hub de eventos, terá acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e hora em que o evento foi processado pelo Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e hora em que o evento foi recebido pelo Event Hubs. |
| **PartitionId** |O ID de partição baseado em zero para o adaptador de entrada. |

Por exemplo, utilizando estes campos, pode escrever uma consulta semelhante ao seguinte exemplo:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Quando utilizar o Hub de eventos como um ponto final para rotas de Hub IoT, pode aceder à utilização de medadata IoT Hub a [GetMetadataPropertyValue função](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Criar a entrada de fluxo de dados do IoT Hub
Iot Hub do Azure é altamente dimensionável de publicação-subscrição ingestor de eventos otimizado para cenários de IoT.

O carimbo de predefinição de eventos proveniente de um hub IoT no Stream Analytics é timestamp que o evento chegou no hub IoT, que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão em fluxo utilizando um timestamp nos eventos payload, tem de utilizar o [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) palavra-chave.

> [!NOTE]
> Apenas as mensagens enviadas com um `DeviceClient` propriedade pode ser processada.
> 
> 

### <a name="consumer-groups"></a>Grupos de consumidores
Deve configurar cada hub IoT da análise de fluxo de entrada para que o seu próprio grupo de consumidores. Quando uma tarefa contém uma associação automática ou quando tem várias entradas, alguns entrada pode ser lidos por mais do que um leitor downstream. Esta situação tem impacto sobre o número de leitores num grupo de consumidores único. Para evitar exceder o limite de IoT Hub do Azure de leitores de cinco por grupo de consumidores por partição, é uma melhor prática para designar um grupo de consumidores para cada tarefa de Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurar um IoT hub como um fluxo de dados de entrada
A tabela seguinte explica cada propriedade no **nova entrada** painel no portal do Azure quando configurar um hub IoT como entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que utilizar na consulta da tarefa para fazer referência a esta entrada.|
| **IoT hub** |O nome do hub IoT para utilizar como entrada. |
| **Ponto final** |O ponto final para o IoT hub.|
| **Nome da política de acesso partilhado** |A política de acesso partilhado fornece acesso ao IoT hub. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. |
| **Chave de política de acesso partilhado** |A chave de acesso partilhado utilizada para autorizar o acesso ao IoT hub. |
| **Grupo de consumidores** (opcional) |O grupo de consumidores para utilizar para a ingestão de dados do IoT hub. Não se for especificado nenhum grupo de consumidores, uma tarefa de Stream Analytics utiliza o grupo de consumidores predefinido. Recomendamos que utilize um grupo de consumidores diferente para cada tarefa de Stream Analytics. |
| **Formato de serialização de eventos** |O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada. |
| **Codificação** |UTF-8 atualmente é o único formato de codificação suportado. |
| **Compressão** (opcional) | O tipo de compressão (nenhum, GZip ou Deflate) do fluxo de dados de entrada. |

Quando os dados é proveniente de um hub IoT, tem acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** |A data e hora em que o evento foi recebido pelo IoT hub. |
| **PartitionId** |O ID de partição baseado em zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Um ID que é utilizado para correlacionar comunicação bidirecional no IoT hub. |
| **IoTHub.CorrelationId** |Um ID que é utilizado em respostas de mensagem e comentários no IoT hub. |
| **IoTHub.ConnectionDeviceId** |O ID de autenticação utilizado para enviar esta mensagem. Este valor é carimbo de data / nas mensagens de servicebound pelo IoT hub. |
| **IoTHub.ConnectionDeviceGenerationId** |O ID de geração do dispositivo autenticado que foi utilizado para enviar esta mensagem. Este valor é carimbo de data / nas mensagens de servicebound pelo IoT hub. |
| **IoTHub.EnqueuedTime** |A hora quando a mensagem foi recebida pelo IoT hub. |
| **IoTHub.StreamId** |Uma propriedade de evento personalizado adicionada pelo dispositivo remetente. |


## <a name="create-data-stream-input-from-blob-storage"></a>Criar a entrada de fluxo de dados do armazenamento de BLOBs
Para cenários com grandes quantidades de dados não estruturados para armazenar na nuvem, o Blob storage do Azure oferece uma solução económica e dimensionável. Dados no Blob storage, normalmente, são considerados dados inativos. No entanto, pode ser processado como um fluxo de dados pelo Stream Analytics. Um cenário típico de entradas de armazenamento de Blobs com o Stream Analytics é o processamento do registo. Neste cenário, os dados telemétricos captura de um sistema em tem de ser analisados e processados para extrair dados significativos.

O carimbo de predefinição de eventos de armazenamento de BLOBs no Stream Analytics é timestamp que o blob foi modificado pela última vez, o que é `BlobLastModifiedUtcTime`. Para processar os dados como uma transmissão em fluxo utilizando um timestamp nos eventos payload, tem de utilizar o [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) palavra-chave.

Entradas de CSV formatado *requerem* uma linha de cabeçalho para definir os campos para o conjunto de dados. Além disso, todos os campos de linha de cabeçalho tem de ser exclusivos.

> [!NOTE]
> Do Stream Analytics não suporta a adição de conteúdo para um ficheiro de blob existente. Do Stream Analytics irá ver uma só vez cada ficheiro e as alterações que ocorrem no ficheiro depois da tarefa tem de ler os dados não são processadas. É melhor prática carregar em simultâneo todos os dados para um ficheiro de blob e, em seguida, adicionar eventos mais recentes adicionais para um ficheiro de blob diferente, os novos.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Configurar o armazenamento de BLOBs como um fluxo de dados de entrada

A tabela seguinte explica cada propriedade no **nova entrada** painel no portal do Azure quando configurar o armazenamento de BLOBs como entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que utilizar na consulta da tarefa para fazer referência a esta entrada. |
| **Conta de armazenamento** | O nome da conta do storage onde estão localizados os ficheiros de blob. |
| **Chave de conta de armazenamento** | A chave secreta associada à conta de armazenamento. |
| **Contentor** | O contentor do blob de entrada. Contentores fornecem um agrupamento lógico blobs armazenados no Microsoft serviço Blob do Azure. Quando carregar um blob para o serviço de armazenamento de Blobs do Azure, tem de especificar um contentor para esse blob. |
| **Padrão de caminho** (opcional) | O caminho do ficheiro utilizado para localizar os blobs no contentor especificado. No caminho, pode especificar uma ou mais instâncias das três variáveis seguintes: `{date}`, `{time}`, ou`{partition}`<br/><br/>Exemplo 1:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2:`cluster1/logs/{date}`<br/><br/>O `*` carácter não é um valor permitido para o prefixo do caminho. Só é válido <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">carateres de Blobs do Azure</a> são permitidos. |
| **Formato de data** (opcional) | Se utilizar a variável de data no caminho, o formato da data em que os ficheiros estão organizados. Exemplo:`YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Se utilizar a variável de tempo no caminho, o formato de hora em que os ficheiros estão organizados. Atualmente é o único valor suportado `HH`. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV ou Avro) para fluxos de dados recebidos. |
| **Codificação** | Para CSV e JSON, UTF-8 atualmente é o único formato de codificação suportado. |
| **Compressão** (opcional) | O tipo de compressão (nenhum, GZip ou Deflate) do fluxo de dados de entrada. |

Quando os dados provém de uma origem de armazenamento de BLOBs, terá acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **BlobName** |O nome do blob de entrada que provém do evento. |
| **EventProcessedUtcTime** |A data e hora em que o evento foi processado pelo Stream Analytics. |
| **BlobLastModifiedUtcTime** |A data e hora em que o blob foi modificado pela última vez. |
| **PartitionId** |O ID de partição baseado em zero para o adaptador de entrada. |

Por exemplo, utilizando estes campos, pode escrever uma consulta semelhante ao seguinte exemplo:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
Aprendeu sobre as opções de ligação de dados no Azure para as tarefas do Stream Analytics. Para saber mais sobre o Stream Analytics, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
