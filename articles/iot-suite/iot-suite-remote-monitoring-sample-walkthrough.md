---
title: "Arquitetura de solução de monitorização remota - Azure | Microsoft Docs"
description: "Obter instruções sobre a arquitetura da solução pré-configurada de monitorização remota."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: e19ba9c88e4fbe4f065c45ce7029247436f7155c
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="remote-monitoring-preconfigured-solution-architecture"></a>Arquitetura da solução pré-configurada de monitorização remota

O IoT Suite monitorização remota [solução pré-configurada](iot-suite-what-are-preconfigured-solutions.md) implementa uma solução de monitorização ponto a ponto para várias máquinas em localizações remotas. A solução combina serviços-chave do Azure para fornecer uma implementação genérica do cenário de negócios. Pode utilizar a solução como um ponto de partida para a sua própria implementação e [personalizar](iot-suite-remote-monitoring-customize.md) para satisfazer os seus requisitos empresariais específicos.

Este artigo acompanha-o através de alguns dos elementos-chave da solução de monitorização remota para que possa compreender como funciona. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos.
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama a seguir descreve os componentes da solução pré-configurada monitorização remota overlaid no lógicos o [arquitetura IoT](iot-suite-what-is-azure-iot.md):

![Arquitetura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Por que motivo micro-serviços?

Arquitetura de nuvem tem evoluiu e deu lugar, uma vez que a Microsoft lançou as primeira soluções pré-configuradas. [Micro-serviços](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) ter emerged como prática comprovada para alcançar a escala e a flexibilidade sem sacrificar a velocidade de desenvolvimento. Vários serviços do Microsoft utilizam este padrão da arquitetura internamente com resultados de escalabilidade e fiabilidade excelente. As soluções pré-configuradas atualizadas colocar estes learnings na prática, para que possa também tirar partido dos mesmos.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviços, veja [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Arquitetura de Aplicações .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microsserviços: uma revolução nas aplicações com tecnologia da cloud).

## <a name="device-connectivity"></a>Conectividade dos dispositivos

A solução inclui os seguintes componentes da parte de conectividade do dispositivo da arquitetura lógica:

### <a name="simulated-devices"></a>Dispositivos simulados

A solução inclui um microsserviço que lhe permite gerir um conjunto de dispositivos simulados para testar o fluxo de ponto a ponto na solução. Os dispositivos simulados:

* Gere telemetria do dispositivo para a nuvem.
* Responda a chamadas de método de nuvem para o dispositivo a partir do IoT Hub.

O microsserviço fornece um ponto final RESTful para que possa criar, iniciar e parar simulações. Cada simulação é composta por um conjunto de dispositivos virtuais de diferentes tipos que enviar telemetria e respondem a chamadas de método.

Pode aprovisionar dispositivos simulados a partir do dashboard no portal de solução.

### <a name="physical-devices"></a>Dispositivos físicos

Pode ligar dispositivos físicos à solução. Pode implementar o comportamento dos seus dispositivos simulados com os SDKs do dispositivo IoT do Azure.

Pode aprovisionar dispositivos físicos a partir do dashboard no portal de solução.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub e o microsserviço do Gestor de IoT

O [IoT hub](../iot-hub/index.md) ingere dados enviados a partir dos dispositivos para a nuvem e torna a mesma disponível para o `telemetry-agent` microsserviço.

O hub IoT na solução também:

* Mantém um registo de identidade que armazena os IDs e chaves de autenticação de todos os dispositivos autorizadas a ligar para o portal. Pode ativar e desativar dispositivos através do registo de identidades.
* Invoca métodos nos seus dispositivos em nome do portal da solução.
* Mantém dispositivos duplos para todos os dispositivos registados. Um dispositivo duplo armazena os valores de propriedades comunicados por um dispositivo. Um dispositivo duplo também armazena as propriedades pretendidas, definidas no portal da solução, para o dispositivo obter da próxima vez que estabelecer ligação.
* Agenda tarefas para definir propriedades para vários dispositivos ou invocar métodos em vários dispositivos.

A solução inclui o `iot-manager` microsserviço para processar as interações com o seu hub IoT, tais como:

* Criar e gerir os dispositivos de IoT.
* Gestão de dispositivos duplos.
* Invocar métodos em dispositivos.
* Gestão de credenciais de IoT.

Este serviço também é executado o IoT Hub consultas para obter os dispositivos que pertencem a grupos definidos pelo utilizador.

O microsserviço fornece um ponto de final RESTful para gerir dispositivos e os dispositivos duplos, invocar métodos e executar consultas de IoT Hub.

## <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

A solução inclui os seguintes componentes no processamento de dados e parte de análise da arquitetura lógica:

### <a name="device-telemetry"></a>Telemetria do dispositivo

A solução inclui dois micro-serviços para processar a telemetria do dispositivo.

O [telemetria-agent](https://github.com/Azure/telemetry-agent-dotnet) microsserviço:

* Armazena a telemetria na base de dados do Cosmos.
* Analisa o fluxo de telemetria dos dispositivos.
* Gera alarmes, de acordo com as regras definidas.

Os alarmes são armazenados na base de dados do Cosmos.

O `telemetry-agent` microsserviço permite que o portal de solução ler a telemetria enviada a partir dos dispositivos. O portal de solução também utiliza este serviço para:

* Definir regras de monitorização, tais como os limiares que acionam alarmes
* Obter a lista de alarmes passados.

Utilize o ponto de final RESTful fornecido por este microsserviço para gerir telemetria, regras e alarmes.

### <a name="storage"></a>Armazenamento

O [adaptador de armazenamento](https://github.com/Azure/pcs-storage-adapter-dotnet) microsserviço é um adaptador à frente o serviço de armazenamento principal utilizado para a solução pré-configurada. Proporciona uma coleção simples e o armazenamento de chave-valor.

A implementação padrão da solução pré-configurada utiliza BD do Cosmos como um serviço de armazenamento principal.

A base de dados do Cosmos DB armazena os dados na solução pré-configurada. O **adaptador de armazenamento** microsserviço age como um adaptador para outros micro-serviços na solução de acesso nos serviços de armazenamento.

## <a name="presentation"></a>Apresentação

A solução inclui os seguintes componentes da parte de apresentação da arquitetura lógica:

O [interface de utilizador web é uma aplicação do Javascript reagir](https://github.com/Azure/pcs-remote-monitoring-webui). A aplicação:

* Utiliza apenas Javascript reagir e é executada inteiramente no browser.
* É escovado com CSS.
* Interage com micro-serviços destinado ao públicos através de chamadas AJAX.

A interface de utilizador apresenta todas as funcionalidades da solução pré-configurada e interage com outros serviços, tais como:

* O [autenticação](https://github.com/Azure/pcs-auth-dotnet) microsserviço para proteger os dados de utilizador.
* O [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) microsserviço para listar e gerir os dispositivos de IoT.

O [IU-config](https://github.com/Azure/pcs-config-dotnet) microsserviço permite que a interface de utilizador armazenar e obter as definições de configuração.

## <a name="next-steps"></a>Passos seguintes

Se pretende explorar a documentação de código e o programador da origem, comece com um repositórios do GitHub principais duas:

* [Solução para com o Azure IoT (.NET) de monitorização remota pré-configurada](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Solução de monitorização remota com o Azure IoT (Java) pré-configurada](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).
* [Solução de arquitetura de monitorização remota pré-configurada de)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Para obter mais informações concetuais sobre a solução pré-configurada de monitorização remota, consulte [personalizar a solução pré-configurada](iot-suite-remote-monitoring-customize.md).
