<properties
 pageTitle="Descrição geral do IoT Hub do Azure | Microsoft Azure"
 description="Descrição geral do serviço de IoT Hub do Azure: o que a conectividade do dispositivo IoT Hub, os padrões de comunicação da Internet das Coisas e o padrão de comunicação auxiliado"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>


# O que é o IoT Hub do Azure?

Bem-vindo ao IoT Hub do Azure. Este artigo fornece uma descrição geral do IoT Hub do Azure e descreve porque deveria utilizar este serviço aquando da implementação da uma solução Internet das Coisas (IoT).

O IoT Hub do Azure é um serviço completamente gerido que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e uma solução de back-end. IoT Hub do Azure:

- Oferece um serviço de mensagens fiável do dispositivo para a nuvem e da nuvem para o dispositivo.
- Tal permite realizar comunicações seguras utilizando credenciais de segurança por dispositivo e controlo de acesso.
- Fornece monitorização exaustiva da conectividade do dispositivo e dos eventos de gestão de identidade do dispositivo.
- Inclui bibliotecas de dispositivo para os idiomas e plataformas mais populares.

O artigo [Comparação do IoT Hub e do Event Hubs][Ink-compare] descreve as principais diferenças entre esses dois serviços e realça as vantagens de utilizar o IoT Hub nas suas soluções de IoT.

![O IoT Hub do Azure como um gateway em nuvem da solução Internet das Coisas][img-architecture]

> [AZURE.NOTE] Para um debate aprofundado da arquitetura do IoT, consulte [Arquitetura de Referência do IoT do Microsoft Azure][Ink refarch].

## Desafios de conectividade do dispositivo IoT

O IoT Hub e as bibliotecas do dispositivo ajudam-no a aceitar o desafio de estabelecer, de forma fiável e segura, a ligação dos dispositivos à solução de back-end. Dispositivos IoT:

- Trata-se frequentemente se sistemas integrados sem nenhum operador humano.
- Podem estar em localizações remotas, onde o acesso físico é dispendioso.
- Podem apenas ser acessíveis através da solução de back-end.
- Podem recursos de processamento e um poder limitados.
- Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.
- Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.
- Podem ser criados utilizando um grande conjunto de plataformas de hardware e software populares.

Além dos requisitos identificados acima, qualquer solução IoT também deverá oferecer um dimensionamento, segurança e fiabilidade. É difícil e moroso implementar o conjunto resultante dos requisitos de conectividade se utilizar tecnologias tradicionais, como contentores Web e mediadores de mensagens.

## Porque utilizar o IoT Hub do Azure?

O IoT Hub do Azure aborda os desafios relativos à conectividade do dispositivo da seguinte forma:

-   **Através da autenticação do dispositivo e da conectividade segura**. Pode aprovisionar cada dispositivo com a sua própria [chave de segurança][Ink-devguide-security] de modo a ativá-la para estabelecer a ligação ao IoT Hub. O [registo de identidade do IoT Hub][Ink-devguide-identityregistry] armazena identidades e chaves do dispositivo numa solução. Um back-end da solução pode adicionar dispositivos individuais para permitir ou recusar listas, possibilitando o controlo total do acesso dos dispositivos.

-   **Monitorização de operações de conectividade do dispositivo**. Pode receber registos detalhados de operações sobre a gestão de identidade do dispositivo e eventos de conectividade do dispositivo. Esta funcionalidade de monitorização permite à sua solução de IoT detetar problemas de conectividade, como dispositivos que se tentam ligar com credenciais incorretas, enviar mensagens com uma frequência excessiva ou rejeitar todas as mensagens da nuvem para o dispositivo.

-   **Um vasto conjunto de bibliotecas do dispositivo**. Os [SDKs do Azure IoT Device][Ink-dispositivo-sdks] estão disponíveis e suportam vários idiomas e plataformas--C para várias distribuições em Linux e Windows e sistemas operativos em tempo real. Os SDKs do Azure IoT Device suportam ainda idiomas geridos, como o C#, Java e JavaScript.

-   **Protocolos e extensibilidade do IoT**. Se a solução não conseguir utilizar as bibliotecas do dispositivo, o IoT Hub expõe um protocolo público que permite aos dispositivos utilizarem de forma nativa os protocolos MQTT v3.1.1, HTTP 1.1 ou AMQP 1.0.  Pode ainda expandir o seu IoT Hub para que suporte protocolos personalizados ao:

    - Criar um gateway de campo com o [SDK do Gateway do IoT do Azure][lnk-gateway-sdk] que converte o seu protocolo personalizado num dos três protocolos abrangidos pelo IoT Hub. 
    - Personalizar o [gateway de protocolo do Azure IoT][protocol-gateway], um componente de open source executado em nuvem.

-   **Dimensionamento**. O IoT Hub do Azure IoT dimensiona milhões de dispositivos ligados em simultâneo e milhões de eventos por segundo.

Estas vantagens são genéricas a muitos padrões de comunicação. Atualmente, o IoT Hub permite-lhe implementar os seguintes padrões de comunicação específicos:

-   **Ingestão dispositivo para a nuvem com base no evento.** O IoT Hub pode receber, de forma fiável, milhões de eventos por segundo dos seus dispositivos. De seguida, pode processá-los no seu caminho instantâneo, utilizando um motor de processador de eventos. Pode ainda armazená-los no seu caminho típico para serem analisados. O IoT Hub retém os dados do evento até sete dias para garantir um processamento fiável e absorver picos de carga.

