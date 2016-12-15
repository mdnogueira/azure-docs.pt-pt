---
title: "Descrição geral do Hub IoT do Azure | Microsoft Docs"
description: "Descrição geral do serviço de Hub IoT do Azure: o que a conectividade do dispositivo Hub IoT, os padrões de comunicação da Internet das Coisas, os gateways e o padrão de comunicação auxiliado"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: 42774f83dc4c53dc8f090acb922b5aae6e8b4bed


---
# <a name="what-is-azure-iot-hub"></a>O que é o IoT Hub do Azure?
Bem-vindo ao IoT Hub do Azure. Este artigo fornece uma descrição geral do IoT Hub do Azure e descreve porque deve utilizar este serviço para implementar uma solução de Internet das Coisas (IoT). O IoT Hub do Azure é um serviço completamente gerido que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e uma solução de back-end. IoT Hub do Azure:

* Oferece várias opções de comunicação dispositivo para a cloud e cloud para o dispositivo, incluindo mensagens unilaterais, transferência de ficheiros e métodos de pedido-resposta.
* Disponibiliza um armazenamento consultável para metadados de dispositivos e informações de estado sincronizadas.
* Permite comunicações seguras e controlo de acesso através de chaves de segurança por dispositivo ou de certificados X.509.
* Fornece monitorização exaustiva da conectividade do dispositivo e dos eventos de gestão de identidade do dispositivo.
* Inclui bibliotecas de dispositivo para os idiomas e plataformas mais populares.

O artigo [Comparison of Hub IoT and Event Hubs (Comparação do Hub IoT e dos Hubs de Eventos)][lnk-compare] descreve as principais diferenças entre esses dois serviços e realça as vantagens de utilizar o HuB IoT nas suas soluções de IoT.

Veja [Internet of Things security from the ground up (Segurança da Internet das Coisas de cima a baixo)][lnk-security-ground-up] para obter mais informações sobre como o Azure e o Hub IoT ajudam a proteger a sua solução de IoT.

![O IoT Hub do Azure como um gateway em nuvem da solução Internet das Coisas][img-architecture]

> [!NOTE]
> Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture (Arquitetura de Referência do Microsoft Azure IoT)][lnk-refarch].
> 
> 

## <a name="iot-device-connectivity-challenges"></a>Desafios de conectividade do dispositivo IoT
O IoT Hub e as bibliotecas do dispositivo ajudam-no a aceitar o desafio de estabelecer, de forma fiável e segura, a ligação dos dispositivos à solução de back-end. Dispositivos IoT:

* Trata-se frequentemente se sistemas integrados sem nenhum operador humano.
* Podem estar em localizações remotas, onde o acesso físico é dispendioso.
* Podem apenas ser acessíveis através da solução de back-end.
* Podem recursos de processamento e um poder limitados.
* Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.
* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.
* Podem ser criados utilizando um grande conjunto de plataformas de hardware e software populares.

Além dos requisitos identificados acima, qualquer solução IoT também deverá oferecer um dimensionamento, segurança e fiabilidade. É difícil e moroso implementar o conjunto resultante dos requisitos de conectividade se utilizar tecnologias tradicionais, como contentores Web e mediadores de mensagens.

## <a name="why-use-azure-iot-hub"></a>Porque utilizar o IoT Hub do Azure?
Para além de um avançado conjunto de opções de comunicação [dispositivo para a cloud][lnk-d2c-guidance] e [cloud para o dispositivo][lnk-c2d-guidance], incluindo mensagens, transferência de ficheiros e métodos de pedido-resposta, o Hub do Azure IoT responde aos desafios de conectividade dos dispositivos das seguintes formas:

* **Dispositivos duplos**. Ao utilizar [Dispositivos duplos][lnk-twins], pode armazenar, sincronizar e consultar metadados e informações de estado de dispositivos. Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O Hub IoT cria um dispositivo duplo para cada dispositivo que ligar ao serviço. 
* **Através da autenticação do dispositivo e da conectividade segura**. Pode aprovisionar cada dispositivo com a respetiva [chave de segurança][lnk-devguide-security], para que aqueles possam ligar ao Hub IoT. O [registo de identidade do Hub IoT][lnk-devguide-identityregistry] armazena identidades e chaves dos dispositivos numa solução. Um back-end da solução pode adicionar dispositivos individuais para permitir ou recusar listas, possibilitando o controlo total do acesso dos dispositivos.
* **Monitorização de operações de conectividade do dispositivo**. Pode receber registos detalhados de operações sobre a gestão de identidade do dispositivo e eventos de conectividade do dispositivo. Esta funcionalidade de monitorização permite à sua solução de IoT detetar problemas de conectividade, como dispositivos que se tentam ligar com credenciais incorretas, enviar mensagens com uma frequência excessiva ou rejeitar todas as mensagens da nuvem para o dispositivo.
* **Um vasto conjunto de bibliotecas do dispositivo**. Os [Azure IoT Device SDKs][lnk-device-sdks] estão disponíveis e suportam vários idiomas e plataformas -- C para várias distribuições Linux, Windows e sistemas operativos em tempo real. Os SDKs do Azure IoT Device suportam ainda idiomas geridos, como o C#, Java e JavaScript.
* **Protocolos e extensibilidade do IoT**. Se a solução não conseguir utilizar as bibliotecas do dispositivo, o IoT Hub expõe um protocolo público que permite aos dispositivos utilizarem de forma nativa os protocolos MQTT v3.1.1, HTTP 1.1 ou AMQP 1.0.  Pode ainda expandir o seu IoT Hub para que suporte protocolos personalizados ao:
  
  * Criar um gateway de campo com o [SDK do Gateway do Azure IoT][lnk-gateway-sdk] que converte o seu protocolo personalizado num dos três protocolos abrangidos pelo Hub IoT. 
  * Personalizar o [gateway de protocolo do IoT do Azure][protocol-gateway], um componente de código aberto executado na cloud.
