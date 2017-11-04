---
title: Compreender os pontos finais do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - informações de referência sobre o IoT Hub pontos finais orientado para o dispositivo e direcionadas para o serviço."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: 47f8949139c48ffa79f5530552b0a2e27b0f9ee0
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reference---iot-hub-endpoints"></a>Referência - pontos finais de IoT Hub

## <a name="iot-hub-names"></a>Nomes de IoT Hub

Pode encontrar o nome do hub IoT que aloja os pontos finais no portal de **descrição geral** painel. Por predefinição, o nome DNS de um hub IoT aspeto: `{your iot hub name}.azure-devices.net`.

Pode utilizar o DNS do Azure para criar um nome DNS personalizado para o seu IoT hub. Para obter mais informações, consulte [DNS do Azure de utilização para fornecer definições de domínio personalizado para um serviço do Azure](../dns/dns-custom-domain.md#azure-iot).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos finais de IoT Hub incorporadas

IoT Hub do Azure é um serviço de multi-inquilino que expõe a funcionalidade para vários atores. O diagrama seguinte mostra os pontos finais vários que o IoT Hub expõe.

![Pontos finais do Hub IoT][img-endpoints]

A lista seguinte descreve os pontos finais:

* **Fornecedor de recursos**. O fornecedor de recursos do IoT Hub expõe um [do Azure Resource Manager] [ lnk-arm] interface. Esta interface que permite que os proprietários de subscrição do Azure para criar e eliminar os hubs IoT e para atualizar as propriedades do hub IoT. As propriedades do IoT Hub governem [políticas de segurança ao nível do hub][lnk-accesscontrol], por oposição a controlo de acesso ao nível do dispositivo e as opções de funcionais para as mensagens de nuvem para o dispositivo e de dispositivo para a nuvem. O fornecedor de recursos do IoT Hub também lhe permite [exportar as identidades de dispositivo][lnk-importexport].
* **Gestão de identidades de dispositivo**. Cada IoT hub expõe um conjunto de pontos finais de HTTPS REST para gerir identidades de dispositivo (criar, obter, atualizar e eliminar). [Identidades de dispositivo] [ lnk-device-identities] são utilizadas para controlo de acesso e autenticação do dispositivo.
* **Gestão do dispositivo duplo**. Cada IoT hub expõe um conjunto de ponto final de REST de HTTPS de orientado para o serviço para consulta e atualização [dispositivos duplos] [ lnk-twins] (atualização etiquetas e propriedades).
* **Tarefas de gestão**. Cada IoT hub expõe um conjunto de ponto final de REST de HTTPS de orientado para o serviço para consultar e gerir [tarefas][lnk-jobs].
* **Pontos finais de dispositivo**. Para cada dispositivo no registo de identidade, o IoT Hub expõe um conjunto de pontos finais:

  * *Enviar mensagens do dispositivo para nuvem*. Um dispositivo utiliza este ponto final para [enviar mensagens do dispositivo para nuvem][lnk-d2c].
  * *Receber mensagens da nuvem para o dispositivo*. Um dispositivo utiliza este ponto final para receber visado [mensagens da nuvem para dispositivo][lnk-c2d].
  * *Iniciar carregamentos de ficheiros*. Um dispositivo utiliza este ponto final para receber um URI de SAS do armazenamento do Azure a partir do IoT Hub para [carregar um ficheiro][lnk-upload].
  * *Obter e atualizar as propriedades do dispositivo duplo*. Um dispositivo utiliza este ponto final para aceder ao respetivo [dispositivo duplo][lnk-twins]do propriedades.
  * *Receber pedidos de método direto*. Um dispositivo utiliza este ponto final para escutar [método direto][lnk-methods]do pedidos.

    Estes pontos finais são expostos utilizando [MQTT v 3.1.1][lnk-mqtt], 1.1 de HTTPS, e [AMQP 1.0] [ lnk-amqp] protocolos. Também está disponível através de AMQP [WebSockets] [ lnk-websockets] na porta 443.

* **Pontos finais de serviço**. Cada IoT hub expõe um conjunto de pontos finais para a sua solução de back-end comunicar com os seus dispositivos. Com uma exceção, estes pontos finais são apenas expostas utilizando o [AMQP] [ lnk-amqp] protocolo. O ponto final de invocação de método está exposto através do protocolo HTTPS.
  
  * *Receber mensagens dispositivo-nuvem*. Este ponto final é compatível com [Event Hubs do Azure][lnk-event-hubs]. Serviço de back-end pode utilizá-la ao ler o [mensagens do dispositivo para nuvem] [ lnk-d2c] enviadas pelos seus dispositivos. Pode criar os pontos finais personalizados no seu IoT hub para além deste ponto final incorporado.
  * *Enviar mensagens de nuvem para o dispositivo e receber em que as confirmações de entrega*. Estes pontos finais ativar a sua solução de back-end enviar fiável [mensagens da nuvem para dispositivo][lnk-c2d]e receber o em que as confirmações de expiração ou entrega correspondente.
  * *Receber notificações de ficheiro*. Este ponto final de serviço de mensagens permite-lhe receber notificações de quando os seus dispositivos com êxito carregar um ficheiro. 
  * *Direcionar a invocação de métodos*. Este ponto final permite que um serviço de back-end invocar um [método direto] [ lnk-methods] num dispositivo.
  * *Receber eventos de monitorização de operações*. Este ponto final permite-lhe receber eventos de monitorização, se o seu hub IoT tiver sido configurado para emiti-los de operações. Para obter mais informações, consulte [operações de IoT Hub monitorização][lnk-operations-mon].

O [SDKs IoT do Azure] [ lnk-sdks] artigo descreve as várias formas de aceder a estes pontos finais.

Todos os pontos finais de IoT Hub utilizam o [TLS] [ lnk-tls] protocolo e nenhum ponto final nunca está exposta em canais não encriptada/protegida.

## <a name="custom-endpoints"></a>Pontos finais personalizados

Pode ligar os serviços do Azure existentes na sua subscrição ao seu IoT hub para funcionar como pontos finais para o encaminhamento de mensagens. Estes pontos finais atuam como pontos finais de serviço e são utilizados como sinks rotas de mensagem. Dispositivos não é possível escrever diretamente para os pontos finais adicionais. Para saber mais sobre as rotas de mensagem, consulte a entrada de guia para programadores sobre [enviar e receber mensagens com o IoT hub][lnk-devguide-messaging].

Atualmente, o IoT Hub suporta os seguintes serviços do Azure como pontos finais adicionais:

* Contentores de armazenamento do Azure
* Event Hubs
* Filas de Service Bus
* Tópicos de Service Bus

IoT Hub precisa de acesso de escrita para estes pontos finais de serviço para o encaminhamento de mensagens para trabalhar. Se configurar pontos finais da sua através do portal do Azure, são adicionadas as permissões necessárias para si. Certifique-se de que configura os serviços para suportar o débito esperado. Quando configurar a sua solução de IoT pela primeira vez, terá de monitorizar os seus pontos finais adicionais e fazer ajustamentos necessários para o carregamento atual.

Se uma mensagem correspondências várias rotas que todos os apontam para o ponto final do mesmo, o IoT Hub fornece uma mensagem para esse ponto final apenas uma vez. Por conseguinte, não terá de configurar a eliminação de duplicados no seu fila do Service Bus ou tópico. Filas particionadas, a afinidade de partição garante que a ordenação de mensagem.

Para os limites no número de pontos finais, pode adicionar, consulte [Quotas e limitação][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Quando utilizar contentores de armazenamento do Azure

IoT Hub apenas suporta a escrita de dados para contentores de armazenamento do Azure como blobs a [Apache Avro](http://avro.apache.org/) formato. IoT Hub mensagens de lotes e escreve dados para um blob quando chegar a um determinado tamanho ou depois de um determinado período de tempo decorrido, que ocorre primeiro. IoT Hub não irá escrever um blob vazio se não houver nenhum dado para escrita.

Predefinições para a seguinte convenção de nomenclatura de ficheiro do IoT Hub:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Pode utilizar qualquer desejar de convenção de nomenclatura de ficheiros, no entanto, deve utilizar listados todos os tokens.

### <a name="when-using-service-bus-queues-and-topics"></a>Ao utilizar tópicos e filas do Service Bus

Utilizado como pontos finais de IoT Hub não pode ter de tópicos e filas de Service Bus **sessões** ou **duplicado deteção** ativada. Se qualquer um dessas opções estiverem ativado, o ponto final é apresentado como **Unreachable** no portal do Azure.

## <a name="field-gateways"></a>Gateways de campo

Uma solução de IoT, um *gateway de campo* se encontra entre os seus dispositivos e os pontos finais de IoT Hub. Está normalmente localizado próximo os seus dispositivos. Os dispositivos comunicam diretamente com o gateway de campo através da utilização de um protocolo suportado pelos dispositivos. O gateway de campo liga a um ponto final de IoT Hub utilizando um protocolo que é suportado pelo IoT Hub. Um gateway de campo pode ser um dispositivo de hardware dedicado ou um computador de baixa energia com o software do gateway personalizado.

Pode utilizar [Azure IoT Edge] [ lnk-iot-edge] para implementar um gateway de campo. Limite de IoT oferece funcionalidades como multiplexação comunicações a partir de vários dispositivos para a mesma ligação do IoT Hub.

## <a name="next-steps"></a>Passos seguintes

Outros tópicos de referência neste guia para programadores do IoT Hub incluem:

* [Idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens][lnk-devguide-query]
* [Quotas e limitação][lnk-devguide-quotas]
* [Suporte de MQTT do IoT Hub][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
