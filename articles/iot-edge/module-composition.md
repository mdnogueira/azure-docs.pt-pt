---
title: "Composição do módulo de limite de IoT do Azure | Microsoft Docs"
description: "Saiba o que fica em módulos de limite de IoT do Azure e como pode ser reutilizadas"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6f9ca3d9b0f41210a3f43a8ae505f0a90b130b34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Compreender como módulos de IoT limite podem ser utilizados, configurado e reutilizada - pré-visualização

Limite de IoT do Azure permite-lhe compor vários módulos de limite de IoT antes de implementá-los para dispositivos de limite de IoT. Este artigo explica os conceitos importantes em torno de composição vários módulos de limite de IoT antes da implementação. 

## <a name="the-deployment-manifest"></a>O manifesto de implementação
O *o manifesto de implementação* é um documento JSON que descreve:

* Os módulos de IoT contorno tem de ser implementado, juntamente com a respetiva criação e as opções de gestão
* A configuração do hub Edge, que descrevem como mensagens devem fluxo entre módulos e entre módulos e IoT Hub;
* Opcionalmente, os valores para definir nas propriedades pretendidas duplos módulo, para configurar as aplicações de módulo individuais.

Os tutoriais de limite de IoT do Azure, criar um manifesto de implementação acedendo através de um assistente no portal do Azure IoT Edge. Também pode aplicar um manifesto de implementação utilizando através de programação REST ou o SDK do serviço do IoT Hub. Consulte [implementar e monitorizar] [ lnk-deploy] para obter mais informações sobre implementações de limite de IoT.

Um nível elevado, o manifesto de implementação configura as propriedades dos módulos de limite de IoT implementados num dispositivo IoT Edge pretendidas. Dois destes módulos são sempre presente: o agente de limite e o hub de limite.

O manifesto segue esta estrutura:

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

Um exemplo de um manifesto de implementação é reportado no final desta secção.

> [!IMPORTANT]
> Todos os dispositivos de IoT contorno tem de ser configurado com um manifesto de implementação. Os relatórios de um tempo de execução de limite de IoT recentemente instalado um código de erro até configurado com um manifesto válido. 

### <a name="specify-the-modules"></a>Especifique os módulos
Contém as propriedades pretendidas de duplo o módulo do agente Edge: os módulos pretendidos, as opções de configuração e gestão, juntamente com os parâmetros de configuração para o agente de limite.

Um nível elevado, esta secção do manifesto contém referências a imagens de módulo e as opções de gestão para os módulos de tempo de execução de limite de IoT (agente de limite e hub limite) e os módulos de utilizador especificado.

Consulte o [pretendido propriedades do agente Edge] [ lnk-edgeagent-desired] para a descrição detalhada nesta secção do manifesto.

> [!NOTE]
> Um manifesto de implementação que contém apenas o limite de IoT tempo de execução (agente e hub) é válido.


### <a name="specify-the-routes"></a>Especifique as rotas
Hub de limite fornece uma forma de forma declarativa encaminhar mensagens entre módulos e entre módulos e IoT Hub.

Rotas de ter a seguinte sintaxe:

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

O *origem* pode ser qualquer coisa dos seguintes procedimentos:

