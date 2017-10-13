---
title: "Descrição geral do Microsoft Azure IoT Suite | Microsoft Docs"
description: "Descrição geral de como o Azure IoT Suite fornece soluções pré-configuradas de Internet das Coisas para recolher, analisar e armazenar dados, fornecer visualizações e integrá-las noutros sistemas."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfa8dbbd0b1d943a9eb7a042df0bac25189d9ac9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-iot-suite"></a>Descrição Geral do Azure IoT Suite

Os serviços Internet das Coisas (IoT) do Azure oferecem uma vasta gama de capacidades. Estes serviços de nível empresarial permitem-lhe:

* Recolher dados de dispositivos
* Analisar fluxos de dados em movimento
* Armazenar e consultar grandes conjuntos de dados
* Visualizar dados em tempo real e dados históricos
* Integrar com sistemas de back-office
* Faça a gestão dos seus dispositivos

Para fornecer estas capacidades, o Azure IoT Suite reúne vários serviços do Azure com extensões personalizadas, como as *soluções pré-configuradas*. Estas soluções pré-configuradas são implementações de base de padrões comuns da solução IoT que ajudam a reduzir o tempo necessário para fornecer as suas soluções de IoT. Utilize os [kits de desenvolvimento de software IoT][lnk-sdks] para personalizar e expandir essas soluções para satisfazer os seus próprios requisitos. Também pode utilizar estas soluções como exemplos ou modelos quando estiver a desenvolver novas soluções de IoT.

O vídeo que se segue fornece uma introdução ao Azure IoT Suite:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Serviços do Azure IoT no Azure IoT Suite
Geralmente, as soluções pré-configuradas utilizam os seguintes serviços:

* Núcleo para o Azure IoT Suite é o serviço do [Azure IoT Hub][lnk-iot-hub]. Este serviço fornece funcionalidades de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo e funciona como gateway para a nuvem e outros serviços essenciais do IoT Suite. O serviço permite-lhe receber mensagens dos seus dispositivos de escala e enviar comandos aos seus dispositivos. O serviço também lhe permite [gerir os seus dispositivos][lnk-device-management]. Por exemplo, pode configurar, reiniciar ou efetuar uma reposição de fábrica num ou mais dispositivos ligados ao hub.
* O [Azure Stream Analytics][lnk-asa] fornece uma análise de dados em movimento. O IoT Suite utiliza este serviço para processar a telemetria de entrada, executar a agregação e detetar eventos. As soluções pré-configuradas utilizam ainda o Stream Analytics para processar mensagens de informações que contêm dados como metadados ou respostas aos comandos dos dispositivos. As soluções utilizam o Stream Analytics para processar as mensagens dos seus dispositivos e enviá-las a outros serviços.
* O [Armazenamento do Azure][lnk-azure-storage] e o [Azure Cosmos DB][lnk-document-db] oferecem as funcionalidades de armazenamento de dados. As soluções pré-configuradas utilizam o Blob storage para armazenar a telemetria e disponibilizá-la para análise. As soluções utilizam o Cosmos DB para armazenar metadados do dispositivo e ativar as funcionalidades de gestão do dispositivo das soluções.
* As [ Web Apps do Azure][lnk-web-apps] e o [Microsoft Power BI][lnk-power-bi] fornecem as funcionalidades de visualização de dados. A flexibilidade do Power BI permite-lhe criar rapidamente os seus próprios dashboards interativos que utilizam os dados do IoT Suite.

Para uma descrição geral da arquitetura de uma solução IoT típica, consulte o artigo [Microsoft Azure e a Internet das coisas (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Soluções pré-configuradas

O IoT Suite inclui soluções pré-configuradas que lhe permitem começar rapidamente e explorar os cenários comuns do IoT, tais como:

* Monitorização remota
* Manutenção preditiva
* Fábrica ligada

Pode implementar estas soluções à sua subscrição do Azure e, em seguida, executar um cenário ponto-a-ponto completo do IoT.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia geral sobre o que o IoT Suite pode fazer e quais são os seus principais componentes, pode obter mais informações sobre as soluções pré-configuradas no IoT Suite. Para mais informações, veja [O que são as soluções pré-configuradas do Azure IoT?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
