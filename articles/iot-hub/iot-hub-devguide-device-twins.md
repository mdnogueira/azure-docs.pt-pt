---
title: Compreender os dispositivos duplos do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - utilize dispositivos duplos para sincronizar dados de estado e a configuração entre o IoT Hub e dos dispositivos"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afadedf72562452e4d57d4545efe59cd8d37c907
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Compreender e utilizar dispositivos duplos no IoT Hub

*Dispositivos duplos* são documentos JSON que armazenam informações de estado do dispositivo, incluindo metadados, configurações e condições. IoT Hub do Azure mantém um dispositivo duplo para cada dispositivo que se liga ao IoT Hub. Este artigo descreve:

* A estrutura do dispositivo duplo: *etiquetas*, *pretendido* e *comunicadas propriedades*.
* As operações que podem efetuar a aplicações de dispositivos e o back-ends nos dispositivos duplos.

Utilize dispositivos duplos para:

* Armazenar metadados específicos do dispositivo na nuvem. Por exemplo, a localização de implementação de uma máquina de distribuidores automáticos.
* Relatório atuais informações de estado como capacidades disponíveis e condições da sua aplicação de dispositivo. Por exemplo, um dispositivo está ligado ao seu IoT hub por rede móvel ou Wi-Fi.
* Sincronize o estado de execução longa fluxos de trabalho entre a aplicação de dispositivo e aplicação de back-end. Por exemplo, quando criar uma solução de fim Especifica a nova versão de firmware para instalar e a aplicação de dispositivo relatórios de várias fases do processo de atualização.
* Consulta os metadados do dispositivo, a configuração ou o estado.

Consulte [orientações de comunicação do dispositivo para nuvem] [ lnk-d2c-guidance] para obter orientações sobre como utilizar propriedades comunicadas, mensagens do dispositivo para nuvem ou carregamento do ficheiro.
Consulte [orientações de comunicação de nuvem para o dispositivo] [ lnk-c2d-guidance] para obter orientações sobre como utilizar propriedades pretendidas, métodos diretos ou mensagens da nuvem para o dispositivo.

## <a name="device-twins"></a>Dispositivos duplos
Dispositivos duplos armazenam informações relacionadas com o dispositivo que:

* As extremidades de dispositivo e back podem utilizar para sincronizar as condições do dispositivo e a configuração.
* O solução de back-end pode utilizar para consulta e de destino demoradas operações.

O ciclo de vida de um dispositivo duplo ligado correspondente [identidade de dispositivo][lnk-identity]. Dispositivos duplos implicitamente são criados e eliminados quando uma identidade de dispositivo é criada ou eliminada no IoT Hub.

Um dispositivo duplo é um documento JSON que inclui:

* **Etiquetas**. Uma secção do documento JSON que a solução de back-end pode leem e escrevem para. As etiquetas não são visíveis para aplicações de dispositivos.
* **Pretender propriedades**. Utilizado juntamente com propriedades comunicadas para sincronizar condições ou de configuração do dispositivo. O solução de back-end pode definir propriedades pretendidas e, a aplicação de dispositivo pode lê-los. A aplicação de dispositivo também pode receber notificações de alterações nas propriedades de pretendido.
* **Comunicado propriedades**. Utilizado juntamente com propriedades pretendidas para sincronizar condições ou de configuração do dispositivo. A aplicação de dispositivo, pode definir propriedades comunicadas e o solução de back-end pode ler e consultá-los.

Além disso, a raiz do documento dispositivo duplo JSON contém as propriedades só de leitura de identidade de dispositivo correspondente armazenado no [registo de identidade][lnk-identity].

![][img-twin]

O exemplo seguinte mostra um dispositivo duplo documento JSON:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

O objeto de raiz, são as propriedades do sistema e contentor de objetos para `tags` e ambos `reported` e `desired` propriedades. O `properties` contentor contém alguns elementos de só de leitura (`$metadata`, `$etag`, e `$version`) descrito a [metadados do dispositivo duplo] [ lnk-twin-metadata] e [ Simultaneidade otimista] [ lnk-concurrency] secções.

### <a name="reported-property-example"></a>Exemplo de propriedade comunicado
No exemplo anterior, o dispositivo duplo contém um `batteryLevel` propriedade que é comunicada pela aplicação do dispositivo. Esta propriedade torna possível consultar e operar em dispositivos com base no nível de bateria comunicado último. Outros exemplos incluem as capacidades de dispositivos de relatórios de aplicações do dispositivo ou as opções de conectividade.

