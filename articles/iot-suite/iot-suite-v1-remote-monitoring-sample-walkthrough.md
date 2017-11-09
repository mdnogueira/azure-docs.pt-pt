---
title: "Instruções sobre a solução pré-configurada de Monitorização Remota | Microsoft Docs"
description: "Uma descrição sobre a solução pré-configurada de monitorização remota e respetiva arquitetura do Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 7cef60998cf9e46a8d89f8ad53edd0382e3ce76e
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Instruções sobre a solução pré-configurada de monitorização remota

A [solução pré-configurada][lnk-preconfigured-solutions] de monitorização remota do IoT Suite é uma implementação de uma solução de monitorização ponto a ponto para várias máquinas em execução em localizações remotas. A solução combina serviços-chave do Azure para fornecer uma implementação genérica do cenário de negócios. Pode utilizar a solução como um ponto de partida para a sua própria implementação e [personalizá-la][lnk-customize] para satisfazer os seus requisitos comerciais específicos.

Este artigo acompanha-o através de alguns dos elementos-chave da solução de monitorização remota para que possa compreender como funciona. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos. 
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos da solução pré-configurada:

![Arquitetura lógica](media/iot-suite-v1-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurada, o dispositivo simulado representa um dispositivo de arrefecimento (como um aparelho de ar condicionado para edifícios ou unidade de tratamento do ar).  Ao implementar a solução pré-configurada, também aprovisiona automaticamente quatro dispositivos simulados que são executados num [Trabalho Web do Azure][lnk-webjobs]. Os dispositivos simulados permitem-lhe explorar facilmente o comportamento da solução sem a necessidade de implementar quaisquer dispositivos físicos. Para implementar um dispositivo físico real, veja o tutorial [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a cloud

Cada dispositivo simulado pode enviar os seguintes tipos de mensagens para o Hub IoT:

| Mensagem | Descrição |
| --- | --- |
| Arranque |Quando o dispositivo é iniciado, envia uma mensagem **device-info** que contém informações sobre si próprio para o back-end. Estes dados incluem o ID do dispositivo e uma lista dos comandos e métodos que o dispositivo suporta. |
| Presença |Um dispositivo envia periodicamente uma mensagem de **presença** para comunicar se o dispositivo consegue detetar a presença de um sensor. |
| Telemetria |Um dispositivo envia periodicamente uma mensagem de **telemetria** que comunica valores simulados de temperatura e humidade recolhidos a partir dos sensores simulados do dispositivo. |

> [!NOTE]
> A solução armazena a lista de comandos suportados pelo dispositivo numa base de dados do Cosmos DB e não no dispositivo duplo.

### <a name="properties-and-device-twins"></a>Propriedades e dispositivos duplos

Os dispositivos simulados enviam as seguintes propriedades do dispositivo para o [dispositivo duplo][lnk-device-twins] no hub IoT como *propriedades comunicadas*. O dispositivo envia as propriedades comunicadas no arranque e em resposta a um comando ou método **Alterar Estado do Dispositivo**.

| Propriedade | Objetivo |
| --- | --- |
| Config.TelemetryInterval | Frequência (segundos) com que o dispositivo envia telemetria |
| Config.TemperatureMeanValue | Especifica o valor médio para a telemetria de temperatura simulada |
| Device.DeviceID |ID fornecido ou atribuído quando um dispositivo é criado na solução |
| Device.DeviceState | Estado comunicado pelo dispositivo |
| Device.CreatedTime |Hora em que o dispositivo foi criado na solução |
| Device.StartupTime |Hora em que o dispositivo foi iniciado |
| Device.LastDesiredPropertyChange |O número de versão da última alteração de propriedade pretendida |
| Device.Location.Latitude |Localização de latitude do dispositivo |
| Device.Location.Longitude |Localização de longitude do dispositivo |
| System.Manufacturer |Fabricante do dispositivo |
| System.ModelNumber |Número de modelo do dispositivo |
| System.SerialNumber |Número de série do dispositivo |
| System.FirmwareVersion |Atual versão do firmware do dispositivo |
| System.Platform |Arquitetura da plataforma do dispositivo |
| System.Processor |Processador que executa o dispositivo |
| System.InstalledRAM |Volume da memória RAM instalada no dispositivo |

O simulador implementa estas propriedades nos dispositivos simulados com valores de exemplo. Sempre que o simulador inicializa um dispositivo simulado, o dispositivo comunica os metadados predefinidos ao Hub IoT como propriedades comunicadas. As propriedades comunicadas só podem ser atualizadas pelo dispositivo. Para alterar uma propriedade comunicada, tem de definir uma propriedade pretendida no portal da solução. É da responsabilidade do dispositivo:

1. Obter periodicamente as propriedades pretendidas a partir do hub IoT.
2. Atualizar a respetiva configuração com o valor da propriedade pretendida.
3. Enviar o novo valor de volta para o hub como uma propriedade comunicada.

A partir do dashboard de solução, pode utilizar as *propriedades pretendidas* para definir propriedades num dispositivo através do [dispositivo duplo][lnk-device-twins]. Normalmente, um dispositivo lê um valor da propriedade pretendida a partir do hub para atualizar o estado interno e comunicar a alteração novamente como uma propriedade comunicada.

> [!NOTE]
> O código do dispositivo simulado utiliza apenas as propriedades pretendidas **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** para atualizar as propriedades comunicadas enviadas de volta para o Hub IoT. Todos os outros pedidos de alteração de propriedades pretendidas são ignorados no dispositivo simulado.

### <a name="methods"></a>Métodos

Os dispositivos simulados podem processar os seguintes métodos ([métodos diretos][lnk-direct-methods]) invocados a partir do portal da solução através do hub IoT:

| Método | Descrição |
| --- | --- |
| InitiateFirmwareUpdate |Dá instruções ao dispositivo para executar uma atualização de firmware |
| Reiniciar |Dá instruções ao dispositivo para reiniciar |
| FactoryReset |Dá instruções ao dispositivo para executar uma reposição de fábrica |

Alguns métodos utilizam propriedades comunicadas para comunicar o progresso. Por exemplo, o método **InitiateFirmwareUpdate** simula a execução da atualização no modo assíncrono no dispositivo. O método devolve imediatamente no dispositivo, enquanto a tarefa assíncrona continua a enviar atualizações de estado para o dashboard de solução utilizando as propriedades comunicadas.

### <a name="commands"></a>Comandos

Os dispositivos simulados podem processar os seguintes comandos (mensagens da cloud para o dispositivo) enviados a partir do portal da solução através do hub IoT:

| Comando | Descrição |
| --- | --- |
| PingDevice |Envia um *ping* ao dispositivo para verificar se está ativo |
| StartTelemetry |Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Para o envio de telemetria do dispositivo |
| ChangeSetPointTemp |Altera o valor do ponto de referência em torno do qual foram gerados os dados aleatórios |
| DiagnosticTelemetry |Aciona a simulador de dispositivo para enviar um valor adicional de telemetria (externalTemp) |
| ChangeDeviceState |Altera uma propriedade de estado expandido do dispositivo e envia ao dispositivo a mensagem de informações sobre o dispositivo |

> [!NOTE]
> Para obter uma comparação destes comandos (mensagens da cloud para o dispositivo) e métodos (métodos diretos), veja [Cloud-to-device communications guidance (Documentação de orientação sobre comunicações da cloud para dispositivos)][lnk-c2d-guidance].

## <a name="iot-hub"></a>IoT Hub

O [hub IoT][lnk-iothub] ingere dados enviados a partir dos dispositivos para a cloud e disponibiliza-os para as tarefas do Azure Stream Analytics (ASA). Cada tarefa de transmissão do ASA utiliza um grupo de consumidores do Hub IoT separado para ler a transmissão de mensagens a partir dos seus dispositivos.

O hub IoT na solução também:

- Mantém um registo de identidades que armazena os IDs e as chaves de autenticação de todos os dispositivos autorizados a ligar ao portal. Pode ativar e desativar dispositivos através do registo de identidades.
- Envia comandos para os seus dispositivos em nome do portal da solução.
- Invoca métodos nos seus dispositivos em nome do portal da solução.
- Mantém dispositivos duplos para todos os dispositivos registados. Um dispositivo duplo armazena os valores de propriedades comunicados por um dispositivo. Um dispositivo duplo também armazena as propriedades pretendidas, definidas no portal da solução, para o dispositivo obter da próxima vez que estabelecer ligação.
- Agenda tarefas para definir propriedades para vários dispositivos ou invocar métodos em vários dispositivos.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Na solução de monitorização remota, o [Azure Stream Analytics][lnk-asa] (ASA) envia mensagens do dispositivo recebidas pelo hub IoT para outros componentes de back-end para fins de processamento ou armazenamento. As diferentes tarefas do ASA efetuam funções específicas com base no conteúdo das mensagens.

**Tarefa 1: Informações do dispositivo** filtra as mensagens de informações do dispositivo a partir do fluxo de mensagens de entrada e envia-as para um ponto final do Event Hub. Um dispositivo envia mensagens de informações do dispositivo aquando do arranque e como resposta a um comando **SendDeviceInfo**. Esta tarefa utiliza a definição de consulta seguinte para identificar as mensagens **device-info**:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Esta tarefa envia o resultado para um Hub de Eventos para processamento adicional.

**Tarefa 2: Regras** avalia os valores de entrada relativos à temperatura e humidade relativamente a limiares por cada dispositivo. Os valores de limiar estão definidos no editor de regras disponível no portal da solução. Cada par de dispositivo/valor é armazenado de acordo com o carimbo de data/hora num blob lido pelo Stream Analytics como **Dados de Referência**. A tarefa compara qualquer valor não vazio com o limiar definido para o dispositivo. Se ultrapassar a condição '>', a tarefa emite um evento de **alarme** que indica que o limiar foi excedido e fornece os valores de dispositivo, valor e carimbo de data/hora. Esta tarefa utiliza a definição de consulta seguinte para identificar mensagens de telemetria que devem acionar um alarme:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

A tarefa envia o resultado para um Hub de Eventos para processamento suplementar e guarda os detalhes de cada alerta no armazenamento de blobs, a partir do qual o portal da solução pode ler as informações de alerta.

**Tarefa 3: Telemetria** intervém no fluxo de telemetria de entrada do dispositivo de duas maneiras. A primeira envia todas as mensagens de telemetria dos dispositivos para um armazenamento persistente de blobs a longo prazo. A segunda calcula os valores de humidade mínima, máxima e média numa janela deslizante de cinco minutos e envia estes dados para o armazenamento de blobs. O portal da solução lê os dados de telemetria do armazenamento de blobs para preencher os gráficos. Esta tarefa utiliza a seguinte definição de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Event Hubs

As tarefas do ASA **device info** e **rules** enviam os seus dados para os Hubs de Eventos para que estes os encaminhem de forma fiável para o **Processador de Eventos** em execução no Trabalho Web.

## <a name="azure-storage"></a>Storage do Azure

A solução utiliza o armazenamento de blobs do Azure para manter todos os dados de telemetria não processados e resumidos dos dispositivos na solução. O portal lê os dados de telemetria do armazenamento de blobs para preencher os gráficos. Para apresentar os alertas, o portal da solução lê os dados do armazenamento de blobs que regista quando os valores de telemetria excederam os valores de limiar configurados. A solução também utiliza o armazenamento de blobs para registar os valores de limiar definidos por si no portal da solução.

## <a name="webjobs"></a>Trabalhos Web

Para além de alojar os simuladores de dispositivo, os Trabalhos Web na solução também alojam o **Processador de Eventos** em execução num Trabalho Web do Azure que processa respostas de comandos. Utiliza mensagens de resposta de comandos para atualizar o histórico de comandos do dispositivo (armazenado na base de dados do Cosmos DB).

## <a name="cosmos-db"></a>Cosmos DB

A solução utiliza uma base de dados do Cosmos DB para armazenar informações sobre os dispositivos ligados à solução. Estas informações incluem o histórico dos comandos enviados para os dispositivos a partir do portal da solução e dos métodos invocados a partir do portal da solução.

## <a name="solution-portal"></a>Portal de solução

O portal da solução é uma aplicação Web implementada como parte da solução pré-configurada. As páginas principais no portal de solução são o dashboard e a lista de dispositivos.

### <a name="dashboard"></a>Dashboard

Esta página na aplicação Web utiliza os controlos javascript do PowerBI (veja [PowerBI-visuals repo](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de telemetria dos dispositivos. A solução utiliza a tarefa de telemetria do ASA para escrever dados de telemetria no armazenamento de blobs.

### <a name="device-list"></a>Lista de dispositivos

A partir desta página no portal da solução, poderá:

* Aprovisionar um novo dispositivo. Esta ação define o ID exclusivo do dispositivo e gera a chave de autenticação. Escreve informações sobre o dispositivo no registo de identidade do Hub IoT e na base de dados do Cosmos DB específica da solução.
* Gerir propriedades dos dispositivos. Esta ação inclui a visualização das propriedades existentes e a atualização de novas propriedades.
* Enviar comandos para um dispositivo.
* Ver o histórico de comando de um dispositivo.
* Ativar e desativar dispositivos.

## <a name="next-steps"></a>Passos seguintes

As seguintes mensagens de blogue do TechNet fornecem mais detalhes sobre a solução pré-configurada de monitorização remota:

* [IoT Suite - Sob definições avançadas - Monitorização Remota](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-v1-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Monitorização Remota - Adicionar Dispositivos em Direto e Dispositivos Simulados](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-v1-remote-monitoring-adding-live-and-simulated-devices.aspx)

Pode continuar a introdução ao IoT Suite ao ler os artigos seguintes:

* [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm]
* [Permissões no site azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
