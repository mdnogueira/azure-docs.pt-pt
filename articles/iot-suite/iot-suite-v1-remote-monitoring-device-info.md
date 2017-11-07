---
title: "Metadados de informações de dispositivos na solução de monitorização remota | Microsoft Docs"
description: "Uma descrição sobre a solução pré-configurada de monitorização remota e respetiva arquitetura do Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 5ada9413b1bb0923df96cfd93c3a0a875e80dd28
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Metadados de informações de dispositivos na solução pré-configurada de monitorização remota

A solução pré-configurada de monitorização remota do Azure IoT Suite demonstra uma abordagem para gerir os metadados do dispositivo. Este artigo descreve a abordagem que esta solução demora para que possa compreender:

* Os metadados do dispositivo em que a solução armazena.
* Como a solução gere os metadados do dispositivo.

## <a name="context"></a>Contexto

Monitorização remota pré-configurada solução utiliza [IoT Hub do Azure] [ lnk-iot-hub] para ativar os seus dispositivos enviar dados para a nuvem. A solução armazena informações sobre os dispositivos em três localizações diferentes:

| Localização | Informações armazenadas | Implementação |
| -------- | ------------------ | -------------- |
| Registo de identidade | Id de dispositivo, chaves de autenticação, estado ativado | Incorporada no IoT Hub |
| Dispositivos duplos | Metadados: propriedades comunicadas propriedades pretendidas, etiquetas | Incorporada no IoT Hub |
| Cosmos DB | Histórico de comando e o método | Personalizada para soluções |

O IoT Hub inclui um [registo de identidade de dispositivo] [ lnk-identity-registry] para gerir o acesso a um IoT hub e utiliza [dispositivos duplos] [ lnk-device-twin] para gerir os metadados do dispositivo. Há também um remoto monitorização específicos *registo do dispositivo* que armazena o histórico de comando e o método. A solução de monitorização remota utiliza um [Cosmos DB] [ lnk-docdb] base de dados para implementar um arquivo personalizado para o histórico de comando e o método.

> [!NOTE]
> A solução pré-configurada de monitorização remota mantém o registo de identidade do dispositivo sincronizadas com as informações na base de dados de base de dados do Cosmos. Ambos utilizam o mesmo id de dispositivo para identificar exclusivamente cada dispositivo ligado ao seu IoT hub.

## <a name="device-metadata"></a>Metadados do dispositivo

IoT Hub mantém um [dispositivo duplo] [ lnk-device-twin] para cada dispositivo simulado e físico ligado a uma solução de monitorização remota. A solução utiliza dispositivos duplos para gerir os metadados associados a dispositivos. Um dispositivo duplo é um documento JSON mantido pelo IoT Hub e a solução utiliza a API do Hub IoT para interagir com dispositivos duplos.

Um dispositivo duplo armazena três tipos de metadados:

- *Comunicado propriedades* são enviadas para um hub IoT por um dispositivo. Na solução de monitorização remota, dispositivos simulados enviam propriedades comunicadas no arranque e em resposta a **alterar estado do dispositivo** comandos e métodos. Pode ver as propriedades comunicadas no **lista de dispositivos** e **detalhes do dispositivo** no portal de solução. Propriedades comunicadas são só de leitura.
- *Pretender propriedades* são obtidos a partir do hub IoT pelos dispositivos. É da responsabilidade do dispositivo para fazer qualquer alteração no dispositivo de configuração necessária. Também é da responsabilidade do dispositivo pode informar a alteração ao hub como uma propriedade que relatados. Pode definir um valor de propriedade pretendido através do portal de solução.
- *Etiquetas* existam apenas no dispositivo duplo e nunca estão sincronizadas com um dispositivo. Pode definir valores de etiqueta no portal de solução e utilizá-los ao filtrar a lista de dispositivos. A solução também utiliza uma etiqueta para identificar o ícone a apresentar para um dispositivo no portal de solução.

Exemplo comunicadas as propriedades dos dispositivos simulados incluem o fabricante, número de modelo, latitude e longitude. Dispositivos simulados também devolvem a lista de métodos suportados como uma propriedade que relatados.

> [!NOTE]
> O código do dispositivo simulado utiliza apenas as propriedades pretendidas **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** para atualizar as propriedades comunicadas enviadas de volta para o Hub IoT. Todos os outros pedidos de alteração de propriedade pretendido são ignorados.

