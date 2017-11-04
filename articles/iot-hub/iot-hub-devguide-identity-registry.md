---
title: Compreender o registo de identidade do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - descrição do registo de identidade do IoT Hub e como utilizá-la para gerir os seus dispositivos. Inclui informações sobre a importação e exportação de identidades de dispositivo em massa."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 32e63b250467f5733b2e691614fe52f96f2f9d91
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Compreender o registo de identidade no seu IoT hub

Cada IoT hub tem um registo de identidade que armazena informações sobre os dispositivos autorizadas a ligar ao IoT hub. Antes de um dispositivo se possa ligar a um IoT hub, tem de existir uma entrada para que o dispositivo no registo de identidade do hub IoT. Um dispositivo também tem de autenticar com o hub IoT com base nas credenciais armazenadas no registo de identidade.

O ID de dispositivo armazenado no registo de identidade é maiúsculas e minúsculas.

Um nível elevado, o registo de identidade é uma coleção com capacidade de REST de recursos de identidade de dispositivo. Quando adiciona uma entrada no registo de identidade, o IoT Hub cria um conjunto de recursos por dispositivo, como a fila que contém mensagens da nuvem para o dispositivo em trânsito.

Utilize o registo de identidade quando precisar de:

* Aprovisionar dispositivos que ligam ao seu IoT hub.
* Controlar o acesso por dispositivo para os pontos finais orientado para o dispositivo do seu hub.

> [!NOTE]
> O registo de identidade não contém quaisquer metadados específicos da aplicação.

## <a name="identity-registry-operations"></a>Operações de registo de identidade

O registo de identidade do IoT Hub expõe as seguintes operações:

* Criar a identidade de dispositivo
* Atualizar a identidade de dispositivo
* Obter a identidade do dispositivo por ID
* Eliminar a identidade de dispositivo
* Lista de identidades até 1000
* Exportar todas as identidades para armazenamento de Blobs do Azure
* Importar as identidades do blob storage do Azure

