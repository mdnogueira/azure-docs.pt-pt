---
title: "Descrição geral do limite do Azure IoT | Microsoft Docs"
description: "Descreve os conceitos fundamentais da arquitetura do Azure IoT Edge como gateways, módulos e os corretores."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.openlocfilehash: 11f6375c319c8945b0278003ee08215715f7ac42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-edge-architectural-concepts"></a>Conceitos de arquitetura de limite de IoT do Azure

Antes de examinar qualquer código de exemplo ou criar o seu próprio gateway de campo com o limite de IoT, deve compreender os conceitos chave que sustentam a arquitetura do IoT Edge.

## <a name="iot-edge-modules"></a>Módulos de limite de IoT

Pode criar um gateway com limite de IoT do Azure através da criação e montagem *módulos de limite de IoT*. Os módulos utilizam *mensagens* para trocar dados entre si. Um módulo recebe uma mensagem, efetua algumas ações na mesma, opcionalmente, transforma-a numa nova mensagem e, em seguida, publica-a para ser processada por outros módulos. Alguns módulos podem produzir apenas novas mensagens e nunca processar as mensagens recebidas. Uma cadeia de módulos cria um pipeline de processamento de dados e cada módulo realiza uma transformação dos dados a um dado momento nesse pipeline.

![Uma cadeia de módulos no gateway criada com o Azure IoT Edge][1]

Limite de IoT contém os seguintes componentes:

* Módulos pré-escritos que efetuam funções comuns do gateway.
* As interfaces que um programador pode utilizar para escrever módulos personalizados.
* A infraestrutura necessária para implementar e executar um conjunto de módulos.

O SDK fornece uma camada de abstração que lhe permite criar gateways para executar em várias plataformas e sistemas operativos.

![Camada de abstração do IoT Azure Edge][2]

## <a name="messages"></a>Mensagens

Apesar de a ideia dos módulos a transmitir mensagens entre si ser uma forma conveniente de conceptualizar o funcionamento do gateway, esta não reflete com precisão o que acontece. Módulos de limite de IoT utilizam um mediador para comunicar entre si. Módulos publiquem mensagens do Mediador (utilizando os padrões de mensagens, tais como barramento ou a publicação) e, em seguida, permita que o Mediador de encaminhar a mensagem para os módulos ligados ao mesmo.

Os módulos utilizam a função **Broker_Publish** para publicar mensagens no mediador. O mediador entrega as mensagens a um módulo ao invocar uma função de chamada de retorno. Uma mensagem é composta por um conjunto de propriedades chave/valor e conteúdo transmitido como um bloco de memória.

![A função de Mediador no Azure IoT Edge][3]

## <a name="message-routing-and-filtering"></a>Encaminhamento e filtragem de mensagens

Existem duas formas de direcionar a mensagens para os módulos de limite de IoT corretos:

* Pode passar um conjunto de ligações para o mediador para que o mediador confie a origem e dependente para cada módulo.
* Um módulo pode filtrar as propriedades da mensagem.

Os módulos só devem tomar medidas relativamente a uma mensagem se esta se destinar aos mesmos. Ligações e a filtragem de forma eficaz de mensagens criam um pipeline de mensagem.

## <a name="next-steps"></a>Passos seguintes

Para ver estes conceitos aplicados um exemplo, pode executar, consulte [arquitetura de explorar o Azure IoT Edge][lnk-hello-world].

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
