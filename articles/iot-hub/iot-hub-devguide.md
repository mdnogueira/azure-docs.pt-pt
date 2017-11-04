---
title: Guia para programadores do IoT Hub do Azure | Microsoft Docs
description: "O guia para programadores do IoT Hub do Azure inclui debates de pontos finais, segurança, o registo de identidade, gestão de dispositivos, métodos diretos, dispositivos duplos, carregamentos de ficheiros, tarefas, o idioma de consulta do IoT Hub e mensagens."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: 27b296092335ec5b95e8f259756aaf9572da1c16
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-iot-hub-developer-guide"></a>Guia para programadores do IoT Hub do Azure

IoT Hub do Azure é um serviço completamente gerido que o ajuda a ativar fiáveis e seguras comunicações bidirecionais entre milhões de dispositivos e uma solução de back-end.

IoT Hub do Azure fornece-lhe:

* Proteger as comunicações através da utilização de credenciais de segurança por dispositivo e controlo de acesso.
* Várias opções de comunicação de hiper escala de dispositivo para nuvem e da nuvem para o dispositivo.
* Armazenamento consultável de informações de estado por dispositivo e os dados de metadados.
* Conectividade do dispositivo fácil com bibliotecas de dispositivo para os idiomas e plataformas mais populares.

Este guia para programadores do IoT Hub inclui os seguintes artigos:

* [Orientações de comunicação do dispositivo para nuvem] [ lnk-d2c-guidance] ajuda a escolha entre mensagens do dispositivo para nuvem, propriedades comunicado dispositivo duplo e o carregamento de ficheiros.
* [Orientações de comunicação de nuvem para o dispositivo] [ lnk-c2d-guidance] ajuda a escolha entre os métodos diretos, dispositivo duplo propriedades pretendidas e mensagens da nuvem para o dispositivo.
* [Dispositivo para nuvem e da nuvem para o dispositivo de mensagens com o IoT Hub] [ devguide-messaging] descreve as funcionalidades mensagens (dispositivo para a nuvem e nuvem para o dispositivo) que o IoT Hub expõe.
  * [Enviar mensagens do dispositivo para nuvem ao IoT Hub][devguide-messages-d2c].
  * [Ler mensagens do dispositivo para nuvem a partir do ponto de final incorporado][devguide-builtin].
  * [Utilizar os pontos finais personalizados e regras de encaminhamento para mensagens do dispositivo para nuvem][devguide-custom].
  * [Enviar mensagens de nuvem para o dispositivo a partir do IoT Hub][devguide-messages-c2d].
  * [Criar e ler as mensagens de IoT Hub][devguide-format].
* [Carregar ficheiros a partir de um dispositivo] [ devguide-upload] descreve como pode carregar ficheiros a partir de um dispositivo. O artigo também inclui informações sobre tópicos, tais como as notificações pode enviar o processo de carregamento.
* [Gerir identidades de dispositivo do IoT Hub] [ devguide-identities] descreve as informações que do cada IoT hub arquivos de registo de identidade e como pode aceder e modificá-lo.
* [Controlar o acesso ao IoT Hub] [ devguide-security] descreve o modelo de segurança utilizado para conceder acesso à funcionalidade do IoT Hub para ambos os dispositivos e componentes de nuvem. O artigo inclui informações sobre a utilização de tokens e certificados x. 509 e detalhes sobre as permissões que pode conceder.
* [Utilizar dispositivos duplos para sincronizar o estado e as configurações] [ devguide-device-twins] descreve o *dispositivo duplo* conceito e a funcionalidade expõe como sincronizar um dispositivo com o respetivo dispositivo duplo. O artigo inclui informações sobre os dados armazenados num dispositivo duplo.
* [Invocar um método direto num dispositivo] [ devguide-directmethods] descreve o ciclo de vida de um método direto, informações sobre como invocar métodos num dispositivo da sua aplicação de back-end e processar o método direto no seu dispositivo.
* [Agendar tarefas em vários dispositivos] [ devguide-jobs] descreve como pode agendar tarefas em vários dispositivos. O artigo descreve como submeter as tarefas que executam tarefas como executar um método direto, a atualização de um dispositivo com um dispositivo duplo. Descreve também como consultar o estado de uma tarefa.
* [Referenciar - escolher um protocolo de comunicação] [ devguide-protocol] descreve os protocolos de comunicação que o IoT Hub suporta para a comunicação do dispositivo e lista as portas que devem ser apresentadas.
* [Referência - pontos finais de IoT Hub] [ devguide-endpoints] descreve os vários pontos finais que cada IoT hub expõe para operações de gestão e de tempo de execução. O artigo também descreve como pode criar os pontos finais adicionais no seu IoT hub e como utilizar um gateway de campo para ativar a conetividade para os pontos finais de IoT Hub em cenários não padrão.
* [Referência - idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens] [ devguide-query] descreve nesse idioma de consulta de IoT Hub permite-lhe obter as informações do seu hub sobre os dispositivos duplos e tarefas.
* [Referência - quotas e limitação] [ devguide-quotas] resume as quotas definidas no serviço de IoT Hub e a limitação que ocorre quando exceder uma quota.
* [Referenciar - preços] [ devguide-pricing] fornece informações gerais sobre SKUs diferentes e preços para o IoT Hub e detalhes sobre como as várias funcionalidades do IoT Hub são limitadas como mensagens pelo IoT Hub.
* [Referência - SDKs de serviço e dispositivo] [ devguide-sdks] lista os SDKs IoT do Azure para desenvolver aplicações de serviço e dispositivo que interagem com o seu IoT hub. O artigo inclui ligações para documentação da API online.
* [Referência - suporte de IoT Hub MQTT] [ devguide-mqtt] fornece informações detalhadas sobre como o IoT Hub suporta o protocolo MQTT. O artigo descreve o suporte para o protocolo MQTT incorporado para os SDKs IoT do Azure e fornece informações sobre como utilizar o protocolo MQTT diretamente.
* [Glossário] [ devguide-glossary] uma lista dos termos comuns relacionadas com o IoT Hub.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
