<properties
 pageTitle="Soluções pré-configuradas do Azure IoT | Microsoft Azure"
 description="Uma descrição das soluções pré-configuradas do Azure IoT e da sua arquitetura com ligações para recursos adicionais."
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
 ms.date="08/09/2016"
 ms.author="dobett"/>

# Quais são as soluções pré-configuradas do Azure IoT Suite?

As soluções pré-configuradas do Azure IoT Suite são implementações de padrões comuns da solução IoT que pode implementar no Azure através da sua subscrição. Pode utilizar as soluções pré-configuradas:

- Como um ponto de partida para as suas próprias soluções de IoT.
- Para saber mais sobre os padrões comuns na conceção e desenvolvimento da solução de IoT.

Cada solução pré-configurada é uma implementação ponto-a-ponto completa que utiliza dispositivos para gerar a telemetria.

Para além de implementar e executar as soluções no Azure, pode ainda transferir o código de origem completo e personalizar e expandir a solução para que responda aos seus requisitos específicos do IoT.

> [AZURE.NOTE] Para implementar uma das soluções pré-configuradas, consulte [Microsoft Azure IoT Suite][Ink-azureiotsuite]. O artigo [Introdução às soluções pré-configuradas do IoT][Ink-getstarted-preconfigured] fornece mais informações sobre a implementação e a execução de uma das soluções.

A tabela seguinte mostra a forma como as soluções mapeiam funcionalidades específicas do IoT:

| Solução | Ingestion de Dados | Identidade do Dispositivo | Comando e Controlo | Regras e Ações | Análise Preditiva |
|------------------------|-----|-----|-----|-----|-----|
| [Monitorização remota][lnk-getstarted-preconfigured] | Sim | Sim | Sim | Sim | -   |
| [Manutenção preditiva][lnk-predictive-maintenance] | Sim | Sim | Sim | Sim | Sim |

- *Ingestão de dados*: Entrada de dados à escala na nuvem.
- *Identidade do dispositivo*: Gerir identidades únicas de cada dispositivo ligado.
- *Comando e controlo*: Enviar mensagens a um dispositivo a partir da nuvem para que o dispositivo execute uma ação.
- *Regras e ações*: A solução de back-end utiliza regras para agir sobre dados específicos do dispositivo para a nuvem.
- *Análise preditiva*: A solução de back-end analisa os dados do dispositivo para a nuvem para prever quando as ações específicas deverão ocorrer. Por exemplo, analisar a telemetria do motor de uma aeronave para determinar quando deverá ser realizada a manutenção.

## Descrição geral da solução pré-configurada de Monitorização Remota

Escolhemos discutir a solução pré-configurada de monitorização remota neste artigo porque ilustre muitos dos elementos comuns de conceção partilhados com outras soluções.

O diagrama seguinte ilustra os principais elementos da solução de monitorização remota. As seções abaixo fornecem mais informações sobre estes elementos.

![Arquitetura da solução pré-configurada de monitorização remota][img-remote-monitoring-arch]

## Dispositivos

Ao implementar a solução pré-configurada de monitorização remota, quatro dispositivos simulados são pré-aprovisionados na solução que simula um dispositivo de arrefecimento. Estes dispositivos simulados têm um modelo de temperatura e humidade integrado que emite a telemetria. Estes dispositivos simulados estão incluídos para ilustrar o fluxo de dados ponto-a-ponto através da solução e para fornecer uma origem conveniente de telemetria e um destino para comandos se for um programador de back-end a utilizar a solução como um ponto de partida para uma implementação personalizada.

Quando um dispositivo é ligado pela primeira vez ao IoT Hub na solução pré-configurada de monitorização remota, a mensagem de informações do dispositivo enviada ao IoT Hub enumera a lista de comandos aos quais o dispositivo pode responder. Na solução pré-configurada de monitorização remota, os comandos são: 

- *Ping Device*: O dispositivo responde a este comando com uma confirmação. Isto é útil para a verificar se o dispositivo ainda está ativo e à escuta.
- *Iniciar telemetria*: Dá instruções ao dispositivo para começar a enviar telemetria.
- *Parar telemetria*: Dá instruções ao dispositivo para parar de enviar telemetria.
- *Alterar Temperatura do Ponto de Referência*: Controla os valores de telemetria de temperatura simulada que o dispositivo envia. Isto é útil para fins de teste de lógica de back-end.
- *Diagnosticar Telemetria*: Controla se o dispositivo deve enviar a temperatura externa como telemetria.
- *Alterar Estado do Dispositivo*: define a propriedade de metadados do estado do dispositivo que o dispositivo comunica. Isto é útil para fins de teste de lógica de back-end.

