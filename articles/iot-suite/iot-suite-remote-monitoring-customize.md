---
title: "Personalizar a solução de monitorização remota - Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como pode aceder ao código fonte da solução pré-configurada de monitorização remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/07/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0e15193975b3142f604c2f3c0391b21d6fb1fac1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>Personalizar a solução pré-configurada de monitorização remota

Este artigo fornece informações sobre como pode aceder ao código fonte e personalizar a monitorização remota solução pré-configurada. Descreve o artigo:

* Repositórios do GitHub que contém o código de origem e recursos para os micro-serviços que constituem a solução pré-configurada.
* Cenários comuns de personalização como adicionar um novo tipo de dispositivo.

## <a name="project-overview"></a>Descrição geral do projeto

### <a name="implementations"></a>Implementações

A solução de monitorização remota tem implementações .NET e Java. Ambas as implementações de fornecem uma funcionalidade semelhante e baseiam-se na mesmos subjacentes serviços do Azure. Pode encontrar aqui os nível superior repositórios do GitHub:

* [Solução de .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solução de Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>Microsserviços

Se estiver interessado numa funcionalidade específica da solução, pode aceder a repositórios do GitHub para cada microsserviço individuais. Cada microsserviço implementa outra parte da funcionalidade de solução. Para saber mais sobre a arquitetura geral, consulte o artigo [arquitetura da solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-sample-walkthrough.md).

Esta tabela resume a disponibilidade atual de cada microsserviço para cada idioma:

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Microsserviço      | Descrição | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| IU da Web            | Aplicação Web para a solução de monitorização remota. Implementa IU utilizando React.js framework. | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Gestor de Hub IoT   | Processa a comunicação com o IoT Hub.        | [Disponível](https://github.com/Azure/iothub-manager-java) | [Disponível](https://github.com/Azure/iothub-manager-dotnet)   |
| Autenticação    |  Gere a integração do Active Directory do Azure.  | Ainda não está disponível | [Disponível](https://github.com/Azure/pcs-auth-dotnet)   |
| Simulação de dispositivo | Gere um conjunto de dispositivos simulados. | Ainda não está disponível | [Disponível](https://github.com/Azure/device-simulation-dotnet)   |
| Telemetria         | Disponibiliza a telemetria do dispositivo para a IU. | [Disponível](https://github.com/Azure/device-telemetry-java) | [Disponível](https://github.com/Azure/device-telemetry-dotnet)   |
| Agente de telemetria   | Analisa o fluxo de telemetria, armazena mensagens do IoT Hub do Azure e gera alertas, de acordo com as regras definidas.  | [Disponível](https://github.com/Azure/telemetry-agent-java) | [Disponível](https://github.com/Azure/telemetry-agent-dotnet)   |
| Configuração da IU         | Gere dados de configuração de IU. | [Disponível](https://github.com/azure/pcs-ui-config-java) | [Disponível](https://github.com/azure/pcs-ui-config-dotnet)   |
| Adaptador de armazenamento   |  Gere as interações com o serviço de armazenamento.   | [Disponível](https://github.com/azure/pcs-storage-adapter-java) | [Disponível](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Proxy inverso     | Expõe recursos privados de forma gerida através de um ponto de final exclusivo. | Ainda não está disponível | [Disponível](https://github.com/Azure/reverse-proxy-dotnet)   |

A solução de Java atualmente utiliza a autenticação .NET, simulação e micro-serviços de proxy inverso. Estes micro-serviços serão substituídos por versões de Java logo que ficarem disponíveis.

## <a name="presentation-and-visualization"></a>Apresentação e visualização

As secções seguintes descrevem as opções para personalizar a camada de apresentação e visualizações na solução de monitorização remota:

### <a name="change-the-logo-in-the-ui"></a>Alterar o logótipo na IU

A implementação predefinida utiliza o nome da empresa Contoso e o logótipo na IU. Para alterar estes elementos de IU para apresentar o nome da empresa e o logótipo:

1. Utilize o seguinte comando para clonar o repositório de IU da Web:

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. Para alterar o nome da empresa, abra o `src/common/lang.js` ficheiro num editor de texto.

1. Localize a seguinte linha no ficheiro:

    ```js
    CONTOSO: 'Contoso',
    ```

1. Substitua `Contoso` com o nome da sua empresa. Por exemplo:

    ```js
    CONTOSO: 'YourCo',
    ```

1. Guarde o ficheiro.

1. Para atualizar o logótipo, adicione um novo ficheiro SVG o `assets/icons` pasta. O logótipo existente é o `assets/icons/Contoso.svg` ficheiro.

1. Abra o `src/components/layout/leftNav/leftNav.js` ficheiro num editor de texto.

1. Localize a seguinte linha no ficheiro:

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Substitua `Contoso.svg` com o nome do seu ficheiro de logótipo. Por exemplo:

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. Localize a seguinte linha no ficheiro:

    ```js
    alt="ContosoIcon"
    ```

1. Substitua `ContosoIcon` com seu `alt` texto. Por exemplo:

    ```js
    alt="YourCoIcon"
    ```

1. Guarde o ficheiro.

1. Para testar as alterações, pode executar a atualização `webui` no seu computador local. Para saber como criar e executar o `webui` solução localmente, consulte [compilação, executar e teste localmente](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) no `webui` ficheiro de Leia-me de repositório do GitHub.

1. Para implementar as suas alterações, consulte o [guia de referência para programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>Personalizar o mapa

Consulte o [Personalizar mapa](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) página no GitHub para obter detalhes sobre os componentes de mapa na solução.

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a camada de apresentação e visualizações na solução de monitorização remota, pode editar o código. Repositórios do GitHub relevantes são:

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [WebUI de monitorização remota de computadores do Azure](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>Conectividade do dispositivo e de transmissão em fluxo

As secções seguintes descrevem as opções para personalizar a conectividade do dispositivo e a camada de transmissão em fluxo na solução de monitorização remota. [Modelos de dispositivos](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) descrevem os tipos de dispositivos e a telemetria na solução. Utilizar modelos de dispositivos para os dispositivos simulados e físicos.

Para obter um exemplo de uma implementação do dispositivo físico, consulte [ligar o seu dispositivo à solução pré-configurada de monitorização remota](iot-suite-connecting-devices-node.md).

Se estiver a utilizar um _dispositivo físico_, tem de disponibilizar a aplicação de cliente com um modelo de dispositivo que contém a especificação de telemetria e de metadados do dispositivo.

As secções seguintes abordam utilizando modelos de dispositivos com dispositivos simulados:

### <a name="add-a-telemetry-type"></a>Adicionar um tipo de telemetria

Os tipos de dispositivos na solução de demonstração de Contoso, especifique a telemetria que envia cada tipo de dispositivo. Para especificar os tipos de telemetria adicionais, um dispositivo pode enviar definições de telemetria como metadados para a solução. Se utilizar este formato, o dashboard consome a telemetria do dispositivo e os métodos disponíveis dinamicamente e não necessitar de modificar a IU. Em alternativa, pode modificar a definição de tipo de dispositivos na solução.

Para saber como adicionar telemetria personalizada no _simulador de dispositivo_ microsserviço, consulte [testar a sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

### <a name="add-a-device-type"></a>Adicionar um tipo de dispositivo

A solução de demonstração de Contoso define alguns tipos de dispositivos de exemplo. A solução permite-lhe definir os tipos de dispositivo personalizadas para satisfazer os seus requisitos de aplicação específica. Por exemplo, da sua empresa pode utilizar um gateway industriais como o dispositivo primário ligado à solução.

Para criar uma representação exata do seu dispositivo, terá de modificar a aplicação que é executado no seu dispositivo para fazer corresponder os requisitos de dispositivo.

Para saber como adicionar um novo tipo de dispositivo no _simulador de dispositivo_ microsserviço, consulte [testar a sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

### <a name="define-custom-methods-for-simulated-devices"></a>Definir métodos personalizados para dispositivos simulados

Para saber como definir métodos personalizados para dispositivos simulados na solução de monitorização remota, consulte [modelos de dispositivos](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) no repositório GitHub.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Utilizar um dispositivo físico

Para implementar métodos e as tarefas nos seus dispositivos físicos, consulte os artigos seguintes do IoT Hub:

* [Compreender e invocar métodos diretos do IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).
* [Agendar tarefas em vários dispositivos](../iot-hub/iot-hub-devguide-jobs.md).

### <a name="other-customization-options"></a>Outras opções de personalização

Para obter mais modificar a conectividade do dispositivo e a camada de transmissão em fluxo na solução de monitorização remota, pode editar o código. Repositórios do GitHub relevantes são:

* [Telemetria do dispositivo (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [Telemetria do dispositivo (Java)](https://github.com/Azure/device-telemetry-java)
* [Agente de telemetria (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Agente de telemetria (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Para modificar o processamento de dados e a camada de análise na solução de monitorização remota, pode editar o código. Repositórios do GitHub relevantes são:

* [Agente de telemetria (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Agente de telemetria (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>Infraestrutura

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Para modificar a infraestrutura na solução de monitorização remota, pode editar o código. Repositórios do GitHub relevantes são:

* [Gestor de IoTHub (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [Gestor de IoTHub (Java)](https://github.com/Azure/iothub-manager-java)
* [Adaptador de armazenamento (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [Adaptador de armazenamento (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a personalizar a solução pré-configurada.

Para obter mais informações concetuais sobre a solução pré-configurada de monitorização remota, consulte [arquitetura de monitorização remota](iot-suite-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre como personalizar a solução de monitorização remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->