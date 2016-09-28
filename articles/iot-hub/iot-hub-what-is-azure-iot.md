<properties
 pageTitle="Soluções Azure para Internet das Coisas | Microsoft Azure"
 description="Uma descrição geral do IoT no Azure, incluindo um exemplo de solução de arquitetura e a forma como se relaciona com o IoT Hub do Azure, SDKs do dispositivo e soluções pré-configuradas."
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
 ms.date="07/19/2016"
 ms.author="dobett"/>


[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Passos seguintes

O IoT Hub do Azure é um serviço do Azure que permite comunicações bidirecionais seguras e fiáveis entre a sua aplicação de back-end e milhões de dispositivos. Permite à aplicação de back-end receber telemetria à escala dos seus dispositivos, encaminhar esses dados para um processador de fluxo de eventos, receber carregamentos de ficheiros a partir de dispositivos e ainda enviar comandos da nuvem para o dispositivo para dispositivos específicos. Pode utilizar o IoT Hub para implementar a sua própria solução de back-end. Além disso, o IoT Hub inclui um registo de identidade do dispositivo utilizado para aprovisionar dispositivos, as suas credenciais de segurança e os seus direitos de ligação ao Hub. Para saber mais sobre o Hub IoT, veja [O que é o Hub IoT?][Ink-iot-hub].

Para saber como o Hub IoT do Azure permite a gestão de dispositivos de IoT baseada em normas para que possa gerir remotamente, configurar e atualizar os seus dispositivos, veja [Descrição geral da gestão de dispositivos do Hub IoT do Azure][lnk-device-management].

Para implementar aplicações de cliente em várias plataformas de hardware e sistemas operativos para dispositivos, pode utilizar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria a um IoT Hub e a receção de comandos da nuvem para o dispositivo. Quando utiliza os SDKs, pode escolher entre vários protocolos de rede para comunicar com o IoT Hub. Para saber mais, consulte [informações sobre os SDKs do dispositivo][Ink-dispositivo-sdks].

Para começar a escrever código e executar alguns exemplos, veja o tutorial [Introdução ao Hub IoT][Ink-getstarted].

Também poderá estar interessado na [Azure IoT Suite][Ink-iot-suite], que é uma coleção de soluções pré-configuradas. A IoT Suite oferece-lhe uma introdução rápida e um dimensionamento de projetos IoT para abordar cenários comuns do IoT - tais como a monitorização remota, a gestão de elementos e a manutenção preditiva.

[Ink-getstarted]: iot-hub-csharp-csharp-getstarted.md
[Ink-dispositivo-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[Ink-iot-hub]: iot-hub-what-is-iot-hub.md
[Ink-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md


<!--HONumber=Sep16_HO3-->