Pode adicionar mais dispositivos simulados à solução que emite a mesma telemetria e responde aos mesmos comandos. 

## IoT Hub

Nesta solução pré-configurada, a instância IoT Hub corresponde ao *Gateway na Nuvem* numa [Arquitetura de solução IoT][Ink-what-is-azure-iot] típica.

Um IoT Hub recebe a telemetria dos dispositivos num ponto final único. Um IoT Hub mantém ainda os pontos finais do dispositivo onde cada dispositivo pode obter os comandos que lhe são enviados.

O IoT Hub disponibiliza a telemetria recebida através do ponto final de leitura de telemetria do lado do serviço.

## Azure Stream Analytics

A solução pré-configurada utiliza três tarefas do [Azure Stream Analytics][Ink-asa] (ASA) para filtrar o fluxo de telemetria dos dispositivos:


- *Tarefa DeviceInfo* - produz dados para Event Hub que redireciona as mensagens de registo específicas do dispositivo, enviadas aquando da primeira ligação de um dispositivo ou em resposta a um comando **Alterar estado do dispositivo** na solução de registo do dispositivo (uma base de dados do DocumentDB). 
- *Tarefa de telemetria* - envia telemetria não processada ao Blob Storage do Azure para um armazenamento de frio e calcula as agregações de telemetria apresentadas no dashboard de solução.
- *Tarefa de regras* - filtra o fluxo de telemetria para valores que excedem quaisquer limiares de regra e produz os dados para um Event Hub. Quando é acionada uma regra, a vista do dashboard do portal de solução mostra este evento como uma nova linha na tabela de histórico de alarme e aciona uma ação baseada nas definições definidas nas vistas Regras e Ações do portal de solução.

Nesta solução pré-configurada, as tarefas ASA fazem parte da **solução de back-end do IoT** numa [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## Processador de eventos

Nesta solução pré-configurada, o processador de eventos faz parte da **solução de back-end do IoT** numa [arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

As tarefas ASA **DeviceInfo** e **Regras** enviam o respetivo resultado ao Event Hubs para que seja transmitido a outros serviços de back-end. A solução utiliza uma instância [EventPocessorHost][Ink-event-processor] executada numa [WebJob][Ink-web-job] para ler as mensagens a partir desses Event Hubs. O **EventProcessorHost** utiliza os dados do **DeviceInfo** para atualizar os dados do dispositivo na base de dados do DocumentDB e utiliza os dados **Regras** para invocar a aplicação lógica e atualizar os alertas apresentados no portal de solução.

## Registo de identidade do dispositivo e DocumentDB

Cada IoT Hub inclui um [registo de identidade do dispositivo][Ink-identity-registry] que armazena as chaves de dispositivo. O IoT Hub utiliza essas informações para autenticar dispositivos - um dispositivo deve estar registado e ter uma chave válida antes de estabelecer a ligação ao hub.

Esta solução armazena informações adicionais sobre os dispositivos, como o seu estado, os comandos que suportam e outros metadados. A solução utiliza uma base de dados do DocumentDB para armazenar dados específicos do dispositivo e o portal de solução recebe dados dessa base de dados do DocumentDB para a visualização e a edição.

A solução deve ainda manter as informações do registo de identidade do dispositivo sincronizadas com os conteúdos da base de dados do DocumentDB. O **EventProcessorHost** utiliza os dados da tarefa **DeviceInfo** do stream analytics para gerir a sincronização.

## Portal de solução

![Dashboard de soluções][img-dashboard]

O portal de solução é uma IU baseada na Web implementada na nuvem como parte da solução pré-configurada. O portal permite-lhe:

- Ver a telemetria e o histórico de alarmes num dashboard.
- Aprovisionar novos dispositivos.
- Gerir e monitorizar dispositivos.
- Enviar comandos para dispositivos específicos.
- Gerir regras e ações.

Nesta solução pré-configurada, o portal de solução faz parte da **Solução de back-end do IoT** e do **Processamento e conectividade de negócios** numa [Arquitetura de solução IoT][lnk-what-is-azure-iot] típica.

## Passos seguintes

Para mais informações sobre as arquiteturas da solução IoT, consulte [Serviços do Microsoft Azure IoT: Arquitetura de Referência][Ink-refarch].

Agora que sabe o que é uma solução pré-configurada, pode começar por implementar a solução pré-configurada de *monitorização remota*: [Introdução às soluções pré-configuradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md


<!--HONumber=ago16_HO4-->