> [!NOTE]
> Propriedades comunicadas simplificam cenários em que a solução de back-end é interessado no último valor conhecido de uma propriedade. Utilize [mensagens do dispositivo para nuvem] [ lnk-d2c] se a solução de back-end necessita de processar a telemetria do dispositivo sob a forma de sequências de eventos de timestamped, tais como a série de tempo.

### <a name="desired-property-example"></a>Exemplo de propriedade pretendida
No exemplo anterior, o `telemetryConfig` dispositivo duplo pretendida e propriedades comunicadas são utilizadas pela solução de back-end e a aplicação de dispositivo para sincronizar a configuração de telemetria para este dispositivo. Por exemplo:

1. O solução de back-end define a propriedade com o valor de configuração pretendida pretendida. Segue-se a parte do documento com a propriedade pretendido definida:
   
        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
2. A aplicação de dispositivo é notificada da alteração imediatamente se ligado ou, no primeiro o restabelecimento de ligação. A aplicação de dispositivo, em seguida, reporta a configuração atualizada (ou uma condição de erro utilizando o `status` propriedade). Segue-se a parte das propriedades comunicadas:
   
        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...
3. O solução de back-end pode controlar os resultados da operação de configuração em vários dispositivos, por [consultar] [ lnk-query] dispositivos duplos.

> [!NOTE]
> Os fragmentos anteriores são exemplos, otimizados para legibilidade, de uma forma de codificar uma configuração de dispositivo e o respetivo estado. IoT Hub não impõe um esquema específico para o dispositivo duplo pretendida e reportado propriedades nos dispositivos duplos.
> 
> 

Pode utilizar duplos para sincronizar as operações de longa execução, tais como atualizações de firmware. Para obter mais informações sobre como utilizar propriedades para sincronizar e controlar uma operação demorada que em todos os dispositivos, consulte [utilização pretendida propriedades para configurar dispositivos][lnk-twin-properties].

## <a name="back-end-operations"></a>Operações de back-end
O solução de back-end funciona no dispositivo duplo utilizando as seguintes operações atómicas, expostas através de HTTPS:

* **Obter o dispositivo duplo por id**. Esta operação devolve o documento de duplo de dispositivo, incluindo etiquetas e propriedades do sistema pretendido e comunicados.
* **Atualizar o dispositivo duplo parcialmente**. Esta operação permite que a solução de back-end parcialmente atualizar as etiquetas ou propriedades pretendidas num dispositivo duplo. A atualização parcial é expresso como um documento JSON que adiciona ou atualiza uma propriedade. As propriedades definidas como `null` são removidos. O exemplo seguinte cria uma nova propriedade pretendida com valor `{"newProperty": "newValue"}`, substitui o valor existente da `existingProperty` com `"otherNewValue"`e remove `otherOldProperty`. Não existem outras alterações são efetuadas às propriedades pretendidas existentes ou tags:
   
        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }
* **Substituir propriedades pretendidas**. Esta operação permite que a solução de back-end para completamente substituir propriedades pretendidas todas as existentes e substituir um novo documento JSON para `properties/desired`.
* **Substitua as etiquetas**. Esta operação permite que a solução de back-end para completamente substituir a existentes todas as etiquetas e a substituir um novo documento JSON para `tags`.
* **Receber notificações de duplo**. Esta operação permite que a solução de back-end ser notificado quando o duplo é modificado. Para tal, a solução de IoT tem de criar uma rota e definir a origem de dados igual a *twinChangeEvents*. Por predefinição, não existem notificações duplo são enviadas, ou seja, sem estas rotas existem previamente. Se a taxa de alteração é demasiado elevada, ou por outros motivos como falhas internos, o IoT Hub poderá enviar apenas uma notificação que contém todas as alterações. Por isso, se a sua aplicação tiver fiável de auditoria e registo de todos os Estados intermédios, em seguida, é ainda recomendado que utilize D2C mensagens. A mensagem de notificação de duplo inclui as propriedades e corpo.

    - Propriedades

    | Nome | Valor |
    | --- | --- |
    $content-tipo | application/json |
    $iothub-enqueuedtime |  Hora em que a notificação foi enviada |
    $iothub-mensagem-origem | twinChangeEvents |
    $content-codificação | UTF-8 |
    deviceId | ID do dispositivo |
    hubName | Nome do IoT Hub |
    operationTimestamp | [ISO8601] timestamp da operação |
    esquema de mensagem iothub | deviceLifecycleNotification |
    opType | "replaceTwin" ou "updateTwin" |

    Propriedades do sistema de mensagens têm o prefixo de `'$'` símbolo.

    - Corpo
        
    Esta secção inclui todas as alterações de duplo num formato JSON. Utiliza o mesmo formato como uma correção, com a diferença que pode conter todas as secções de duplo: etiquetas, properties.reported, properties.desired e que contém os elementos de "$metadata". Por exemplo,
    ```
    {
        "properties": {
            "desired": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            },
            "reported": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            }
        }
    }
    ``` 