* **Dimensionamento**. O IoT Hub do Azure IoT dimensiona milhões de dispositivos ligados em simultâneo e milhões de eventos por segundo.

## <a name="gateways"></a>Gateways
Normalmente, um gateway numa solução de IoT é um [gateway de protocolo][lnk-gateway] implementado na cloud ou um [gateway de campo][lnk-field-gateway] implementado localmente nos seus dispositivos. Um gateway de protocolo traduz o protocolo, por exemplo de MQTT para AMQP. Um gateway de campo pode executar a análise na extremidade, tomar decisões urgentes para reduzir a latência, fornecer serviços de gestão de dispositivos, impor a segurança e restrições de privacidade e ainda proceder à tradução do protocolo. Ambos os tipos de gateway agem como intermediários entre os dispositivos e o seu IoT Hub.

Um gateway de campo difere de um dispositivo de encaminhamento de tráfego simples (como um dispositivo de tradução de endereços de rede ou firewall), porque desempenha normalmente uma função ativa na gestão do acesso e do fluxo de informações da sua solução.

Uma solução pode incluir os gateways de protocolo e de campo.

## <a name="how-does-iot-hub-work"></a>Como funciona o IoT Hub?
O Hub IoT do Azure implementa a norma de [comunicação auxiliada][lnk-service-assisted-pattern] para mediar as interações entre os seus dispositivos e a sua solução de back-end. O objetivo da comunicação auxiliada consiste em estabelecer percursos de comunicação fidedignos e bidirecionais entre um sistema de controlo, como o IoT Hub e dispositivos com objetivos especiais implementados num espaço físico não fidedigno. O padrão estabelece os seguintes princípios:

* A segurança tem precedência sobre todas as outras funcionalidades.
* Os dispositivos não aceitam informações da rede não solicitadas. Um dispositivo estabelece todas as ligações e rotas de uma forma apenas de saída. Para que um dispositivo receba um comando da solução de back-end, deverá frequentemente iniciar uma ligação para verificar se devem ser processados quaisquer comandos pendentes.
* Os dispositivos apenas devem estabelecer a ligação ou rotas a serviços bem conhecidos com os quais estão emparelhados, como o IoT Hub.
* O caminho de comunicação entre o dispositivo e o serviço ou entre o dispositivo e o gateway é protegido na camada de protocolo da aplicação.
* A autorização e autenticação ao nível do sistema assentam em identidades por dispositivo. As credenciais e permissões de acesso são quase instantaneamente revogáveis.
* A comunicação bidirecional para dispositivos que ligam esporadicamente devido a problemas de alimentação é facilitada ao reter comandos e notificações do dispositivo até que um dispositivo estabeleça a ligação para recebê-las. O Hub IoT mantém filas específicas de dispositivo para os comandos que envia.
* Dados payload da aplicação são protegidos separadamente para o trânsito protegido através de gateways para um serviço particular.

A indústria móvel utilizou a norma de comunicação auxiliada a uma grande escala para implementar os serviços de notificações push, como os [Serviços de Notificações Push do Windows][lnk-wns], o [Google Cloud Messaging][lnk-google-messaging] e o [Serviço Apple Push Notification][lnk-apple-push].

O Hub IoT é suportado ao longo do caminho de peering público do ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
Para saber como o Hub IoT do Azure permite a gestão de dispositivos baseada em normas para que possa gerir remotamente, configurar e atualizar os seus dispositivos, veja [Descrição geral da gestão de dispositivos com o Hub IoT][lnk-device-management].

Para implementar aplicações de cliente em várias plataformas de hardware e sistemas operativos para dispositivos, pode utilizar os Azure IoT Device SDKs. Os SDKs do dispositivo incluem bibliotecas que facilitam o envio de telemetria para um Hub IoT e a receção de mensagens da cloud para o dispositivo. Quando utiliza os Device SDKs, pode escolher entre vários protocolos de rede para comunicar com o Hub IoT. Para saber mais, veja as [informações sobre os Device SDKs][lnk-device-sdks].

Para começar a escrever código e executar alguns exemplos, veja o tutorial [Introdução ao Hub IoT][lnk-get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Service Assisted Communication (Comunicação Auxiliada, mensagem de blogue de Clemens Vasters)"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md


<!--HONumber=Dec16_HO1-->