Um documento de metadados informações do dispositivo JSON armazenado na base de dados de BD do Cosmos de registo do dispositivo tem a estrutura seguinte:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> Informações do dispositivo também podem incluir metadados para descrever a telemetria que do dispositivo envia ao IoT Hub. A solução de monitorização remota utiliza estes metadados de telemetria para personalizar a forma como o dashboard apresenta [telemetria dinâmica][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Ciclo de vida

Quando cria um dispositivo pela primeira vez no portal de solução, a solução cria uma entrada na base de dados de base de dados do Cosmos para armazenar o histórico de comando e o método. Neste momento, a solução também cria uma entrada para o dispositivo no registo de identidade do dispositivo, gera as chaves de que dispositivo utiliza para autenticar com o IoT Hub. Também cria um dispositivo duplo.

Quando um dispositivo ligado pela primeira vez para a solução, envia uma mensagem de informações do dispositivo e propriedades comunicadas. Os valores de propriedade comunicados são guardados automaticamente no dispositivo duplo. As propriedades que relatados incluem o fabricante do dispositivo, o número de modelo, o número de série e uma lista de métodos suportados. A mensagem de informações de dispositivos inclui a lista de comandos que o dispositivo suporta incluindo informações sobre quaisquer parâmetros de comando. Quando a solução recebe esta mensagem, atualiza as informações do dispositivo na base de dados de base de dados do Cosmos.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Ver e editar as informações do dispositivo no portal de solução

A lista de dispositivos no portal de solução apresenta as seguintes propriedades do dispositivo como colunas por predefinição: **estado**, **DeviceId**, **fabricante**, **modelo Número**, **número de série**, **Firmware**, **plataforma**, **processador**, e  **Instalado RAM**. Pode personalizar as colunas clicando **editor colunas**. As propriedades do dispositivo **Latitude** e **Longitude** unidade a localização no mapa do Bing no dashboard.

![Editor de colunas na lista de dispositivos][img-device-list]

No **detalhes do dispositivo** painel no portal de solução, pode editar as etiquetas e propriedades pretendidas (comunicadas propriedades são só de leitura).

![Painel de detalhes do dispositivo][img-device-edit]

Pode utilizar o portal de solução para remover um dispositivo da solução. Quando remove um dispositivo, a solução elimina a entrada de dispositivo a partir do registo de identidade e, em seguida, o dispositivo duplo. A solução também remove informações relacionadas com o dispositivo da base de dados de base de dados do Cosmos. Antes de poder remover um dispositivo, tem de o desativar.

![Remover dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Processamento de mensagens de informações do dispositivo

As mensagens de informações de dispositivo enviadas por um dispositivo são diferentes das mensagens de telemetria. Mensagens de informações do dispositivo incluem os comandos de que um dispositivo pode responder e nenhum histórico de comando. IoT Hub propriamente dito não tem conhecimento de metadados contidos numa mensagem de informações do dispositivo e processa a mensagem da mesma forma que processa mensagens de qualquer dispositivo para a nuvem. Na solução de monitorização remota, um [Azure Stream Analytics] [ lnk-stream-analytics] tarefa (ASA) lê as mensagens a partir do IoT Hub. O **DeviceInfo** filtros para mensagens que contêm de tarefa de stream analytics **"ObjectType": "DeviceInfo"** e reencaminha-os para o **EventProcessorHost** instância de anfitrião que executa uma tarefa de web. Lógica de **EventProcessorHost** instância utiliza o ID de dispositivo para localizar o registo da BD do Cosmos para o dispositivo específico e atualizar o registo.

> [!NOTE]
> Uma mensagem de informações do dispositivo é uma mensagem de dispositivo para nuvem padrão. A solução distingue entre mensagens de telemetria e mensagens de informações do dispositivo utilizando ASA consultas.

## <a name="next-steps"></a>Passos seguintes

Agora que concluiu a aprender como pode personalizar as soluções pré-configuradas, pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Descrição geral de solução pré-configurada de manutenção preditiva][lnk-predictive-overview]
* [Perguntas mais frequentes sobre o IoT Suite][lnk-faq]
* [Segurança de IoT desde o início][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-v1-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-v1-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-v1-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