Todas as operações anteriores suportam [simultaneidade otimista] [ lnk-concurrency] e requerem o **ServiceConnect** permissão, tal como definido no [segurança] [ lnk-security] artigo.
 
Além destas operações, o solução de back-end pode:

* Consultar os dispositivos duplos com o tipo SQL, [idioma de consulta do IoT Hub][lnk-query].
* Efetuar operações em grandes conjuntos de dispositivos duplos com [tarefas][lnk-jobs].

## <a name="device-operations"></a>Operações de dispositivo
A aplicação de dispositivo funciona no dispositivo duplo utilizando as seguintes operações atómicas:

* **Obter o dispositivo duplo**. Esta operação devolve o documento de duplo de dispositivo (incluindo etiquetas e propriedades do sistema pretendido e comunicados) para o dispositivo atualmente ligado.
* **Parcialmente atualizar propriedades comunicadas**. Esta operação permite que a atualização parcial das propriedades comunicadas do dispositivo atualmente ligado. Esta operação utiliza o mesmo formato de atualização JSON que a solução de fazer uma cópia de utilizações de fim de uma atualização parcial das propriedades pretendidas.
* **Observar propriedades pretendidas**. O dispositivo atualmente ligado pode optar por ser notificado das atualizações para as propriedades pretendidas quando que ocorrem. O dispositivo recebe o mesmo formato de atualização (substituição parcial ou completa) executado pela solução de back-end.

Todas as operações anteriores requerem o **DeviceConnect** permissão, tal como definido no [segurança] [ lnk-security] artigo.

