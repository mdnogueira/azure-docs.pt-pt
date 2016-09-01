<properties
 pageTitle="Instruções sobre a solução pré-configurada de Monitorização Remota | Microsoft Azure"
 description="Uma descrição sobre a solução pré-configurada de monitorização remota e respetiva arquitetura do Azure IoT."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>

# Instruções sobre a solução pré-configurada de monitorização remota

## Introdução

A [solução pré-configurada][lnk-preconfigured-solutions] de monitorização remota do IoT Suite é uma implementação de uma solução de monitorização ponto a ponto para várias máquinas em execução em localizações remotas. A solução combina serviços-chave do Azure para fornecer uma implementação genérica do cenário de negócios e pode utilizá-la como um ponto de partida para a sua própria implementação. Pode [personalizar][lnk-customize] a solução para satisfazer os seus próprios requisitos comerciais.

Este artigo acompanha-o através de alguns dos elementos-chave da solução de monitorização remota para que possa compreender como funciona. Estes conhecimentos ajudam a:

- Resolver problemas na solução.
- Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos. 
- Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

## Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos da solução pré-configurada:

![Arquitetura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## Dispositivos simulados

Na solução pré-configurada, o dispositivo simulado representa um dispositivo de arrefecimento (como um aparelho de ar condicionado para edifícios ou unidade de tratamento do ar).  Ao implementar a solução pré-configurada, também aprovisiona automaticamente quatro dispositivos simulados que são executados num [Trabalho Web do Azure][lnk-webjobs]. Os dispositivos simulados permitem-lhe explorar facilmente o comportamento da solução sem a necessidade de implementar quaisquer dispositivos físicos. Para implementar um dispositivo físico real, veja o tutorial [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm].

Cada dispositivo simulado pode enviar os seguintes tipos de mensagens para o Hub IoT:

| Mensagem  | Descrição |
|----------|-------------|
| Arranque  | Quando o dispositivo é iniciado, envia uma mensagem **device-info** que contém informações sobre si próprio para o back-end. Estes dados incluem o ID e metadados do dispositivo, uma lista dos comandos suportados pelo dispositivo e a configuração atual do dispositivo. |
| Presença | Um dispositivo envia periodicamente uma mensagem de **presença** para comunicar se o dispositivo consegue detetar a presença de um sensor. |
| Telemetria | Um dispositivo envia periodicamente uma mensagem de **telemetria** que comunica valores simulados de temperatura e humidade recolhidos a partir dos sensores simulados do dispositivo. |


Os dispositivos simulados enviam as seguintes propriedades do dispositivo numa mensagem **device-info**:

| Propriedade               |  Objetivo |
|------------------------|--------- |
| ID do dispositivo              | ID fornecido ou atribuído quando um dispositivo é criado na solução. |
| Fabricante           | Fabricante do dispositivo |
| Número de Modelo           | Número de modelo do dispositivo |
| Número de série          | Número de série do dispositivo |
| Firmware               | Atual versão do firmware do dispositivo |
| Plataforma               | Arquitetura da plataforma do dispositivo |
| Processador              | Processador que executa o dispositivo |
| Memória RAM instalada          | Volume da memória RAM instalada no dispositivo |
| Estado Ativado do Hub      | Propriedade do estado do IoT Hub do dispositivo |
| Hora de Criação           | Hora em que o dispositivo foi criado na solução |
| Hora de Atualização           | Hora da última atualização das propriedades do dispositivo |
| Latitude               | Localização de latitude do dispositivo |
| Longitude              | Localização de longitude do dispositivo |

O simulador implementa estas propriedades nos dispositivos simulados com valores de exemplo.  Sempre que o simulador inicializa um dispositivo simulado, o dispositivo publica os metadados predefinidos no IoT Hub. Tenha em atenção que esta ação substitui as atualizações de metadados efetuadas no portal do dispositivo.


Os dispositivos simulados podem processar os seguintes comandos enviados a partir do dashboard da solução através do Hub IoT:

| Comando                | Descrição                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Envia um _ping_ ao dispositivo para verificar se está ativo   |
| StartTelemetry         | Inicia o dispositivo que envia a telemetria                 |
| StopTelemetry          | Para o envio de telemetria do dispositivo             |
| ChangeSetPointTemp     | Altera o valor do ponto de referência em torno do qual foram gerados os dados aleatórios |
| DiagnosticTelemetry    | Aciona a simulador de dispositivo para enviar um valor adicional de telemetria (externalTemp) |
| ChangeDeviceState      | Altera uma propriedade de estado expandido do dispositivo e envia ao dispositivo a mensagem de informações sobre o dispositivo |

A confirmação do comando de dispositivo para o back-end da solução é fornecida através do Hub IoT.

## IoT Hub

O [Hub IoT][lnk-iothub] ingere dados enviados a partir dos dispositivos para a nuvem e disponibiliza-os para as tarefas do Azure Stream Analytics (ASA). O Hub IoT envia também comandos para os seus dispositivos em nome do portal do dispositivo. Cada tarefa de transmissão do ASA utiliza um grupo de consumidores do Hub IoT separado para ler a transmissão de mensagens a partir dos seus dispositivos.

## Azure Stream Analytics

Na solução de monitorização remota, o [Azure Stream Analytics][lnk-asa] (ASA) envia mensagens do dispositivo recebidas pelo Hub IoT para outros componentes de back-end para fins de processamento ou armazenamento. As diferentes tarefas do ASA efetuam funções específicas com base no conteúdo das mensagens.

**Tarefa 1: Informações do dispositivo** filtra as mensagens de informações do dispositivo a partir do fluxo de mensagens de entrada e envia-as para um ponto final do Event Hub. Um dispositivo envia mensagens de informações do dispositivo aquando do arranque e como resposta a um comando **SendDeviceInfo**. Esta tarefa utiliza a definição de consulta seguinte para identificar as mensagens **device-info**:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Esta tarefa envia o resultado para um Hub de Eventos para processamento adicional.

**Tarefa 2: Regras** avalia os valores de entrada relativos à temperatura e humidade relativamente a limiares por cada dispositivo. Os valores de limiar estão definidos no editor de regras disponível no dashboard da solução. Cada par de dispositivo/valor é armazenado de acordo com o carimbo de data/hora num blob lido pelo Stream Analytics como **Dados de Referência**. A tarefa compara qualquer valor não vazio com o limiar definido para o dispositivo. Se ultrapassar a condição '>', a tarefa emite um evento de **alarme** que indica que o limiar foi excedido e fornece os valores de dispositivo, valor e carimbo de data/hora. Esta tarefa utiliza a definição de consulta seguinte para identificar mensagens de telemetria que devem acionar um alarme:

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

A tarefa envia o resultado para um Hub de Eventos para processamento suplementar e guarda os detalhes de cada alerta no armazenamento de blobs, a partir do qual o dashboard da solução pode ler as informações de alerta.

**Tarefa 3: Telemetria** intervém no fluxo de telemetria de entrada do dispositivo de duas maneiras. A primeira envia todas as mensagens de telemetria dos dispositivos para um armazenamento persistente de blobs a longo prazo. A segunda calcula os valores de humidade mínima, máxima e média numa janela deslizante de cinco minutos e envia estes dados para o armazenamento de blobs. O dashboard de solução lê os dados de telemetria do armazenamento de blobs para preencher os gráficos. Esta tarefa utiliza a seguinte definição de consulta:

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

## Event Hubs

As tarefas do ASA **device info** e **rules** enviam os seus dados para os Hubs de Eventos para que estes os encaminhem de forma fiável para o **Processador de Eventos** em execução no Trabalho Web.

## Storage do Azure

A solução utiliza o armazenamento de blobs do Azure para manter todos os dados de telemetria não processados e resumidos dos dispositivos na solução. O dashboard lê os dados de telemetria do armazenamento de blobs para preencher os gráficos. Para apresentar os alertas, o dashboard lê os dados do armazenamento de blobs que registam quando os valores de telemetria excederam os valores de limiar configurados. A solução também utiliza o armazenamento de blobs para registar os valores de limiar definidos por si no dashboard.

## Trabalhos Web

Para além de alojar simuladores de dispositivo, os Trabalhos Web na solução também alojam o **Processador de Eventos** em execução num Trabalho Web do Azure que processa mensagens de informações do dispositivo e as respostas de comandos. Utiliza:

- Mensagens de informações de dispositivo para atualizar o registo de dispositivos (armazenado na base de dados do DocumentDB) com as atuais informações do dispositivo.
- Mensagens de resposta de comando para atualizar o histórico de comando do dispositivo (armazenado na base de dados do DocumentDB).

## DocumentDB

A solução utiliza uma base de dados do DocumentDB para armazenar informações sobre os dispositivos ligados à solução. Estas informações incluem metadados do dispositivo e o histórico dos comandos enviados para os dispositivos a partir do dashboard.

## Web Apps

### Dashboard de monitorização remota
Esta página na aplicação Web utiliza os controlos javascript do PowerBI (consulte [PowerBI-visuals repo](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de telemetria dos dispositivos. A solução utiliza a tarefa de telemetria do ASA para escrever dados de telemetria no armazenamento de blobs.


### Portal de administração do dispositivo

Esta aplicação Web permite-lhe:

- Aprovisionar um novo dispositivo. Esta ação define o ID exclusivo do dispositivo e gera a chave de autenticação. Escreve informações sobre o dispositivo no registo de identidade do Hub IoT e na base de dados do DocumentDB específica da solução.
- Gerir propriedades dos dispositivos. Esta ação inclui a visualização das propriedades existentes e a atualização de novas propriedades.
- Enviar comandos para um dispositivo.
- Ver o histórico de comando de um dispositivo.
- Ativar e desativar dispositivos.

## Passos seguintes

As seguintes mensagens de blogue do TechNet fornecem mais detalhes sobre a solução pré-configurada de monitorização remota:

- [IoT Suite - Sob definições avançadas - Monitorização Remota](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Monitorização Remota - Adicionar Dispositivos em Direto e Dispositivos Simulados](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Pode continuar a introdução ao IoT Suite ao ler os artigos seguintes:

- [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm]
- [Permissions on the azureiotsuite.com site (Permissões no site azureiotsuite.com)][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md


<!--HONumber=ago16_HO4-->