Todas estas operações podem utilizar simultaneidade otimista, conforme especificado no [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> A única forma de obter todas as identidades no registo de identidade de um hub IoT consiste em utilizar o [exportar] [ lnk-export] funcionalidade.

Um registo de identidade do IoT Hub:

* Não contém quaisquer metadados da aplicação.
* Podem ser acedidos, como um dicionário, utilizando o **deviceId** como a chave.
* Não suporta consultas expressivas.

Uma solução de IoT normalmente tem um arquivo separado de solução específicos que contém os metadados específicos de aplicação. Por exemplo, o arquivo de solução específicos numa solução de criação inteligente seria registe o espaço que um sensor de temperatura é implementado.

> [!IMPORTANT]
> Utilize apenas o registo de identidade para operações de aprovisionamento e gestão de dispositivos. Operações de débito elevado em tempo de execução não devem depender efetuar operações no registo de identidade. Por exemplo, a verificar o estado de ligação de um dispositivo antes de enviar um comando não é um padrão suportado. Certifique-se de que marca a [taxas de limitação] [ lnk-quotas] para o registo de identidade e o [heartbeat de dispositivo] [ lnk-guidance-heartbeat] padrão.

## <a name="disable-devices"></a>Desativar dispositivos

Pode desativar dispositivos atualizando o **estado** propriedade de uma identidade no registo de identidade. Normalmente, utilizar esta propriedade em dois cenários:

* Durante um processo de orquestração de aprovisionamento. Para obter mais informações, consulte [aprovisionamento de dispositivos][lnk-guidance-provisioning].
* Se, por qualquer motivo, considera que um dispositivo for comprometido ou tornou-não autorizado.

## <a name="import-and-export-device-identities"></a>Importar e exportar as identidades de dispositivo

Pode exportar as identidades de dispositivo em massa a partir do registo de identidade de um hub IoT através da utilização de operações assíncronas no [ponto final de fornecedor de recursos do IoT Hub][lnk-endpoints]. Exportações são tarefas de longa execução que utilizam um contentor do blob fornecido pelo cliente para guardar dados de identidade de dispositivo ler a partir do registo de identidade.

Pode importar as identidades de dispositivo em massa ao registo de identidade de um IoT hub, utilizando operações assíncronas o [ponto final de fornecedor de recursos do IoT Hub][lnk-endpoints]. Importa é tarefas de longa execução que utilizam dados num contentor de blob fornecido pelo cliente para escrever dados de identidade de dispositivo no registo de identidade.

Para obter informações detalhadas sobre a importação e exportação APIs, consulte [fornecedor de recursos do IoT Hub REST APIs][lnk-resource-provider-apis]. Para obter mais informações sobre a execução de importar e exportar tarefas, consulte [em massa a gestão de identidades de dispositivo do IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Aprovisionamento de dispositivos

Os dados de dispositivo que armazena uma determinada solução de IoT dependem dos requisitos específicos dessa solução. No entanto, como no mínimo, uma solução tem de armazenar as identidades de dispositivo e chaves de autenticação. IoT Hub do Azure inclui um registo de identidade que pode armazenar os valores para cada dispositivo, como os IDs, chaves de autenticação e códigos de estado. Uma solução pode utilizar outros serviços do Azure como o table storage, armazenamento de BLOBs ou Cosmos DB para armazenar quaisquer dados adicionais do dispositivo.

*Aprovisionamento de dispositivos* é o processo de adicionar os dados de dispositivos inicial para os arquivos na sua solução. Para ativar um novo dispositivo ligar ao seu hub, tem de adicionar um ID de dispositivo e as chaves para o registo de identidade do IoT Hub. Como parte do processo de aprovisionamento, poderá ter de inicializar dados específicos do dispositivo nos outros arquivos de solução.

## <a name="device-heartbeat"></a>Heartbeat do dispositivo

O registo de identidade do IoT Hub contém um campo chamado **connectionState**. Utilizar apenas o **connectionState** campo durante o desenvolvimento e depuração. Soluções de IoT não devem consultar o campo em tempo de execução. Por exemplo, não consultar o **connectionState** campo para verificar se um dispositivo está ligado antes de enviar uma mensagem da nuvem para o dispositivo ou um SMS.

Se precisar de saber se um dispositivo estiver ligado, deve implementar a solução de IoT a *padrão de heartbeat*.

O padrão de heartbeat, o dispositivo envia mensagens do dispositivo para nuvem, pelo menos, uma vez cada valor fixo de tempo (por exemplo, pelo menos uma vez a cada hora). Por conseguinte, mesmo se um dispositivo não tem quaisquer dados para enviar, ainda envia uma mensagem de dispositivo para nuvem vazia (normalmente, com uma propriedade que identifica como um heartbeat). No lado do serviço, a solução mantém um mapa com o último heartbeat recebido para cada dispositivo. Se a solução não receber uma mensagem de heartbeat no período de tempo esperado do dispositivo, assume que há um problema com o dispositivo.

Uma implementação mais complexa pode incluir as informações da [operações de monitorização] [ lnk-devguide-opmon] para identificar dispositivos que estão a tentar estabelecer ligação ou comunicar, mas a falhar. Quando implementa o padrão de heartbeat, certifique-se [IoT Hub Quotas e limitações][lnk-quotas].

> [!NOTE]
> Se uma solução de IoT utiliza o estado da ligação unicamente para determinar se pretende enviar mensagens da nuvem para o dispositivo e as mensagens não são de difusão para grandes conjuntos de dispositivos, considere utilizar o mais simples *curto hora de expiração* padrão. Neste padrão de alcance o mesmo resultado como manter um registo de estado de ligação do dispositivo utilizar o padrão de heartbeat, ao mesmo tempo a ser mais eficiente. Se o pedido confirmações de mensagem, o IoT Hub pode notificá-lo sobre os dispositivos que são capazes receber mensagens e que não são.

## <a name="device-lifecycle-notifications"></a>Notificações de ciclo de vida do dispositivo

IoT Hub pode notificar a sua solução de IoT quando uma identidade de dispositivo é criada ou eliminada pelo envio de notificações de ciclo de vida do dispositivo. Para tal, a solução de IoT tem de criar uma rota e definir a origem de dados igual a *DeviceLifecycleEvents*. Por predefinição, não existem notificações de ciclo de vida são enviadas, ou seja, sem estas rotas existem previamente. A mensagem de notificação inclui as propriedades e corpo.

Propriedades: As propriedades do sistema de mensagens têm o prefixo de `'$'` símbolo.

| Nome | Valor |
| --- | --- |
$content-tipo | application/json |
$iothub-enqueuedtime |  Hora em que a notificação foi enviada |
$iothub-mensagem-origem | deviceLifecycleEvents |
$content-codificação | UTF-8 |
opType | **createDeviceIdentity** ou **deleteDeviceIdentity** |
hubName | Nome do IoT Hub |
deviceId | ID do dispositivo |
operationTimestamp | ISO8601 timestamp da operação |
esquema de mensagem iothub | deviceLifecycleNotification |

Corpo: Esta secção está no formato JSON e representa o duplo da identidade de dispositivo criada. Por exemplo,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
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

## <a name="device-identity-properties"></a>Propriedades de identidade de dispositivo

Identidades de dispositivo são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | Descrição |
| --- | --- | --- |
| deviceId |atualizações necessárias, só de leitura no |Uma cadeia de maiúsculas e minúsculas (até 128 carateres de comprimento) de carateres alfanuméricos ASCII 7 bits plus determinados caracteres especiais: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| generationId |obrigatório só de leitura |Uma IoT hub-gerados, maiúsculas e minúsculas cadeia até 128 carateres de comprimento. Este valor é utilizado para distinguir dispositivos com o mesmo **deviceId**, quando tiver sido eliminadas e recriadas. |
| ETag |obrigatório só de leitura |Uma cadeia representando o num ETag fraco para a identidade de dispositivo, como por [RFC7232][lnk-rfc7232]. |
| autenticação |Opcional |Um objeto composto que contém os materiais de segurança e informações de autenticação. |
| auth.symkey |Opcional |Um objeto composto que contém um site primário e uma chave secundária, armazenados em formato base64. |
| status |Necessário |Um indicador de acesso. Pode ser **ativado** ou **desativado**. Se **ativado**, o dispositivo tem permissão para estabelecer a ligação. Se **desativado**, este dispositivo não é possível aceder a qualquer ponto final orientado para o dispositivo. |
| statusReason |Opcional |Uma cadeia de carateres longa 128 que armazena o motivo para o estado de identidade do dispositivo. Todos os carateres UTF-8 são permitidos. |
| statusUpdateTime |Só de leitura |Um indicador temporal, que mostra a data e hora da última atualização do Estado. |
| connectionState |Só de leitura |Um campo com a indicação de estado da ligação: o **ligado** ou **desligado**. Este campo representa a vista do Iothub do Estado de ligação do dispositivo. **Importante**: Este campo deve ser utilizado apenas para fins de desenvolvimento/depuração. O estado da ligação é atualizado apenas para dispositivos com o MQTT ou AMQP. Além disso, se baseia no nível de protocolo pings (MQTT pings ou AMQP pings) e pode ter um atraso máximo de apenas 5 minutos. Por esta razão, podem existir falsos positivos, tais como dispositivos comunicados como ligado, mas que estão desligados. |
| connectionStateUpdatedTime |Só de leitura |Um indicador temporal, que mostra a data e hora da última o estado da ligação foi atualizado. |
| lastActivityTime |Só de leitura |Um indicador temporal, que mostra a data e hora da última o dispositivo ligado, recebidos ou enviados uma mensagem. |

> [!NOTE]
> Estado da ligação só pode representar a vista do Iothub do Estado da ligação. Atualizações para este estado podem sofrer um atraso, consoante as condições de rede e configurações.

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no guia de programadores do IoT Hub incluem:

* [Pontos finais de IoT Hub] [ lnk-endpoints] descreve os vários pontos finais que cada IoT hub expõe para operações de gestão e de tempo de execução.
* [Limitação e quotas] [ lnk-quotas] descreve as quotas e limitação comportamentos que se aplicam ao serviço do IoT Hub.
* [Azure SDKs IoT do serviço e dispositivo] [ lnk-sdks] indica o idioma de vários SDKs que pode utilizar ao desenvolver aplicações de serviço e dispositivo que interagem com o IoT Hub.
* [A linguagem de consulta do IoT Hub] [ lnk-query] descreve o idioma de consulta pode utilizar para obter informações a partir do IoT Hub sobre os dispositivos duplos e tarefas.
* [Suporte do IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como utilizar o registo de identidade do IoT Hub, poderá estar interessado nos seguintes tópicos do guia de programadores do IoT Hub:

* [Controlar o acesso ao IoT Hub][lnk-devguide-security]
* [Utilizar dispositivos duplos para sincronizar o estado e configurações][lnk-devguide-device-twins]
* [Invocar um método direto num dispositivo][lnk-devguide-directmethods]
* [Agenda de tarefas em vários dispositivos][lnk-devguide-jobs]

Se pretender experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado no tutorial seguinte do IoT Hub:

* [Introdução ao IoT Hub do Azure][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