O [SDKs do Azure IoT device] [ lnk-sdks] torna mais fácil de utilizar as operações anteriores a partir de vários idiomas e plataformas. Para obter mais informações sobre os detalhes de primitivos do IoT Hub para a sincronização de propriedades pretendido, consulte [fluxo de restabelecimento de ligação do dispositivo][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Formato de etiquetas e propriedades
As etiquetas, propriedades pretendidas e propriedades comunicadas são objetos JSON com as seguintes restrições:

* Todas as chaves em objetos JSON são sensíveis a maiúsculas 64 bytes cadeias UNICODE UTF-8. Permitido carateres excluir carateres de controlo UNICODE (segmentos C0 e C1), e `'.'`, `' '`, e `'$'`.
* Todos os valores em objetos JSON podem ser dos seguintes tipos de JSON: boolean, número, cadeia, objecto. Não são permitidas matrizes. O valor máximo de números inteiros é 4503599627370495 e o valor mínimo de números inteiros é-4503599627370496.
* Todos os objetos JSON etiquetas, propriedades pretendidas e reportadas podem ter uma profundidade máxima de 5. Por exemplo, do seguinte objeto é válido:

        {
            ...
            "tags": {
                "one": {
                    "two": {
                        "three": {
                            "four": {
                                "five": {
                                    "property": "value"
                                }
                            }
                        }
                    }
                }
            },
            ...
        }

* Todos os valores de cadeia podem ser, no máximo 4 KB de comprimento.

## <a name="device-twin-size"></a>Tamanho do dispositivo duplo
IoT Hub impõe uma limitação de tamanho de 8KB nos valores totais das `tags`, `properties/desired`, e `properties/reported`, excluindo os elementos de só de leitura.
O tamanho é calculado através da contagem de todos os carateres, excluindo carateres de controlo UNICODE (segmentos C0 e C1) e espaços que estejam fora as constantes string.
IoT Hub com um erro rejeita todas as operações que podem aumentar o tamanho desses documentos supera o limite.

## <a name="device-twin-metadata"></a>Metadados do dispositivo duplo
IoT Hub mantém o carimbo da última atualização para cada objeto JSON no dispositivo duplo pretendida e reportado propriedades. Os carimbos está em UTC e codificadas no [ISO8601] formato `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por exemplo:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Esta informação é mantida em cada nível (não apenas os leaves da estrutura de JSON) para manter as atualizações que remover chaves de objeto.

## <a name="optimistic-concurrency"></a>Simultaneidade otimista
Etiquetas, assim o desejar e comunicadas propriedades todos os simultaneidade otimista de suporte.
Etiquetas tem uma ETag, como por [RFC7232], que representa a representação JSON da etiqueta. Pode utilizar ETags em operações de actualização condicional da solução de back-end para garantir consistência.

Dispositivo duplo pretendida e reportado a propriedades não dispõe de ETags, mas tem um `$version` valor que fique incremental. Da mesma forma para uma ETag, a versão pode ser utilizada por terceiros a atualização para impor a consistência das atualizações. Por exemplo, uma aplicação de dispositivo para uma propriedade que relatados ou a solução de back-end para uma propriedade pretendida.

Versões também são úteis quando um agente observing (por exemplo, a aplicação de dispositivo observar as propriedades pretendidas) tem reconciliar races entre o resultado de uma operação de obtenção e uma notificação de atualização. A secção [fluxo de restabelecimento de ligação do dispositivo] [ lnk-reconnection] fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de restabelecimento de ligação do dispositivo
IoT Hub não preserva a notificações de atualização de propriedades pretendido para dispositivos desligados. Segue-se um dispositivo que está a ligar tem de obter o documento de propriedades pretendido completa, para além de subscrever notificações de atualização de. Dada a possibilidade de races entre as notificações de atualizações e a obtenção completa, o fluxo seguinte tem de ser a certificar-se:

1. Aplicação de dispositivo estabelece ligação a um IoT hub.
2. Aplicação de dispositivo subscreve para propriedades pretendidas notificações de atualização.
3. Aplicação de dispositivo obtém o documento completo de propriedades pretendidos.

A aplicação de dispositivo, pode ignorar todas as notificações com `$version` inferior ou igual à versão do documento obtido completo. Esta abordagem é possível porque o IoT Hub garante que sempre incrementar versões.

> [!NOTE]
> Esta lógica já está implementada a [SDKs do Azure IoT device][lnk-sdks]. Esta descrição é útil apenas se a aplicação de dispositivo não é possível utilizar qualquer um dos SDKs do dispositivo IoT do Azure e tem de programa diretamente a interface MQTT.
> 
> 

## <a name="additional-reference-material"></a>Material de referência adicionais
Outros tópicos de referência no guia de programadores do IoT Hub incluem:

* O [pontos finais de IoT Hub] [ lnk-endpoints] artigo descreve os vários pontos finais que cada IoT hub expõe para operações de gestão e de tempo de execução.
* O [limitação e quotas] [ lnk-quotas] artigo descreve as quotas que se aplicam para o serviço de IoT Hub e o comportamento de limitação pode esperar quando utilizar o serviço.
* O [SDKs do dispositivo e o serviço do Azure IoT] [ lnk-sdks] artigo apresenta uma lista de idioma vários SDKs que pode utilizar ao desenvolver aplicações de serviço e dispositivo que interagem com o IoT Hub.
* O [idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens] [ lnk-query] artigo descreve o idioma de consulta do IoT Hub pode utilizar para obter informações a partir do IoT Hub sobre os dispositivos duplos e tarefas.
* O [suporte do IoT Hub MQTT] [ lnk-devguide-mqtt] artigo fornece mais informações sobre o suporte de IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes
Agora que tem aprendeu sobre dispositivos duplos, poderá estar interessado nos seguintes tópicos do guia de programadores do IoT Hub:

* [Invocar um método direto num dispositivo][lnk-methods]
* [Agenda de tarefas em vários dispositivos][lnk-jobs]

Se pretender experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado nos seguintes tutoriais do IoT Hub:

* [Como utilizar o dispositivo duplo][lnk-twin-tutorial]
* [Como utilizar as propriedades do dispositivo duplo][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
