---
title: "Telemetria de serviços de multimédia do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral de telemetria de Media Services do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 1b26d7925fe5bd39905d9f51d22433b1eea43af6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-telemetry"></a>Telemetria de serviços de multimédia do Azure

Serviços de suporte de dados do Azure (AMS) permite-lhe aceder a dados de telemetria/métricas para os respetivos serviços. A versão atual do AMS permite-lhe recolher dados de telemetria para direta **canal**, **StreamingEndpoint**e em direto **arquivo** entidades. 

Uma tabela de armazenamento numa conta do Storage do Azure que especificou é escrita telemetria (normalmente, seria utilizar a conta de armazenamento associada à sua conta de AMS). 

O sistema de telemetria não gere a retenção de dados. Pode remover os dados de telemetria antigo, eliminando as tabelas de armazenamento.

Este tópico descreve como configurar e consumir a telemetria de AMS.

## <a name="configuring-telemetry"></a>Configurar a telemetria

Pode configurar a telemetria numa granularidade de nível do componente. Existem dois níveis de detalhe "Normal" e "Verboso". Atualmente, os dois níveis devolvem as mesmas informações. É recomendado utilizar "Normal. 

Os tópicos seguintes mostram como ativar a telemetria:

[Ativar a telemetria com o .NET](media-services-dotnet-telemetry.md) 

