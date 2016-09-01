<properties
    pageTitle="Descrição geral do Microsoft Azure IoT Suite | Microsoft Azure"
    description="Descrição geral de como o Azure IoT Suite fornece soluções pré-configuradas de Internet das Coisas para recolher, analisar e armazenar dados, fornecer visualizações e integrá-las noutros sistemas."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# O que é o Azure IoT Suite?

Os serviços Internet das Coisas (IoT) do Azure oferecem uma vasta gama de capacidades. Estes serviços de nível empresarial permitem-lhe:

- Recolher dados de dispositivos
- Analisar fluxos de dados em movimento
- Armazenar e consultar grandes conjuntos de dados
- Visualizar dados em tempo real e dados históricos
- Integrar com sistemas de back-office

Para fornecer estas capacidades, o Azure IoT Suite reúne vários serviços do Azure com extensões personalizadas, como as *soluções pré-configuradas*. Estas soluções pré-configuradas são implementações de base de padrões comuns da solução IoT que ajudam a reduzir o tempo necessário para fornecer as suas soluções de IoT. Utilize os [kits de desenvolvimento de software IoT][lnk-sdks] para personalizar e expandir essas soluções para satisfazer os seus próprios requisitos. Também pode utilizar estas soluções como exemplos ou modelos quando estiver a desenvolver novas soluções de IoT.

O vídeo que se segue fornece uma introdução ao Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Serviços do Azure IoT no Azure IoT Suite

Geralmente, as soluções pré-configuradas utilizam os seguintes serviços:

- Núcleo para o Azure IoT Suite é o serviço do [Azure IoT Hub][lnk-iot-hub]. Este serviço fornece funcionalidades de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo e funciona como gateway para a nuvem e outros serviços essenciais do IoT Suite. O serviço permite-lhe receber mensagens dos seus dispositivos de escala e enviar comandos aos seus dispositivos.

- O [Azure Stream Analytics][lnk-asa] fornece uma análise de dados em movimento. O IoT Suite tira partido deste serviço para processar a telemetria de entrada, executar a agregação e detetar eventos. As soluções pré-configuradas utilizam ainda o Stream Analytics para processar mensagens de informações que contêm dados como metadados ou respostas aos comandos dos dispositivos. As soluções utilizam o Stream Analytics para processar as mensagens dos seus dispositivos e enviá-las a outros serviços.

- O [Storage do Azure][lnk-azure-storage] e o [DocumentDB do Azure][lnk-document-db] fornecem as funcionalidades de armazenamento de dados. As soluções pré-configuradas utilizam o Blob storage para armazenar a telemetria e disponibilizá-la para análise. As soluções utilizam o DocumentDB para armazenar metadados do dispositivo e ativar as funcionalidades de gestão do dispositivo das soluções.

- As [ Web Apps do Azure][lnk-web-apps] e o [Microsoft Power BI][lnk-power-bi] fornecem as funcionalidades de visualização de dados. A flexibilidade do Power BI permite-lhe criar rapidamente os seus próprios dashboards interativos que utilizam os dados do IoT Suite.

Para uma descrição geral da arquitetura de uma solução IoT típica, consulte o artigo [Microsoft Azure e a Internet das coisas (IoT)][iot-suite-what-is-azure-iot].

## Soluções pré-configuradas

O IoT Suite inclui soluções pré-configuradas que lhe permitem começar rapidamente e explorar os cenários comuns do IoT, tais como a *Monitorização remota* e a *Manutenção preditiva*, que o Azure IoT Suite torna possível. Pode implementar estas soluções à sua subscrição do Azure e, em seguida, executar um cenário ponto-a-ponto completo do IoT.

## Passos seguintes

Agora que tem uma ideia geral sobre o que o IoT Suite pode fazer e quais são os seus principais componentes, pode obter mais informações sobre as soluções pré-configuradas no IoT Suite. Para tal, veja [Quais são as soluções pré-configuradas do Azure IoT?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md



<!--HONumber=ago16_HO4-->


