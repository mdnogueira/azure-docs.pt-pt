---
title: "Simulação de dispositivos na solução de monitorização remota - Azure | Microsoft Docs"
description: "Este tutorial mostra como utilizar o simulador de dispositivo com a solução pré-configurada de monitorização remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 732ec45003481b0e2f2eca03b6ae13772d325ef1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>Testar a sua solução com dispositivos simulados

Este tutorial mostra como utilizar a personalizar o microsserviço do simulador de dispositivo com a solução pré-configurada de monitorização remota. Para mostrar as capacidades do simulador de dispositivo, que este tutorial utiliza dois cenários na aplicação Contoso IoT.

O primeiro cenário, a Contoso pretende testar um novo dispositivo lightbulb inteligente. Para executar os testes, crie um novo dispositivo simulado com as seguintes características:

*Propriedades*

| Nome                     | Valores                      |
| ------------------------ | --------------------------- |
| Cor                    | Em branco, vermelho, azul            |
| Brightness               | 0 e 100                    |
| Estimado vida restantes | Contagem decrescente de 10 000 horas |

*Telemetria*

A tabela seguinte mostra os dados que a ligthbulb relatórios para a nuvem como um fluxo de dados:

| Nome   | Valores      |
| ------ | ----------- |
| Estado | 'em", 'off' |

*Métodos*

A tabela seguinte mostra as ações que o novo dispositivo suporta:

| Nome        |
| ----------- |
| Comutador no   |
| Desactivar  |

*Estado inicial*

A tabela seguinte mostra o estado inicial do dispositivo:

| Nome                     | Valores |
| ------------------------ | -------|
| Cor inicial            | Branco  |
| Brightness inicial       | 75     |
| Vida restantes inicial   | 10,000 |
| Estado de telemetria inicial | "ativado"   |

O segundo cenário, adicione um novo tipo de telemetria para Contoso do existente **Chiller** dispositivo.

Este tutorial mostra como utilizar o simulador de dispositivo com a solução pré-configurada de monitorização remota:

Neste tutorial, ficará a saber como:

>[!div class="checklist"]
> * Criar um novo tipo de dispositivo
> * Simular o comportamento de dispositivo personalizada
> * Adicionar um novo tipo de dispositivo para o dashboard
> * Enviar telemetria personalizada a partir de um tipo de dispositivo existente

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada da solução de monitorização remota na sua subscrição do Azure.

Se ainda não implementado a solução de monitorização remota ainda, deve efetuar o [implementar a solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-deploy.md) tutorial.

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>O serviço de simulação de dispositivo

O serviço de simulação de dispositivos na solução pré-configurada permite-lhe efetuar alterações para os tipos de dispositivo simulado incorporadas e criar novos tipos de dispositivo simulado. Pode utilizar os tipos de dispositivo personalizadas para testar o comportamento da solução de monitorização remota antes de ligar os dispositivos físicos a solução.

## <a name="create-a-simulated-device-type"></a>Criar um tipo de dispositivo simulada

A forma mais fácil de criar um novo tipo de dispositivo no microsserviço a simulação é para copiar e modificar um tipo existente. Os passos seguintes mostram como copiar incorporada **Chiller** dispositivo para criar uma nova **Lightbulb** dispositivo:

1. Utilize o seguinte comando para clonar o **simulação de dispositivo** repositório do GitHub para o computador local:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Cada tipo de dispositivo tem um ficheiro de modelo JSON e scripts associados no `Services/data/devicemodels` pasta. Copiar o **Chiller** ficheiros para criar o **Lightbulb** ficheiros conforme mostrado na seguinte tabela:

    | Origem                      | Destino                   |
    | --------------------------- | ----------------------------- |
    | chiller 01.json             | lightbulb 01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | reiniciar o computador/scripts-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definir as características do novo tipo de dispositivo

O `lightbulb-01.json` ficheiro define as características do tipo, tais como a telemetria gera e os métodos que suporta. Os passos seguintes atualização o `lightbulb-01.json` ficheiro para definir o **Lightbulb** dispositivo:

1. No `lightbulb-01.json` de ficheiros, atualize os metadados do dispositivo, conforme mostrado no seguinte fragmento:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. No `lightbulb-01.json` ficheiro, a definição de simulação de atualização, conforme mostrado no seguinte fragmento:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. No `lightbulb-01.json` de ficheiros, Atualize as propriedades do tipo de dispositivo, conforme mostrado no seguinte fragmento:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. No `lightbulb-01.json` de ficheiros, Atualize as definições de telemetria do tipo de dispositivo, conforme mostrado no seguinte fragmento:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. No `lightbulb-01.json` de ficheiros, atualize os métodos de tipo de dispositivo, conforme mostrado no seguinte fragmento:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. Guardar o `lightbulb-01.json` ficheiro.

### <a name="simulate-custom-device-behavior"></a>Simular o comportamento de dispositivo personalizada

O `scripts/lightbulb-01-state.js` ficheiro define o comportamento de simulação do **Lightbulb** tipo. Os passos seguintes atualização o `scripts/lightbulb-01-state.js` ficheiro para definir o comportamento do **Lightbulb** dispositivo:

1. Edite a definição de estado no `scripts/lightbulb-01-state.js` conforme mostrado no seguinte fragmento de ficheiros:

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Substitua o **variar** função com o seguinte **inverte** função:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Editar o **principal** função para implementar o comportamento, conforme mostrado no seguinte fragmento:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Guardar o `scripts/lightbulb-01-state.js` ficheiro.

