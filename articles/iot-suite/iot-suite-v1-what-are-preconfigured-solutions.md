---
title: "Soluções pré-configuradas do Azure IoT | Microsoft Docs"
description: "Uma descrição das soluções pré-configuradas do Azure IoT e da sua arquitetura com ligações para recursos adicionais."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 76df013e8e5868fcc9f5d95aa523a6a56dea7163
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Quais são as soluções pré-configuradas do Azure IoT Suite?

As soluções pré-configuradas do Azure IoT Suite são implementações de padrões comuns da solução IoT que pode implementar no Azure através da sua subscrição. Pode utilizar as soluções pré-configuradas:

* Como um ponto de partida para as suas próprias soluções de IoT.
* Para saber mais sobre os padrões comuns na conceção e desenvolvimento da solução de IoT.

Cada solução pré-configurada é uma implementação ponto-a-ponto completa que utiliza dispositivos para gerar a telemetria.

Pode transferir o código de origem completo para personalizar e expandir a solução, de modo a satisfazer os seus requisitos de IoT específicos.

> [!NOTE]
> Para implementar uma das soluções pré-configuradas, consulte [Microsoft Azure IoT Suite][lnk-azureiotsuite]. O artigo [Get started with the IoT preconfigured solutions (Introdução às soluções pré-configuradas do IoT)][lnk-getstarted-preconfigured] fornece mais informações sobre a implementação e a execução de uma das soluções.

A tabela seguinte mostra a forma como as soluções mapeiam funcionalidades específicas do IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Gestão de dispositivos | Comando e controlo | Regras e ações | Análise preditiva |
| --- | --- | --- | --- | --- | --- | --- |
| [Monitorização remota][lnk-getstarted-preconfigured] |Sim |Sim |Sim |Sim |Sim |- |
| [Manutenção preditiva][lnk-predictive-maintenance] |Sim |Sim |- |Sim |Sim |Sim |
| [Fábrica ligada][lnk-getstarted-factory] |Sim |Sim |Sim |Sim |Sim |- |

* *Ingestão de dados*: Entrada de dados à escala na nuvem.
* *Identidade do dispositivo*: Gerir identidades únicas de cada dispositivo e controlar o acesso do dispositivo à solução.
* *Gestão de dispositivos*: Gerir metadados de dispositivos e executar operações, como reinícios de dispositivo e atualizações de firmware.
* *Comando e controlo*: para fazer com que o dispositivo realize uma ação, envie mensagens da cloud para um dispositivo.
* *Regras e ações*: para realizar ações em dados de dispositivo para a cloud específicos, o back-end da solução utiliza regras.
* *Análise preditiva*: A solução de back-end analisa os dados do dispositivo para a cloud para prever quando as ações específicas deverão ocorrer. Por exemplo, analisar a telemetria do motor de uma aeronave para determinar quando deverá ser realizada a manutenção.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Descrição geral da solução pré-configurada de Monitorização Remota

Escolhemos discutir a solução pré-configurada de monitorização remota neste artigo porque ilustre muitos dos elementos comuns de conceção partilhados com outras soluções.

O diagrama seguinte ilustra os principais elementos da solução de monitorização remota. As secções seguintes fornecem mais informações sobre estes elementos.

