---
title: "Instruções da solução de fábrica ligada do Azure IoT Suite | Microsoft Docs"
description: "Uma descrição da solução pré-configurada de fábrica ligada do Azure IoT e respetiva arquitetura."
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
ms.date: 05/08/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: fd5e1083c65068c7f6b019838586b1bc7e37aa9f
ms.contentlocale: pt-pt
ms.lasthandoff: 05/11/2017


---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Instruções da solução pré-configurada de fábrica ligada

A [solução pré-configurada][lnk-preconfigured-solutions] de fábrica ligada do IoT Suite é uma implementação de uma solução industrial ponta a ponta que:

* Se liga quer a dispositivos industriais simulados que executem servidores OPC UA em linhas de produção de fábricas simuladas, quer a dispositivos de servidor OPC UA reais.
* Mostra KPIs e OEE operacionais relativos a esses dispositivos e linhas de produção.
* Demonstra como utilizar uma aplicação baseada na cloud para interagir com sistemas de servidores OPC UA.
* Permite-lhe ligar os seus próprios dispositivos de servidor OPC UA.
* Permite-lhe procurar e modificar os dados do servidor OPC UA.
* Integra-se com o serviço Azure Time Series Insights (TSI), para proporcionar vistas personalizadas dos dados a partir dos servidores OPC UA.

Pode utilizar a solução como um ponto de partida para a sua própria implementação e [personalizá-la][lnk-customize] para satisfazer os seus requisitos comerciais específicos.

Este artigo acompanha-o através de alguns dos elementos-chave da solução de fábrica ligada, para que possa compreender como funciona. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos.
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos da solução pré-configurada:

![Arquitetura lógica da fábrica ligada][connected-factory-logical]

## <a name="simulation"></a>Simulação

As estações simuladas e os sistemas de execução de fabrico simulados (MES, Manufacturing Execution Systems) são compostos por linhas de produção de fábrica. Os dispositivos simulados e o Módulo de Publicador OPC baseiam-se na [Normal OPC UA .NET][lnk-OPC-UA-NET-Standard] publicada pela OPC Foundation.

O Proxy OPC e o Publicador OPC são implementados como módulos com base no [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Cada linha de produção simulada tem um gateway designado anexado.

Todos os componentes da simulação são executados em contentores do Docker alojados numa VM do Linux do Azure. A simulação está configurada para executar oito linhas de produção simuladas por predefinição.

## <a name="simulated-production-line"></a>Linha de produção simulada

Uma linha de produção fabrica peças. É composta por diferentes estações: uma estação de montagem, uma de teste e outra de empacotamento.

A simulação é executada e atualiza os dados que são expostos através dos nós de OPC UA. Todas as estações da linha de produção simulada são orquestradas pelo MES através do OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Sistema de execução de fabrico simulado

O MES monitoriza cada estação da linha de produção através de OPC UA para detetar alterações ao estado das estações. Chama métodos de OPC UA para controlar as estações e envia produtos de uma estação para outra até que estejam concluídos.

## <a name="gateway-opc-publisher-module"></a>Módulo de publicador OPC do gateway

O Módulo de Publicador do OPC liga-se aos serviços OPC UA da estação e subscreve os nós do OPC que vão ser publicados. O módulo converte os dados dos nós no formato JSON, encripta-os e envia-os para o Hub IoT como mensagens Pub/Sub de OPC UA.

O Módulo de Publicador OPC só precisa de uma porta https de saída (443) e funciona com a infraestrutura empresarial existente.

## <a name="gateway-opc-proxy-module"></a>Módulo de proxy OPC do gateway

O Módulo de Proxy OPC UA do Gateway faz o túnel de mensagens de comando e controlo binárias de OPC UA e só precisa de uma porta https de saída (443). Funciona com a infraestrutura empresarial existente, incluindo Proxies Web.

Utiliza métodos de Dispositivo Hub IoT para transferir dados TCP/IP empacotados na camada da aplicação e, assim, garante a fiabilidade do ponto final, a encriptação dos dados e a integridade através da utilização de SSL/TLS.

O protocolo binário OPC UA transmitido através do próprio proxy utiliza a autenticação e a encriptação UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

O Módulo de Publicador OPC do Gateway subscreve os nós dos servidores OPC UA para detetar alterações aos valores dos dados. Se for detetada uma alteração aos dados num dos nós, este módulo envia, então, mensagens para o Hub IoT do Azure.

O Hub IoT disponibiliza uma origem de eventos para o Azure TSI. O TSI armazena dados durante 30 dias com base nos carimbos de data/hora anexados às mensagens. Estes dados incluem:

* O ApplicationUri de OPC UA
* O NodeId de OPC UA
* O valor do nó
* O Carimbo de data/hora da origem
* O DisplayName de OPC UA

Atualmente, o TSI não permite aos clientes personalizar o período de tempo durante o qual pretendem manter os dados.

O TSI executa consultas nos dados dos nós com SearchSpan (Time.From, Time.To) e agrega por ApplicationUri, NodeId ou DisplayName de OPC UA.

Para obter os dados relativos aos medidores OEE e KPI, bem como os gráficos de séries temporais, os dados são agregados por contagem de eventos, Soma, Média, Mín e Máx.

As séries temporais são criadas com outro processo. O OEE e os KPIs são calculados a partir dos dados base das estações e agregados para a topologia (linhas de produção, fábricas, empresas) da aplicação.

Além disso, as séries temporais para a topologia de OEE e KPI são calculadas na aplicação, sempre que um intervalo de tempo apresentado estiver pronto. Por exemplo, a vista diária é atualizada a cada hora completa.

A vista de série temporal dos dados de nós vêm diretamente do TSI através de uma agregação para o intervalo de tempo.

## <a name="iot-hub"></a>IoT Hub
O [hub IoT][lnk-IoT Hub] recebe dados enviados a partir do Módulo de Publicador OPC para a cloud e disponibiliza-os para o serviço Azure TSI. 

O Hub IoT na solução também:
- Mantém um registo de identidades que armazena os IDs de todos os Módulos de Publicador OPC e de todos os Módulos de Proxy OPC.
- É utilizado como canal de transporte para comunicação bidirecional do Módulo de Proxy OPC.

## <a name="azure-storage"></a>Storage do Azure
A solução utiliza o Armazenamento de blobs do Azure como armazenamento de discos para a VM e para armazenar dados de implementação.

## <a name="web-app"></a>Aplicação Web
A aplicação Web implementada como parte da solução pré-configurada inclui um cliente OPC UA integrado processamento de alertas e visualização de telemetria.

## <a name="next-steps"></a>Passos seguintes

Pode continuar a introdução ao IoT Suite ao ler os artigos seguintes:

* [Permissões no site azureiotsuite.com][lnk-permissions]

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/azure-iot-gateway-sdk
[lnk-permissions]: iot-suite-permissions.md
