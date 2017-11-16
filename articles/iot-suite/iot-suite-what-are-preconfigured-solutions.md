---
title: "Descrição geral das soluções pré-configuradas do Azure IoT Suite | Microsoft Docs"
description: "Uma descrição das soluções pré-configuradas do Azure IoT Suite e da sua arquitetura com ligações para recursos adicionais."
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
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 5c5fa4927073ff52418a940fce59ca1f6b57daa6
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-iot-suite"></a>O que é o Azure IoT Suite?

O Azure IoT Suite é um conjunto de *soluções pré-configuradas* que:

* São implementadas em minutos
* Ajudam a começar rapidamente
* Podem ser personalizadas para satisfazer os seus requisitos específicos

Todas as soluções pré-configuradas do *IoT Suite* foram concebidas de acordo com os mesmos princípios e objetivos.

## <a name="preconfigured-solutions-overview"></a>Descrição geral das soluções pré-configuradas

As soluções pré-configuradas são implementações de código aberto de padrões comuns de soluções IoT que pode implementar no Azure através da sua subscrição. Cada solução pré-configurada combina código personalizado e serviços do Azure para implementar um ou mais cenários de IoT específicos. Pode personalizar qualquer um destes cenários para satisfazer os seus requisitos específicos. Os cenários incluem:

* Ver dados num dashboard avançado para obter estatísticas aprofundadas e o estado da solução.
* Configurar regras e alarmes sobre telemetria dos dispositivos IoT em direto.
* Agendar tarefas de gestão de dispositivos, como atualizações de software e configuração.
* Aprovisionar os seus próprios dispositivos físicos ou simulados personalizados.
* Resolver problemas e remediar problemas nos seus grupos de dispositivos IoT.

Cada solução pré-configurada é uma implementação ponto a ponto e completa que pode utilizar dispositivos simulados ou físicos para gerar a telemetria. Pode utilizar as soluções pré-configuradas como aceleradores de soluções para:

* Funcionar como ponto de partida para as suas próprias soluções de IoT.
* Saber mais sobre os padrões comuns na conceção e desenvolvimento da solução IoT.

Atualmente, estão disponíveis três soluções pré-configuradas:

* [Monitorização remota](iot-suite-remote-monitoring-explore.md)
* [Manutenção preditiva](iot-suite-predictive-overview.md)
* [Fábrica ligada](iot-suite-connected-factory-overview.md)

A tabela seguinte mostra a forma como as soluções mapeiam funcionalidades específicas do IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Gestão de dispositivos | Processamento na periferia | Comando e controlo | Regras e ações | Análise preditiva |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Monitorização remota](iot-suite-remote-monitoring-explore.md)  |Sim |Sim |Sim |-   |Sim |Sim |-   |
| [Manutenção preditiva](iot-suite-predictive-overview.md)   |Sim |Sim |-   |-   |Sim |Sim |Sim |
| [Fábrica ligada](iot-suite-connected-factory-overview.md) |Sim |Sim |Sim |Sim |Sim |Sim |-   |

* *Ingestão de dados*: Entrada de dados à escala na nuvem.
* *Identidade do dispositivo*: Gerir identidades únicas de cada dispositivo e controlar o acesso do dispositivo à solução.
* *Gestão de dispositivos*: Gerir metadados de dispositivos e executar operações, como reinícios de dispositivo e atualizações de firmware.
* *Comando e controlo*: para fazer com que o dispositivo realize uma ação, envie mensagens da cloud para um dispositivo.
* *Regras e ações*: para realizar ações em dados de dispositivo para a cloud específicos, o back-end da solução utiliza regras.
* *Análise preditiva*: A solução de back-end analisa os dados do dispositivo para a cloud para prever quando as ações específicas deverão ocorrer. Por exemplo, analisar a telemetria do motor de uma aeronave para determinar quando deverá ser realizada a manutenção.