-   **Mensagens fiáveis da nuvem para o dispositivo (ou *comandos*).** A solução de back-end pode utilizar o IoT Hub para enviar mensagens com a garantia de pelo menos uma entrega a dispositivos individuais. Cada mensagem tem uma definição de tempo de vida individual e o back-end pode pedir relatórios de entrega e de expiração. Estes relatórios garantem a visibilidade total do ciclo de vida de uma mensagem da nuvem para o dispositivo. De seguida, pode implementar a lógica de negócio que inclui operações executadas nos dispositivos.

-   **Carregar ficheiros e dados de sensor em cache para a nuvem.** Os seus dispositivos podem carregar ficheiros para o Storage do Azure utilizando URIs SAS geridos pelo IoT Hub. O IoT Hub pode gerar notificações quando os ficheiros entrarem na nuvem, permitindo deste modo que sejam processados pela solução de back-end.

## Gateways

Normalmente, um gateway numa solução de IoT é um [gateway de protocolo][Ink-gateway] implementado na nuvem ou um [gateway de campo][gateway-field-gateway] implementado localmente nos seus dispositivos. Um gateway de protocolo traduz o protocolo, por exemplo de MQTT para AMQP. Um gateway de campo pode executar a análise na extremidade, tomar decisões urgentes para reduzir a latência, fornecer serviços de gestão de dispositivos, impor a segurança e restrições de privacidade e ainda proceder à tradução do protocolo. Ambos os tipos de gateway agem como intermediários entre os dispositivos e o seu IoT Hub.

Um gateway de campo difere de um dispositivo de encaminhamento de tráfego simples (como um dispositivo de tradução de endereços de rede ou firewall), porque desempenha normalmente uma função ativa na gestão do acesso e do fluxo de informações da sua solução.

Uma solução pode incluir os gateways de protocolo e de campo.

## Como funciona o IoT Hub?

O IoT Hub do Azure implementa o padrão de comunicação [auxiliado][lnk-service-assisted-pattern] para mediar as interações entre os seus dispositivos e a sua solução de back-end. O objetivo da comunicação auxiliada consiste em estabelecer percursos de comunicação fidedignos e bidirecionais entre um sistema de controlo, como o IoT Hub e dispositivos com objetivos especiais implementados num espaço físico não fidedigno. O padrão estabelece os seguintes princípios:

- A segurança tem precedência sobre todas as outras funcionalidades.
- Os dispositivos não aceitam informações da rede não solicitadas. Um dispositivo estabelece todas as ligações e rotas de uma forma apenas de saída. Para que um dispositivo receba um comando do back-end, deverá frequentemente iniciar uma ligação para verificar se devem ser processados quaisquer comandos pendentes.
- Os dispositivos apenas devem estabelecer a ligação ou rotas a serviços bem conhecidos com os quais estão emparelhados, como o IoT Hub.
- O caminho de comunicação entre o dispositivo e o serviço ou entre o dispositivo e o gateway é protegido na camada de protocolo da aplicação.
- A autorização e autenticação ao nível do sistema assentam em identidades por dispositivo. As credenciais e permissões de acesso são quase instantaneamente revogáveis.
- A comunicação bidirecional para dispositivos que ligam esporadicamente devido a problemas de alimentação é facilitada ao reter comandos e notificações do dispositivo até que um dispositivo estabeleça a ligação para recebê-las. O Hub IoT mantém filas específicas de dispositivo para os comandos que envia.
- Dados payload da aplicação são protegidos separadamente para o trânsito protegido através de gateways para um serviço particular.

A indústria móvel utilizou o padrão de comunicação auxiliada a uma grande escala para implementar os serviços de notificação push, como os [Serviços de Notificação Push do Windows][Ink-wns], o [Google Cloud Messaging][Ink-google-messaging] e o [Serviço Apple Push Notification][lnk-apple-push].

## Passos seguintes

Para saber como o Hub IoT do Azure permite a gestão de dispositivos de IoT baseada em normas para que possa gerir remotamente, configurar e atualizar os seus dispositivos, veja [Descrição geral da gestão de dispositivos do Hub IoT do Azure][lnk-device-management].

Para implementar aplicações de cliente em várias plataformas de hardware e sistemas operativos para dispositivos, pode utilizar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria a um IoT Hub e a receção de comandos da nuvem para o dispositivo. Quando utiliza os SDKs, pode escolher entre vários protocolos de rede para comunicar com o Hub IoT. Para saber mais, consulte [informações sobre os SDKs do dispositivo][Ink-dispositivo-sdks].

Para começar a escrever código e executar alguns exemplos, veja o tutorial [Introdução ao Hub IoT][Ink get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[Ink get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-d2c]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicação auxiliada, mensagem de blogue por Clemens Vasters"
[Ink-compare]: iot-hub-compare-event-hubs.md
[Ink-gateway]: iot-hub-protocol-gateway.md
[gateway-field-gateway]: iot-hub-guidance.md#field-gateways
[Ink-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[Ink-devguide-security]: iot-hub-devguide.md#security
[Ink-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[Ink-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[Ink-dispositivo-sdks]: https://github.com/Azure/azure-iot-sdks
[Ink refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Sep16_HO3-->