O `scripts/SwitchOn-method.js` ficheiro implementa o **no comutador** método um **Lightbulb** dispositivo. Os passos seguintes atualização o `scripts/SwitchOn-method.js` ficheiro:

1. Edite a definição de estado no `scripts/SwitchOn-method.js` conforme mostrado no seguinte fragmento de ficheiros:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Para mudar o lightbulb, edite o **principal** funciona da seguinte forma:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Guardar o `scripts/SwitchOn-method.js` ficheiro.

1. Faça uma cópia do `scripts/SwitchOn-method.js` ficheiro chamado `scripts/SwitchOff-method.js`.

1. Para desactivar a lightbulb, edite o **principal** funcionar o `scripts/SwitchOff-method.js` ficheiro da seguinte forma:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Guardar o `scripts/SwitchOff-method.js` ficheiro.

### <a name="test-the-lightbulb-device-type"></a>O tipo de dispositivo Lightbulb de teste

Para testar o **Lightbulb** tipo de dispositivo, primeiro pode testar o tipo de dispositivo funciona conforme esperado ao executar uma cópia local do **simulação de dispositivo** serviço. Se tiver testado e debugged localmente o novo tipo de dispositivo, pode recriar o contentor e voltar a implementar o **simulação de dispositivo** serviço para o Azure.

Para testar e depurar as suas alterações localmente, consulte [descrição geral de simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md).

Configurar o projeto para copiar o novo **Lightbulb** ficheiros de dispositivos para o diretório de saída.

Para testar o novo dispositivo numa solução implementada, consulte um dos:

* [Implementar contentores de conta personalizada docker-hub](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [Atualizar um contentor implementado através de cópia manual](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

No **dispositivos** página, pode aprovisionar as instâncias do seu novo tipo:

![Ver a lista de simulações disponíveis](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Pode ver a telemetria do dispositivo simulado:

![Vista lightbulb telemetria](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Pode chamar o **SwitchOn** e **SwitchOff** métodos no seu dispositivo:

![Chamar os métodos de lightbulb](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Para criar uma imagem de Docker com o novo tipo de dispositivo para a implementação para o Azure, consulte [criação de uma imagem personalizada do Docker](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="add-a-new-telemetry-type"></a>Adicionar um novo tipo de telemetria

Esta secção descreve como modificar um tipo de dispositivo simulado existente para suportar um novo tipo de telemetria.

### <a name="locate-the-chiller-device-type-files"></a>Localizar os ficheiros de tipo de dispositivo Chiller

Os passos seguintes mostram como encontrar os ficheiros que definem incorporada **Chiller** dispositivo:

1. Se ainda não o tiver feito, utilize o seguinte comando para clonar o **simulação de dispositivo** repositório do GitHub para o computador local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Cada tipo de dispositivo tem um ficheiro de modelo JSON e scripts associados no `data/devicemodels` pasta. Os ficheiros que definem a simulada **Chiller** são do tipo de dispositivo:
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Especifique o novo tipo de telemetria

Os passos seguintes mostram como adicionar um novo **temperatura interno** escreva para a **Chiller** tipo de dispositivo:

1. Abra o ficheiro `chiller-01.json`.

1. Atualização do **SchemaVersion** valor da seguinte forma:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. No **InitialState** secção, adicione as follwing duas definições:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. No **telemetria** matriz, adicione a seguinte definição:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Guardar o `chiller-01.json` ficheiro.

1. Abra o ficheiro `scripts/chiller-01-state.js`.

1. Adicione os seguintes campos para o **estado** variável:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Adicione a seguinte linha para o **principal** função:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Guardar o `scripts/chiller-01-state.js` ficheiro.

### <a name="test-the-chiller-device-type"></a>O tipo de dispositivo Chiller de teste

Para testar a atualização **Chiller** tipo de dispositivo, primeiro pode testar o tipo de dispositivo funciona conforme esperado ao executar uma cópia local do **simulação de dispositivo** serviço. Se tiver testado e debugged localmente o seu tipo de dispositivo atualizado, pode recriar o contentor e voltar a implementar o **simulação de dispositivo** serviço para o Azure.

Quando executa o **simulação de dispositivo** serviço localmente, envia telemetria à sua solução de monitorização remota. No **dispositivos** página, pode aprovisionar instâncias do seu tipo atualizado.

Para testar e depurar as suas alterações localmente, consulte [que executa o serviço com o Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) ou [compilar e executar a linha de comandos](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line).

Para testar o novo dispositivo numa solução implementada, consulte um dos:

* [Implementar contentores de conta personalizada docker-hub](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [Atualizar um contentor implementado através de cópia manual](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

No **dispositivos** página, pode aprovisionar instâncias do seu tipo atualizada:

![Adicionar chiller atualizado](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Pode ver o novo **temperatura interna** telemetria do dispositivo simulado.

Para criar uma imagem de Docker com o novo tipo de dispositivo para a implementação para o Azure, consulte [criação de uma imagem personalizada do Docker](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou, como para:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Criar um novo tipo de dispositivo
> * Simular o comportamento de dispositivo personalizada
> * Adicionar um novo tipo de dispositivo para o dashboard
> * Enviar telemetria personalizada a partir de um tipo de dispositivo existente

Agora que aprendeu a utilizar o serviço de simulação de dispositivo, o passo seguinte sugerido é saber como [ligar um dispositivo físico a sua solução de monitorização remota](iot-suite-connecting-devices-node.md).

Para obter mais informações de programador sobre a solução de monitorização remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->