![Arquitetura da solução pré-configurada de monitorização remota][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivos

Ao implementar a solução pré-configurada de monitorização remota, quatro dispositivos simulados são pré-aprovisionados na solução que simula um dispositivo de arrefecimento. Estes dispositivos simulados têm um modelo de temperatura e humidade integrado que emite a telemetria. Estes dispositivos simulados estão incluídos para:

- Ilustrar o fluxo ponto a ponto de dados através da solução.
- Fornecer uma origem conveniente de telemetria.
- Fornecer um destino para métodos ou comandos se for um programador de back-end a utilizar a solução como um ponto de partida para uma implementação personalizada.

Os dispositivos simulados na solução podem responder às seguintes comunicações da cloud para o dispositivo:

- *Métodos ([métodos diretos][lnk-direct-methods])*: um método de comunicação bidirecional onde se espera que um dispositivo ligado responda imediatamente.
- *Comandos (mensagens da cloud para o dispositivo)*: um método de comunicação unidirecional onde um dispositivo obtém o comando a partir de uma fila durável.

Para obter uma comparação destas abordagens diferentes, veja [Cloud-to-device communications guidance (Documentação de orientação sobre comunicações da cloud para dispositivos)][lnk-c2d-guidance].

Quando um dispositivo é ligado pela primeira vez ao Hub IoT na solução pré-configurada, envia uma mensagem informativa do dispositivo para o hub. Esta mensagem enumera os métodos aos quais o dispositivo pode responder. Na solução pré-configurada de monitorização remota, os dispositivos simulados suportam estes métodos:

* *Iniciar a Atualização de Firmware*: este método inicia uma tarefa assíncrona no dispositivo para realizar uma atualização de firmware. A tarefa assíncrona utiliza propriedades comunicadas para a entrega de atualizações de estado ao dashboard da solução.
* *Reiniciar*: este método faz com que o dispositivo simulado reinicie.
* *FactoryReset*: este método aciona uma reposição de fábrica num dispositivo simulado.

Quando um dispositivo é ligado pela primeira vez ao Hub IoT na solução pré-configurada, envia uma mensagem informativa do dispositivo para o hub. Esta mensagem enumera os comandos aos quais o dispositivo pode responder. Na solução pré-configurada de monitorização remota, os dispositivos simulados suportam estes comandos:

* *Ping Device*: O dispositivo responde a este comando com uma confirmação. Este comando é útil para verificar se o dispositivo ainda está ativo e à escuta.
* *Iniciar telemetria*: Dá instruções ao dispositivo para começar a enviar telemetria.
* *Parar telemetria*: Dá instruções ao dispositivo para parar de enviar telemetria.
* *Alterar Temperatura do Ponto de Referência*: Controla os valores de telemetria de temperatura simulada que o dispositivo envia. Este comando é útil para testar a lógica de back-end.
* *Diagnosticar Telemetria*: Controla se o dispositivo deve enviar a temperatura externa como telemetria.
* *Alterar Estado do Dispositivo*: define a propriedade de metadados do estado do dispositivo que o dispositivo comunica. Este comando é útil para testar a lógica de back-end.

Pode adicionar mais dispositivos simulados à solução, que emitem a mesma telemetria e respondem aos mesmos métodos e comandos.

Para além de responder aos comandos e métodos, a solução utiliza [dispositivos duplos][lnk-device-twin]. Os dispositivos utilizam dispositivos duplos para comunicar os valores de propriedades ao back-end da solução. O dashboard da solução utiliza dispositivos duplos para definir para novos valores de propriedades pretendidas nos dispositivos. Por exemplo, durante o processo de atualização de firmware, o dispositivo simulado comunica o estado da atualização utilizando propriedades comunicadas.

## <a name="iot-hub"></a>IoT Hub

Nesta solução pré-configurada, a instância Hub IoT corresponde ao *Gateway na Cloud* numa [Arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

Um IoT Hub recebe a telemetria dos dispositivos num ponto final único. Mantém ainda os pontos finais do dispositivo onde cada dispositivo pode obter os comandos que lhe são enviados.

O IoT Hub disponibiliza a telemetria recebida através do ponto final de leitura de telemetria do lado do serviço.

A capacidade de gestão de dispositivos do Hub IoT permite-lhe gerir as propriedades do seu dispositivo a partir do portal da solução e agendar tarefas que executam operações como:

- Reiniciar dispositivos
- Alterar estados de dispositivos
- Atualizações de firmware

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

A solução pré-configurada utiliza três tarefas do [Azure Stream Analytics][lnk-asa] (ASA) para filtrar o fluxo de telemetria dos dispositivos:

* *Tarefa DeviceInfo* - produz dados para um hub de eventos que redireciona mensagens específicas do registo do dispositivo para o registo do dispositivo da solução. Este registo de dispositivo é uma base de dados do Azure Cosmos DB. Estas mensagens são enviadas quando um dispositivo é ligado pela primeira vez ou em resposta a um comando **Alterar estado do dispositivo**.
* *Tarefa de telemetria* - envia telemetria não processada ao Blob Storage do Azure para um armazenamento de frio e calcula as agregações de telemetria apresentadas no dashboard de solução.
* *Tarefa de regras* - filtra o fluxo de telemetria para valores que excedem quaisquer limiares de regra e produz os dados para um Event Hub. Quando uma regra é acionada, a vista do dashboard do portal da solução apresenta este evento como uma nova linha na tabela do histórico de alarmes. Estas regras também podem acionar uma ação com base nas definições especificadas nas vistas **Regras** e **Ações** no portal da solução.

Nesta solução pré-configurada, as tarefas ASA fazem parte da **solução de back-end do IoT** numa [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## <a name="event-processor"></a>Processador de eventos

Nesta solução pré-configurada, o processador de eventos faz parte da **solução de back-end do IoT** numa [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

As tarefas ASA **DeviceInfo** e **Regras** enviam o respetivo resultado aos Hubs de Eventos para que seja transmitido a outros serviços de back-end. A solução utiliza uma instância [EventPocessorHost][lnk-event-processor] executada numa [WebJob][lnk-web-job] para ler as mensagens a partir destes Hubs de eventos. O **EventProcessorHost** utiliza:
- Os dados de **DeviceInfo** para atualizar os dados do dispositivo na base de dados do Cosmos DB.
- O dados de **Regras** para invocar a Aplicação lógica e atualizar a apresentação dos alertas no portal da solução.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Registo de identidade do dispositivo, dispositivo duplo e do Cosmos DB

Cada Hub IoT inclui um [registo de identidade do dispositivo][lnk-identity-registry] que armazena as chaves de dispositivo. O IoT Hub utiliza essas informações para autenticar dispositivos - um dispositivo deve estar registado e ter uma chave válida antes de estabelecer a ligação ao hub.

Um [dispositivo duplo][lnk-device-twin] é um documento JSON gerido pelo Hub IoT. Um dispositivo duplo para um dispositivo contém:

- Propriedades comunicadas enviadas pelo dispositivo para o hub. Pode ver estas propriedades no portal da solução.
- Propriedades pretendidas que quer enviar para o dispositivo. Pode definir estas propriedades no portal da solução.
- Etiquetas que existem apenas no dispositivo duplo e não no dispositivo. Pode utilizar estas etiquetas para filtrar as listas de dispositivos no portal da solução.

Esta solução utiliza dispositivos duplos para gerir metadados do dispositivo. A solução também utiliza uma base de dados do Cosmos DB para armazenar dados adicionais de dispositivos específicos da solução, tais como os comandos suportados por cada dispositivo e o histórico de comandos.

A solução deve ainda manter as informações do registo de identidade do dispositivo sincronizadas com os conteúdos da base de dados do Cosmos DB. O **EventProcessorHost** utiliza os dados da tarefa **DeviceInfo** do stream analytics para gerir a sincronização.

## <a name="solution-portal"></a>Portal de solução

![portal de solução][img-dashboard]

O portal de solução é uma IU baseada na Web implementada na nuvem como parte da solução pré-configurada. O portal permite-lhe:

* Ver a telemetria e o histórico de alarmes num dashboard.
* Aprovisionar novos dispositivos.
* Gerir e monitorizar dispositivos.
* Enviar comandos para dispositivos específicos.
* Invocar métodos em dispositivos específicos.
* Gerir regras e ações.
* Agendar tarefas para executar num ou mais dispositivos.

Nesta solução pré-configurada, o portal de solução faz parte da **Solução de back-end do IoT** e do **Processamento e conectividade de negócios** numa [Arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre as arquiteturas da solução IoT, consulte [Microsoft Azure IoT services: Reference Architecture (Serviços do Microsoft Azure IoT: Arquitetura de Referência)][lnk-refarch].

Agora que sabe o que é uma solução pré-configurada, pode começar por implementar a solução pré-configurada de *monitorização remota*: [Introdução às soluções pré-configuradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-v1-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-v1-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]: iot-suite-connected-factory-overview.md
