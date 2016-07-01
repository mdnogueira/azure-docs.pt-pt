<properties
 pageTitle="Instruções sobre a solução pré-configurada de Monitorização Remota | Microsoft Azure"
 description="Uma descrição sobre a solução pré-configurada de monitorização remota e respetiva arquitetura do Azure IoT."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="stevehob"/>

# Instruções sobre a solução pré-configurada de monitorização remota

## Introdução

A solução pré-configurada de monitorização remota do IoT suite é uma solução de monitorização ponto-a-ponto para um cenário empresarial que opera várias máquinas em localizações remotas. A solução combina os principais serviços do Azure IoT Suite para fornecer uma implementação genérica do cenário empresarial e é um ponto de partida para os clientes que pretendem implementar este tipo de solução IoT para satisfazer os seus próprios requisitos empresariais.

## Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos da solução pré-configurada:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Dispositivos simulados

Na solução pré-configurada, o dispositivo simulado representa um dispositivo de arrefecimento (como um aparelho de ar condicionado para edifícios ou unidade de tratamento do ar).  Cada dispositivo simulado envia as seguintes mensagens de telemetria ao IoT Hub:


| Mensagem  | Descrição |
|----------|-------------|
| Arranque  | Quando o dispositivo é iniciado, envia uma mensagem **informações do dispositivo** com informações sobre si próprio, como o seu id de dispositivo, os metadados do dispositivo, uma lista de comandos que o dispositivo suporta e a atual configuração do dispositivo. |


Os dispositivos simulados enviam as seguintes propriedades do dispositivo como metadados:

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


Os dispositivos simulados podem processar os seguintes comandos enviados a partir de um IoT Hub:

| Comando                | Descrição                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Envia um _ping_ ao dispositivo para verificar se está ativo   |
| StartTelemetry         | Inicia o dispositivo que envia a telemetria                 |
| StopTelemetry          | Para o envio de telemetria do dispositivo             |
| ChangeSetPointTemp     | Altera o valor do ponto de referência em torno do qual foram gerados os dados aleatórios |
| DiagnosticTelemetry    | Aciona a simulador de dispositivo para enviar um valor adicional de telemetria (externalTemp) |
| ChangeDeviceState      | Altera uma propriedade de estado expandido do dispositivo e envia ao dispositivo a mensagem de informações sobre o dispositivo |


A confirmação do comando de dispositivo é fornecida através do IoT Hub.


### Tarefas do Azure Stream Analytics


**Tarefa 1: Informações do dispositivo** filtra as mensagens de informações do dispositivo a partir do fluxo de mensagens de entrada e envia-as para um ponto final do Event Hub. Um dispositivo envia mensagens de informações do dispositivo aquando do arranque e como resposta a um comando **SendDeviceInfo**. Esta tarefa utiliza a seguinte definição de consulta:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

**Tarefa 2: Regras** avalia os valores de entrada relativos à temperatura e humidade relativamente a limiares por cada dispositivo. Os valores de limiar estão definidos no editor de regras incluído na solução. Cada conjunto de dispositivo/valor é armazenado pelo carimbo de data/hora num blob que é lido no Stream Analytics como **Dados de Referência**. A tarefa compara qualquer valor não vazio com o limiar definido para o dispositivo. Se ultrapassar a condição ' >', a tarefa irá emitir um evento de **alarme** que indica que o limiar foi excedido e fornece os valores dispositivo, valor e carimbo de data/hora. Esta tarefa utiliza a seguinte definição de consulta:

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
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

**Tarefa 3: Telemetria** intervém no fluxo de telemetria de entrada do dispositivo de duas maneiras. A primeira envia todas as mensagens de telemetria dos dispositivos ao Blob Storage persistente. A segunda calcula os valores de humidade mínima, máxima e média numa janela deslizante de cinco minutos. Estes dados também são enviados para o Blob Storage. Esta tarefa utiliza a seguinte definição de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

### Processador de Eventos

O **Processador de Eventos** processa mensagens de informações do dispositivo e as respostas de comando. Utiliza:

- Mensagens de informações de dispositivo para atualizar o registo de dispositivos (armazenado na base de dados do DocumentDB) com as atuais informações do dispositivo.
- Mensagens de resposta de comando para atualizar o histórico de comando do dispositivo (armazenado na base de dados do DocumentDB).

## Comecemos o nosso percurso

Esta secção explica-lhe os componentes da solução, descreve o caso de utilização prevista e fornece exemplos.

### Dashboard de Monitorização remota
Esta página na aplicação Web utiliza os controlos javascript do PowerBI (consulte [PowerBI-visuals repo](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de saída das tarefas do Stream Analytics no Blob Storage.


### Portal de Administração do Dispositivo

Esta aplicação Web permite-lhe:

- Aprovisionar um novo dispositivo que define o id único do dispositivo e gere a chave de autenticação.
- Gerir propriedades do dispositivo que incluem as propriedades existentes de visualização e a atualização de novas propriedades.
- Enviar comandos para um dispositivo.
- Ver o histórico de comando de um dispositivo.

### Observar o comportamento da solução na nuvem
Pode ver os recursos que aprovisionou acedendo ao [Portal do Azure](https://portal.azure.com) e, de seguida, ao navegar para o grupo de recursos com o nome de solução que especificou.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Quando executar o exemplo pela primeira vez, irá ver quatro dispositivos simulados pré-configurados.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

Pode utilizar o Portal de Administração do Dispositivo para adicionar um novo dispositivo simulado:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Inicialmente, o estado do novo dispositivo no portal de Administração do Dispositivo é **Pendente**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Quando a aplicação tiver concluído a implementação do dispositivo simulado, irá ver o estado da alteração do dispositivo para **Em execução** no portal de Administração do Dispositivo, como mostrado na seguinte captura de ecrã. A tarefa **DeviceInfo** do Stream Analytics envia as informações do estado do dispositivo ao portal de Administração do Dispositivo.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Através do portal de solução, pode enviar os comandos, tais como **ChangeSetPointTemp** ao dispositivo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Quando o dispositivo comunicar que executou o comando com êxito, o estado passa a **Êxito**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Através do portal de solução, pode pesquisar dispositivos com características específicas, tais como um número de modelo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

Pode desativar um dispositivo e removê-lo após a desativação:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)


## Passos seguintes

As seguintes mensagens de blogue do TechNet fornecem detalhes adicionais sobre a solução pré-configurada de monitorização remota:

- [IoT Suite - Sob definições avançadas - Monitorização Remota](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Monitorização Remota - Adicionar Dispositivos em Direto e Dispositivos Simulados](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)


<!--HONumber=Jun16_HO2-->


