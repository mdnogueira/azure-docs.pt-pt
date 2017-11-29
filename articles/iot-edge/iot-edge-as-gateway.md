---
title: Compreender como um dispositivo de limite de IoT do Azure pode ser utilizado como um gateway para outros dispositivos | Microsoft Docs
description: "Utilize o limite de IoT do Azure para criar um transparente opaco ou dispositivo de gateway de proxy que envia dados de vários dispositivos a jusante para a nuvem ou os processos que localmente."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c1ae74127fce40a6f1ab412f25797076dda9d888
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Como um dispositivo de limite de IoT que pode ser utilizado como um gateway de - de pré-visualização

O objetivo de gateways em soluções de IoT é específico para a solução e combinar conectividade do dispositivo com a análise de limite. Limite de IoT do Azure pode ser utilizada para satisfazer todas as necessidades de um gateway de IoT independentemente se estão relacionados com a conectividade, a identidade ou análise de limite. Padrões de gateway neste artigo apenas Consulte características da conectividade do dispositivo a jusante e identidade de dispositivo, não como dados de dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões
Existem três padrões de utilização de um dispositivo de limite de IoT como um gateway: protocolo transparente, tradução e a tradução de identidade:
* **Transparente** – dispositivos teoria conseguiu estabelecer ligação ao IoT Hub podem ligar a um dispositivo de gateway em vez disso. Isto implica que os dispositivos a jusante tem as suas próprias identidades do IoT Hub e estiver a utilizar qualquer um dos protocolos MQTT, AMQP ou HTTP. O gateway transmite simplesmente as comunicações entre os dispositivos e o IoT Hub. Os dispositivos não estiverem cientes que estão a comunicar com a nuvem através de um gateway e um utilizador interage com os dispositivos no IoT Hub não tem conhecimento do dispositivo de gateway intermédio. Assim, o gateway é transparente. Consulte o [criar um gateway transparente] [ lnk-iot-edge-as-transparent-gateway] procedimentos para informações específicas sobre a utilização de um dispositivo de limite de IoT como um gateway transparente.
* **Tradução de protocolo** – dispositivos que não suportam MQTT, AMQP ou HTTP utilizam um dispositivo de gateway para enviar dados para o IoT Hub. O gateway está inteligente compreender esse protocolo utilizado pelos dispositivos a jusante; No entanto é o único dispositivo que tenha a identidade do IoT Hub. Todas as informações parece que este for proveniente de um dispositivo, o gateway. Isto implica que dispositivos a jusante tem incorporar as informações de identificação adicionais as mensagens se quiser aplicações em nuvem razão sobre os dados numa base por dispositivo. Além disso, o IoT Hub primitivos como duplo e métodos só estão disponíveis para o dispositivo de gateway, não a jusante dispositivos.
* **Tradução de identidade** -os dispositivos que não é possível ligar ao IoT Hub ligar a um dispositivo de gateway que fornece o IoT Hub tradução de identidade e protocolo em nome dos dispositivos a jusante. O gateway é inteligente compreender o protocolo utilizado pelos dispositivos a jusante, fornecer-lhes a identidade e traduzir primitivos do IoT Hub. Dispositivos a jusante aparecem no IoT Hub como primeira classe dispositivos duplos e métodos. Um utilizador pode interagir com os dispositivos do IoT Hub does não tem conhecimento do dispositivo de gateway intermédio.

![Diagramas de padrões de gateway][1]

## <a name="use-cases"></a>Casos de utilização
Todos os padrões de gateway fornecem as seguintes vantagens:
* **Análise de contorno** – total de serviços de utilização AI localmente para processar os dados provenientes de dispositivos a jusante sem enviar telemetria fidelidade para a nuvem. Localize e reagir a insights localmente e enviar apenas um subconjunto dos dados ao IoT Hub. 
* **Isolamento de dispositivo a jusante** – o dispositivo de gateway pode proteger todos os dispositivos a jusante exposição à internet. -Pode manter-se entre uma rede de Olocar que não tem conectividade e uma rede IT que fornece acesso à web. 
* **Ligação de multiplexação** - todos os dispositivos a ligação ao IoT Hub através de um limite de IoT dispositivo irá utilizar a mesma ligação subjacente.
* **Tráfego suavização** -dispositivo de limite de IoT a irão implementar automaticamente término exponencial em caso de limitação, enquanto as mensagens localmente a persistência do IoT Hub. Isto faz com que a solução resiliente para picos de tráfego.
* **Suporte offline limitado** - o dispositivo de gateway irá armazenar localmente as mensagens e duplo de atualizações que não é possível entregar ao IoT Hub.

Uma tradução de protocolos de does de gateway também pode efetuar análise de limite, isolamento de dispositivo, suavização de tráfego e offline suporte para dispositivos existentes e novos dispositivos que são recurso restringido. Muitos dispositivos existentes são produzir dados que podem ligar informações empresariais; No entanto não foram concebidas com conectividade de nuvem em mente. Gateways opaco permitir que estes dados para ser desbloqueado e utilizados numa solução de IoT ponto a ponto.

Um gateway que suporta a conversão de identidade fornecer as vantagens de tradução de protocolos e adicionalmente permitem uma gestão completa de dispositivos a jusante da nuvem. Todos os dispositivos no seu IoT solução são apresentados no IoT Hub independentemente do protocolo de com estes enunciar.

## <a name="cheat-sheet"></a>Nota
Eis uma referência rápida folha que compara primitivos do IoT Hub quando utilizar transparente, opaco e gateways de proxy.

| &nbsp; | Gateway transparente | Tradução de protocolos | Tradução de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registo de identidade do IoT Hub | Identidades de todos os dispositivos ligados | Apenas a identidade de dispositivo de gateway | Identidades de todos os dispositivos ligados |
| Dispositivo duplo | Cada dispositivo ligado tem o seu próprio dispositivo duplo | Apenas o gateway tem um módulo e dispositivos duplos | Cada dispositivo ligado tem o seu próprio dispositivo duplo |
| Métodos Direct e mensagens da nuvem para dispositivo | A nuvem pode resolver individualmente cada dispositivo ligado | Nuvem apenas pode resolver o dispositivo de gateway | A nuvem pode resolver individualmente cada dispositivo ligado |
| [Limitações do IoT Hub e quotas][lnk-iothub-throttles-quotas] | Aplicar a cada dispositivo | Se aplicam ao dispositivo de gateway | Aplicar a cada dispositivo |

Quando utiliza um padrão de gateway opaco, todos os dispositivos que se ligam através desse gateway partilham a mesma fila de nuvem para o dispositivo, que pode conter no máximo 50 mensagens. Segue-se que o padrão de gateway opaco deve ser utilizado apenas quando muito alguns dispositivos se ligam através de cada gateway de campo e os respetivos tráfego de nuvem para o dispositivo é reduzido.

## <a name="next-steps"></a>Passos seguintes
Utilizar um dispositivo de limite de IoT como uma [gateway transparente][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png