> [!NOTE]
> Para implementar uma solução pré-configurada e saber mais sobre como pode personalizá-la, visite [Microsoft Azure IoT Suite](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Serviços do Azure

Quando implementa uma solução pré-configurada, o processo de aprovisionamento configura vários serviços do Azure. A tabela seguinte mostra os serviços utilizados nas soluções pré-configuradas:

|                      | Monitorização remota  | Manutenção preditiva | Fábrica ligada |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | Sim                |                        | Sim               |
| Event Hubs           |                    | Sim                    |                   |
| Time Series Insights |                    |                        | Sim               |
| Serviços de Contentores   | Sim                |                        | Sim               |
| Stream Analytics     |                    | Sim                    |                   |
| Aplicações Web             | Sim                | Sim                    | Sim               |
| Cosmos DB            | Sim                | Sim                    | Sim               |
| Tabelas do Azure         |                    | Sim                    | Sim               |

> [!NOTE]
> Para obter mais informações sobre os recursos implementados na solução pré-configurada de monitorização remota, veja este [artigo](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) no GitHub.

* [Hub IoT do Azure](../iot-hub/index.md). Este serviço fornece funcionalidades de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo e funciona como gateway para a nuvem e outros serviços essenciais do IoT Suite. O serviço permite-lhe receber mensagens dos seus dispositivos de escala e enviar comandos aos seus dispositivos. O serviço também lhe permite [gerir os seus dispositivos](../iot-hub/iot-hub-device-management-overview.md). Por exemplo, pode configurar, reiniciar ou efetuar uma reposição de fábrica num ou mais dispositivos ligados ao hub.
* [Hubs de Eventos do Azure](../event-hubs/index.md). Este serviço disponibiliza ingestão de elevados volumes de eventos na cloud. Veja [Comparação do Hub IoT do Azure e Hubs de Eventos do Azure](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). As soluções pré-configuradas utilizam este serviço para analisar e apresentar os dados de telemetria dos seus dispositivos.
* [Azure Container Service](../container-service/index.yml). Este serviço aloja e gere os microsserviços das soluções pré-configuradas.
* [Azure Cosmos DB](../cosmos-db/index.yml) e [Armazenamento do Azure](../storage/index.yml) para armazenamento de dados.
* [Azure Stream Analytics](../stream-analytics/index.md). A solução pré-configurada de manutenção preditiva utiliza este serviço para processar a telemetria de entrada, executar a agregação e detetar eventos. Esta solução pré-configurada utiliza ainda o Stream Analytics para processar mensagens de informações que contêm dados como metadados ou respostas aos comandos dos dispositivos.
* [Aplicações Web do Azure](../app-service/index.yml), para alojar o código da aplicação personalizada nas soluções pré-configuradas.

Para uma descrição geral da arquitetura de uma solução IoT típica, veja [Microsoft Azure e a Internet das Coisas (IoT)](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-preconfigured-solutions"></a>Quais são as novidades das soluções pré-configuradas?

A Microsoft está a atualizar as soluções pré-configuradas para uma nova arquitetura baseada em microsserviços. A tabela abaixo mostra o estado atual das soluções pré-configuradas:

| Solução pré-configurada | Arquitetura  | Linguagens     |
| ---------------------- | ------------- | ------------- |
| Monitorização remota      | Microsserviços | Java e .NET |
| Manutenção preditiva | MVC           | .NET          |
| Fábrica ligada      | MVC           | .NET          |

As secções seguintes descrevem as novidades das soluções pré-configuradas com base em microsserviços:

### <a name="microservices"></a>Microsserviços

A nova versão da solução pré-configurada de monitorização remota utiliza uma arquitetura de microsserviços. É composta por múltiplos microsserviços, como um *gestor do Hub IoT* e um *gestor de Armazenamento*. Tanto a versão Java, como .NET, de cada microssserviço está disponível para transferência, juntamente com a documentação de programação associada. Para obter mais informações sobre os microsserviços, veja [Arquitetura da monitorização remota](iot-suite-remote-monitoring-sample-walkthrough.md).

Esta arquitetura de microsserviços é um padrão comprovado para soluções na cloud que:

* É escalável.
* Permite a extensibilidade.
* É fácil de compreender.
* Permite a substituição de serviços individuais por alternativas.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviços, veja [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Arquitetura de Aplicações .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microsserviços: uma revolução nas aplicações com tecnologia da cloud).

Quando implementa a versão nova da monitorização remota, tem de selecionar uma das opções de implementação seguintes:

* **Básica:** versão com custo reduzido para demonstração ou para testar implementações. Todos os microsserviços são implementados numa máquina virtual do Azure individual.
* **Standard:** implementação de infraestrutura expandida para desenvolver uma implementação de produção. O Azure Container Service implementa os microsserviços em várias máquinas virtuais do Azure. O Kubernetes orquestra os contentores do Docker que alojam os microsserviços individuais.

### <a name="language-choices-java-and-net"></a>Opções de linguagem: Java e .NET

As implementações de cada uma dos microsserviços estão disponíveis em Java e .NET. Tal como o código .NET, o código de origem Java é aberto e está disponível para personalização, de modo a satisfazer os seus requisitos específicos:

* [Remote monitoring .NET GitHub repository](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) (Repositório do GitHub de .NET da monitorização remota)
* [Remote monitoring Java GitHub repository](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) (Repositório do GitHub de Java da monitorização remota)

Se quiser ver implementações noutras linguagens, adicione um pedido ao [fórum de utilizadores do Azure IoT](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Estrutura da interface de utilizador do React

A IU é criada com a biblioteca de javascript do [React](https://facebook.github.io/react/). O código de origem é aberto e está disponível para transferência e personalização.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral das soluções pré-configuradas do IoT Suite, seguem-se os passos seguintes sugeridos para cada uma das soluções:

* [Explorar o modelo de implementação Resource Manager da solução de monitorização remota do Azure IoT Suite](iot-suite-remote-monitoring-explore.md).
* [Descrição geral da solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md).
* [Introdução à solução pré-configurada de fábrica ligada](iot-suite-connected-factory-overview.md).

Para obter mais informações sobre as arquiteturas das soluções IoT, veja [Microsoft Azure IoT services: Reference Architecture](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf) (Serviços do Microsoft Azure IoT: Arquitetura de Referência).