[Ativar a telemetria com REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Consumir informações de telemetria

Telemetria é escrita para uma tabela de armazenamento do Azure na conta de armazenamento que especificou quando configurou a telemetria para a conta de Media Services. Esta secção descreve as tabelas de armazenamento para as métricas.

Pode consumir dados de telemetria de uma das seguintes formas:

- Ler dados diretamente a partir do armazenamento de tabelas do Azure (por exemplo, utilizando o SDK de armazenamento). Para obter a descrição das tabelas de armazenamento de telemetria, consulte o **consumir informações de telemetria** no [isto](https://msdn.microsoft.com/library/mt742089.aspx) tópico.

Ou

- Utilize o suporte de SDK do .NET de Media Services para ler os dados de armazenamento, conforme descrito em [isto](media-services-dotnet-telemetry.md) tópico. 


O esquema de telemetria descrito abaixo é concebido para dar um bom desempenho dentro dos limites do Table Storage do Azure:

- Dados particionados conta ID e o ID de serviço para permitir a telemetria de cada serviço a ser consultados de forma independente.
- Partições contêm a data para lhe dar um limite superior razoável sobre o tamanho de partição.
- Chaves de linha estão por ordem inversa tempo para permitir que os itens de telemetria mais recentes para ser consultado para um determinado serviço.

Isto deve permitir muitas das consultas comuns para ser eficiente:

- Paralelas, independente durante a transferência de dados para os serviços separados.
- Obter todos os dados para um determinado serviço de um intervalo de datas.
- Obter os dados mais recentes para um serviço.

### <a name="telemetry-table-storage-output-schema"></a>Esquema de armazenamento e saída da tabela de telemetria

Dados de telemetria são armazenados no agregado numa tabela, "TelemetryMetrics20160321" onde "20160321" é a data da tabela criada. Sistema de telemetria cria uma tabela em separado para cada dia nova com base em UTC de 00:00. A tabela é utilizada para armazenar valores recorrentes, tais como ingestão de velocidade de transmissão numa determinada janela de tempo, os bytes enviados, etc. 

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|{ID da conta} _ {ID de entidade}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66 < br /<br/>O ID de conta está incluído na chave de partição para simplificar a fluxos de trabalho em que várias contas de serviços de suporte de dados estiver a escrever para a mesma conta de armazenamento.
RowKey|_ {segundos meia-noite} {value aleatório}|01688_00199<br/><br/>A chave de linha começa com o número de segundos a meia-noite para permitir que as consultas de estilo n principais dentro de uma partição. Para obter mais informações, consulte [isto](../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artigo. 
Timestamp|Data/hora|Auto timestamp da tabela do Azure 2016-09-09T22:43:42.241Z
Tipo|O tipo de entidade que fornece dados de telemetria|Canal/StreamingEndpoint/arquivo<br/><br/>Tipo de evento é apenas um valor de cadeia.
Nome|O nome do evento de telemetria|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|A hora que do evento de telemetria Ocorreu (UTC)|2016-09-09T22:42:36.924Z<br/><br/>O tempo observado é fornecido pela entidade enviar a telemetria (por exemplo um canal). Podem existir problemas de sincronização entre os componentes para que este valor é aproximada do tempo
ServiceID|{ID do serviço}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Propriedades de entidade específica|Conforme definido pelo evento|StreamName: stream1, velocidade de transmissão 10123,...<br/><br/>As propriedades restantes são definidas para o tipo de eventos fornecido. Conteúdo de tabela do Azure é pares de valor de chave.  (ou seja, diferentes linhas na tabela têm conjuntos diferentes de propriedades).

### <a name="entity-specific-schema"></a>Esquema de entidade específica

Existem três tipos de entradas de dados específicos da entidade de telemetric cada instalados com a seguinte frequência:

- Pontos finais de transmissão em fluxo: cada 30 segundos
- Em direto canais: cada minuto
- Em direto arquivo: cada minuto

**Ponto final de transmissão em fluxo**

Propriedade|Valor|Exemplos
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Auto timestamp de Azure Table 2016-09-09T22:43:42.241Z
Tipo|Tipo|StreamingEndpoint
Nome|Nome|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID do serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Nome de anfitrião|Nome de anfitrião do ponto final|builddemoserver.Origin.mediaservices.Windows.NET
statusCode|Estado HTTP de registos|200
resultCode|Detalhe de código de resultado|S_OK
requestCount|Pedido total na agregação|3
BytesSent|Agregados bytes enviados|2987358
ServerLatency|Latência média de servidor (incluindo o armazenamento)|129
E2ELatency|Latência média de ponto a ponto|250

**Canais em direto**

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Auto timestamp da tabela do Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|Canal
Nome|Nome|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID do serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Tipo de controlar vídeo/áudio/texto|áudio/vídeo
TrackName|Nome da controlar|vídeo/audio_1
Velocidade de transmissão|Controlar velocidade de transmissão|785000
CustomAttributes||   
IncomingBitrate|Velocidade de transmissão de entrada real|784548
OverlapCount|Se sobrepõe da inserção|0
DiscontinuityCount|Descontinuidade para controlar|0
LastTimestamp|Último carimbo de data transmissões em|1800488800
NonincreasingCount|Contagem de fragmentos rejeitados devido a não aumentar timestamp|2
UnalignedKeyFrames|Se recebemos fragment(s) (em níveis de qualidade) onde chave frames não alinhados |Verdadeiro
UnalignedPresentationTime|Indica se recebemos fragment(s) (em níveis de qualidade/faixas) onde o tempo de apresentação não está alinhado|Verdadeiro
UnexpectedBitrate|VERDADEIRO, se calculado/real velocidade de transmissão de áudio/vídeo controlar > 40 000 bps e IncomingBitrate = = 0 ou IncomingBitrate e actualBitrate diferirem em 50% |Verdadeiro
Bom estado de funcionamento|Se for VERDADEIRO, <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> são todos os 0|Verdadeiro<br/><br/>Bom estado de funcionamento é uma função composta que devolve um valor false quando qualquer uma das seguintes condições reter:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames = = True<br/>-UnalignedPresentationTime = = True<br/>-UnexpectedBitrate = = True

**Arquivo em direto**

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Auto timestamp da tabela do Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|Arquivo
Nome|Nome|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID do serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Url de programa|Asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4BD2-8c01-a92a2b38c9ba.ISM
TrackName|Nome da controlar|audio_1
TrackType|Tipo da controlar|Áudio/vídeo
CustomAttribute|Hexadecimais de cadeia que distingue entre diferentes controlar com o mesmo nome e a velocidade de transmissão (ângulo câmara de várias)|
Velocidade de transmissão|Controlar velocidade de transmissão|785000
Bom estado de funcionamento|Se for VERDADEIRO, FragmentDiscardedCount = = 0 & & ArchiveAcquisitionError = = False|VERDADEIRO (estes dois valores não estão presentes na métrica mas estiverem presentes no evento de origem)<br/><br/>Bom estado de funcionamento é uma função composta que devolve um valor false quando qualquer uma das seguintes condições reter:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError = = True

## <a name="general-qa"></a>Geral perguntas e respostas

### <a name="how-to-consume-metrics-data"></a>Como consumir os dados de métricas?

Dados de métricas são armazenados como uma série de tabelas do Azure numa conta de armazenamento do cliente. Estes dados podem ser utilizados com as ferramentas seguintes:

- SDK DO AMS
- Explorador de armazenamento do Microsoft Azure (suporta a exportação para o formato de valores separados por vírgulas e processado no Excel)
- API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Como localizar o consumo de largura de banda média?

O consumo de largura de banda média é a média de BytesSent através de um intervalo de tempo.

### <a name="how-to-define-streaming-unit-count"></a>Como definir a contagem de unidades de transmissão em fluxo?

A contagem de unidades de transmissão em fluxo pode ser definida como o débito de pico de transmissão em fluxo pontos finais o serviço dividido pelo débito de pico de um ponto final de transmissão em fluxo. O débito de utilizável de pico de um ponto final de transmissão em fluxo é 160 Mbps.
Por exemplo, suponha que o débito de pico do serviço de um cliente é de 40 MBps (o valor máximo de BytesSent através de um intervalo de tempo). Em seguida, a contagem de unidades de transmissão em fluxo é igual a (40 MBps) * (8 bits/byte) /(160 Mbps) = 2 unidades de transmissão em fluxo.

### <a name="how-to-find-average-requestssecond"></a>Como localizar média de pedidos por segundo?

Para localizar o número médio de pedidos por segundo, calcule o número médio de pedidos (RequestCount) através de um intervalo de tempo.

### <a name="how-to-define-channel-health"></a>Como definir o estado de funcionamento do canal?

Estado de funcionamento do canal pode ser definido como um compostos função booleana que é falso quando qualquer uma das seguintes condições reter:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames = = True 
- UnalignedPresentationTime = = True 
- UnexpectedBitrate = = True


### <a name="how-to-detect-discontinuities"></a>Como detetar discontinuities?

Para detetar discontinuities, localizar todas as entradas de canal de dados onde DiscontinuityCount > 0. O carimbo de ObservedTime correspondente indica o número de vezes que ocorreu os discontinuities.

### <a name="how-to-detect-timestamp-overlaps"></a>Como detetar timestamp sobreposições?

Para detetar sobreposições timestamp, localizar todas as entradas de canal de dados onde OverlapCount > 0. O carimbo de ObservedTime correspondente indica o número de vezes que o timestamp sobreposições ocorreu.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Como localizar falhas de pedido de transmissão em fluxo e as razões?

Para localizar falhas de pedido de transmissão em fluxo e as razões, localize todas as entradas de dados de ponto final de transmissão em fluxo em que não é igual a S_OK ResultCode. O campo de StatusCode correspondente indica a razão da falha do pedido.

### <a name="how-to-consume-data-with-external-tools"></a>Como consumir dados com ferramentas externas?

Dados telemetric podem ser processados e visualizados com as ferramentas seguintes:

- PowerBI
- Application Insights
- Monitor do Azure (anteriormente Shoebox)
- Dashboard em direto do AMS
- Portal do Azure (pendente lançamento)

### <a name="how-to-manage-data-retention"></a>Como gerir retenção de dados?

O sistema de telemetria não fornece gestão de retenção de dados ou eliminação automática antigo de registos. Assim, tem de gerir e eliminar manualmente os registos antigos da tabela de armazenamento. Pode consultar SDK de armazenamento para como fazê-lo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