| Origem | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens do dispositivo para a nuvem de qualquer dispositivo ou o módulo |
| `/messages/*` | Qualquer mensagem de dispositivo para nuvem enviada por um dispositivo ou um módulo através de alguns ou nenhum resultado |
| `/messages/modules/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo através de alguns ou nenhum resultado |
| `/messages/modules/{moduleId}/*` | Qualquer mensagem de dispositivo para nuvem enviada por {moduleId} sem saída |
| `/messages/modules/{moduleId}/outputs/*` | Qualquer mensagem de dispositivo para nuvem enviada por {moduleId}, com algumas saídas de |
| `/messages/modules/{moduleId}/outputs/{output}` | Qualquer mensagem de dispositivo para nuvem enviada através de {moduleId} {saída} |

A condição pode ser qualquer condição suportada pelo [idioma de consulta do IoT Hub] [ lnk-iothub-query] para regras de encaminhamento do IoT Hub.

O sink pode ser um dos seguintes:

| sink | Descrição |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem ao IoT Hub |
| `BrokeredEndpoint(/modules/{moduleId}/inputs/{input})` | Enviar a mensagem de entrada `{input}` do módulo`{moduleId}` |

É importante ter em atenção que Edge hub fornece garantias em-menos-uma vez, que significa que as mensagens serão armazenadas localmente no caso de uma rota não é possível entregar a mensagem à respetiva dependente, por exemplo, o hub Edge não é possível ligar ao IoT Hub ou o módulo de destino não está ligado.

Hub de limite armazena as mensagens até à hora especificada no `storeAndForwardConfiguration.timeToLiveSecs` propriedade do Edge hub pretendido propriedades.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>Especificar as propriedades pretendidas do duplo de módulo

O manifesto de implementação pode especificar as propriedades pretendidas de duplo o módulo de cada um dos módulos de utilizador especificados na secção agente de limite.

Quando as propriedades pretendidas são especificadas no manifesto de implementação, podem substituem quaisquer propriedades pretendidas atualmente no duplo módulo.

Se não especificar propriedades de pretendido de um duplo de módulo no manifesto de implementação, o IoT Hub não irá modificar o duplo de módulo de qualquer forma e conseguirá definir as propriedades de pretendido através de programação.

### <a name="deployment-manifest-example"></a>Exemplo de manifesto de implementação

Este exemplo de um documento JSON manifesto de implementação.

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>Referência: Duplo de módulo de agente de limite

O duplo de módulo para o agente de limite é chamado `$edgeAgent` e coordena as comunicações entre o agente de limite, em execução num dispositivo e do IoT Hub.
As propriedades pretendidas são definidas ao aplicar um manifesto de implementação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala. Consulte [implementação e monitorização] [ lnk-deploy] para obter mais informações sobre como implementar módulos em dispositivos de limite de IoT.

### <a name="edge-agent-twin-desired-properties"></a>Propriedades de duplo pretendida do agente de limite

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| Runtime.Type | Tem de ser "docker" | Sim |
| runtime.settings.minDockerVersion | Definido para a versão mínima do Docker necessária para o manifesto de implementação | Sim |
| runtime.settings.loggingOptions | Um stringified JSON que contém as opções de registo para o contentor de agente de limite. [Opções de registo do docker][lnk-docker-logging-options] | Não |
| systemModules.edgeAgent.type | Tem de ser "docker" | Sim |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente do limite. Atualmente, o agente de limite não é possível se Atualize automaticamente. | Sim |
| systemModules.edgeAgent.settings.createOptions | Um stringified JSON que contém as opções para a criação do contentor de agente Edge. [Docker criar opções][lnk-docker-create-options] | Não |
| systemModules.edgeAgent.configuration.id | O ID da implementação que implementado Este módulo. | Isto é definido pelo IoT Hub quando este manifesto é aplicado através de uma implementação. Não faz parte de um manifesto de implementação. |
| systemModules.edgeHub.type | Tem de ser "docker" | Sim |
| systemModules.edgeHub.status | Tem de ser "em execução" | Sim |
| systemModules.edgeHub.restartPolicy | Tem de ser "sempre" | Sim |
| systemModules.edgeHub.settings.image | O URI da imagem do Edge hub. | Sim |
| systemModules.edgeHub.settings.createOptions | Um stringified JSON que contém as opções para a criação do contentor de hub de limite. [Docker criar opções][lnk-docker-create-options] | Não |
| systemModules.edgeHub.configuration.id | O ID da implementação que implementado Este módulo. | Isto é definido pelo IoT Hub quando este manifesto é aplicado através de uma implementação. Não faz parte de um manifesto de implementação. |
| módulos. .version {moduleId} | Uma cadeia definida pelo utilizador, que representa a versão deste módulo. | Sim |
| módulos. .type {moduleId} | Tem de ser "docker" | Sim |
| módulos. .restartPolicy {moduleId} | {"nunca" \| "no-falhou" \| "no-mau estado de funcionamento" \| "sempre"} | Sim |
| módulos. .settings.image {moduleId} | O URI para a imagem do módulo. | Sim |
| módulos. .settings.createOptions {moduleId} | Um stringified JSON que contém as opções para a criação do contentor do módulo. [Docker criar opções][lnk-docker-create-options] | Não |
| módulos. .configuration.id {moduleId} | O ID da implementação que implementado Este módulo. | Isto é definido pelo IoT Hub quando este manifesto é aplicado através de uma implementação. Não faz parte de um manifesto de implementação. |

### <a name="edge-agent-twin-reported-properties"></a>Duplo de agente Edge comunicadas propriedades

O agente de limite reportados propriedades incluem as três principais informações:

1. O estado da aplicação das propriedades pretendidas vistos por último;
2. O estado dos módulos atualmente em execução no dispositivo, conforme comunicado pelo agente de limite; e
3. Uma cópia das propriedades pretendidas atualmente em execução no dispositivo.

Neste último elemento de informação é útil caso as propriedades pretendidas mais recente não são aplicadas com êxito pelo runtime e o dispositivo ainda está em execução um manifesto de implementação anterior.

> [!NOTE]
> As propriedades do agente Edge comunicadas são úteis como podem ser consultadas com o [idioma de consulta do IoT Hub] [ lnk-iothub-query] para investigar o estado de implementações de escala. Consulte [implementações] [ lnk-deploy] para obter mais informações sobre como utilizar esta funcionalidade.

A tabela seguinte não inclui as informações que são copiadas a partir das propriedades pretendidas.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este int refere-se para a última versão das propriedades pretendidas processados pelo agente de limite. |
| lastDesiredStatus.code | Este é o código de estado que faça referência a última propriedades pretendidas vistas pelo agente de limite. Valores permitidos: `200` com êxito, `400` configuração inválida, `412` versão de esquema inválida, `417` as propriedades pretendidas estão vazios, `500` falhou |
| lastDesiredStatus.description | Descrição de texto do Estado |
| DeviceHealth | `healthy`Se o estado do tempo de execução de todos os módulos `running` ou `stopped`, `unhealthy` caso contrário |
| configurationHealth. .health {deploymentId} | `healthy`Se o estado do tempo de execução de todos os módulos definido pela implementação {deploymentId} `running` ou `stopped`, `unhealthy` caso contrário |
| runtime.platform.OS | Relatórios de SO em execução no dispositivo |
| Runtime.Platform.Architecture | A arquitetura da CPU de relatórios no dispositivo |
| systemModules.edgeAgent.runtimeStatus | O estado do agente de limite: {"em execução" \| "mau estado de funcionamento"} |
| systemModules.edgeAgent.statusDescription | Descrição de texto do Estado do agente Edge comunicado. |
| systemModules.edgeHub.runtimeStatus | Estado atual do hub de limite: {"em execução" \| "parado" \| "Falha" \| "término" \| "mau estado de funcionamento"} |
| systemModules.edgeHub.statusDescription | Descrição de texto do estado atual do hub de limite se mau estado de funcionamento. |
| systemModules.edgeHub.exitCode | Se terminado, o código de saída comunicado pelo contentor de hub de limite |
| systemModules.edgeHub.startTimeUtc | Tempo quando Edge hub foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Quando terminado pela última vez hub de limite de tempo |
| systemModules.edgeHub.lastRestartTimeUtc | Tempo quando Edge hub foi reiniciado pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |
| módulos. .runtimeStatus {moduleId} | Estado atual do módulo: {"em execução" \| "parado" \| "Falha" \| "término" \| "mau estado de funcionamento"} |
| módulos. .statusDescription {moduleId} | Descrição de texto do estado atual do módulo se mau estado de funcionamento. |
| módulos. .exitCode {moduleId} | Se terminado, o código de saída comunicado pelo contentor de módulo |
| módulos. .startTimeUtc {moduleId} | Tempo quando o módulo foi iniciado pela última vez |
| módulos. .lastExitTimeUtc {moduleId} | Tempo quando o módulo terminado pela última vez |
| módulos. .lastRestartTimeUtc {moduleId} | Tempo quando o módulo foi reiniciado pela última vez |
| módulos. .restartCount {moduleId} | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |

## <a name="reference-edge-hub-module-twin"></a>Referência: Duplo de módulo de hub de limite

O duplo de módulo para o hub de limite é chamado `$edgeHub` e coordena as comunicações entre o hub de limite que executem um dispositivo e o IoT Hub.
As propriedades pretendidas são definidas ao aplicar um manifesto de implementação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala. Consulte [implementações] [ lnk-deploy] para obter mais informações sobre como implementar módulos em dispositivos de limite de IoT.

### <a name="edge-hub-twin-desired-properties"></a>Propriedades do limite hub duplo pretendida

| Propriedade | Descrição | Necessário no manifesto de implementação |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| rotas. {routeName} | Uma cadeia que representa uma rota de hub de limite. | O `routes` elemento pode estar presente, mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos, que mantém o hub de limite mensagens no caso de pontos finais de encaminhamento desligados, por exemplo, desligado do IoT Hub ou módulo local | Sim |

### <a name="edge-hub-twin-reported-properties"></a>Duplo de hub de limite comunicadas propriedades

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este int refere-se para a última versão das propriedades pretendidas processados através do hub de limite. |
| lastDesiredStatus.code | Este é o código de estado que faça referência a última propriedades pretendidas vistas pelo Edge hub. Valores permitidos: `200` com êxito, `400` configuração inválida, `500` falhou |
| lastDesiredStatus.description | Descrição de texto do Estado |
| clientes. .status {identidade de dispositivo ou módulo} | O estado de conectividade deste dispositivo ou o módulo. Os valores possíveis {"ligada" \| "desligada"}. Apenas as identidades do módulo podem estar no estado desligado. Dispositivos a jusante estabelecer ligação ao Edge hub aparecem apenas quando estiver ligado. |
| clientes. .lastConnectTime {identidade de dispositivo ou módulo} | Última hora no dispositivo ou módulo ligado |
| clientes. .lastDisconnectTime {identidade de dispositivo ou módulo} | Última vez que o dispositivo ou módulo desligado |

## <a name="next-steps"></a>Passos seguintes

Agora que já sabe como são utilizados os módulos de IoT Edge, [compreender os requisitos e ferramentas para o desenvolvimento de módulos de limite de IoT][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
