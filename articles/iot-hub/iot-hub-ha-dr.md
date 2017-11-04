---
title: "Azure IoT Hub elevada disponibilidade e recuperação após desastre | Microsoft Docs"
description: "Descreve as funcionalidades do Azure e o IoT Hub que ajudam a criar soluções de IoT do Azure elevadas com desastre funcionalidades de recuperação."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 3ea10ee8652dc2a03791feb66041431e7b3c6ae1
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub elevada disponibilidade e recuperação após desastre
Como um serviço do Azure, o IoT Hub fornece elevada disponibilidade (HA) utilizando redundancies ao nível da região do Azure, sem qualquer trabalho adicional necessário para a solução. A plataforma Microsoft Azure também inclui as funcionalidades para o ajudar a criar soluções com as capacidades do após desastre (DR) de recuperação ou a disponibilidade de por várias regiões. Se pretender fornecer global, por várias regiões elevada disponibilidade para os dispositivos ou utilizadores, tirar partido destas funcionalidades de DR do Azure. O artigo [Azure continuidade de negócio orientações técnica](../resiliency/resiliency-technical-guidance.md) descreve as funcionalidades integradas no Azure para a continuidade do negócio e DR. O [recuperação após desastre e elevada disponibilidade para aplicações do Azure] [ Disaster recovery and high availability for Azure applications] documento fornece orientações de arquitetura no estratégias para aplicações do Azure alcançar HA e DR.

## <a name="azure-iot-hub-dr"></a>DR do IoT Hub do Azure
Para além do HA intra região, o IoT Hub implementa mecanismos de ativação pós-falha para recuperação após desastre, que não requerem nenhuma intervenção do utilizador. IoT Hub DR automática é iniciada e tem um objetivo de tempo de recuperação (RTO) de 2 26 horas e os seguintes objetivos de ponto de recuperação (RPOs):

| Funcionalidade | RPO |
| --- | --- |
| Disponibilidade do serviço para operações de registo e comunicação |Possíveis perdas de CName |
| Dados de identidade no registo de identidade |0 a 5 minutos perda de dados |
| Mensagens do dispositivo para a cloud |Todas as mensagens unread são perdidas |
| Operações de monitorização de mensagens |Todas as mensagens unread são perdidas |
| Mensagens da nuvem para dispositivo |0 a 5 minutos perda de dados |
| Fila de comentários da nuvem para o dispositivo |Todas as mensagens unread são perdidas |

## <a name="regional-failover-with-iot-hub"></a>Ativação pós-falha com o IoT Hub
Um tratamento completado de topologias de implementação em soluções de IoT está fora do âmbito deste artigo. O artigo aborda o *ativação pós-falha regional* modelo de implementação para fins de elevada disponibilidade e recuperação após desastre.

No modelo de ativação pós-falha, a solução de cópia de end execução principalmente numa única localização de centro de dados. Uma secundário do IoT hub e o back-end são implementadas noutra localização do Centro de dados. Se o hub IoT do datacenter primário sofrerá de uma interrupção ou a conetividade de rede do dispositivo para o datacenter primário é interrompida, os dispositivos utilizam um ponto final de serviço secundário. Pode melhorar a disponibilidade da solução através da implementação de um modelo de ativação pós-falha por várias regiões, em vez de permanecendo numa única região. 

Um nível elevado, para implementar um modelo de ativação pós-falha com o IoT Hub, precisa do seguinte:

* **Um IoT hub e dispositivos de encaminhamento lógica secundário**: se o serviço na sua região primária é interrompido, dispositivos tem de iniciar a ligação para a região secundária. Tendo em conta a natureza com suporte para o estado da maioria dos serviços de envolvidas, é comum de solução de administradores para acionar o processo de ativação pós-falha de região entre. A melhor forma para comunicar o novo ponto final para dispositivos, mantendo o controlo do processo, é para os verificar regularmente uma *concierge* serviço para o ponto final do Active Directory atual. O serviço de concierge pode ser uma aplicação web que é replicada e mantida acessível através de técnicas de redirecionamento de DNS (por exemplo, utilizando [Traffic Manager do Azure][Azure Traffic Manager]).
* **Replicação de registo de identidade**: seja utilizável, o IoT hub secundário tem de conter todas as identidades de dispositivo que possam ligar à solução. A solução deve manter georreplicação cópias de segurança das identidades de dispositivo e carregá-los para o IoT hub secundário antes de mudar o ponto final do Active Directory para os dispositivos. A funcionalidade de exportação de identidade de dispositivo do IoT Hub é útil neste contexto. Para obter mais informações, consulte [guia para programadores do IoT Hub - registo de identidade][IoT Hub developer guide - identity registry].
* **A intercalação lógica**: quando a região primária fica novamente disponível, todos o estado e os dados que foram criados no site secundário tem de ser migrados de volta para a região primária. Este estado e os dados principalmente relacionadas com as identidades de dispositivo e metadados de aplicações, que têm de ser intercalados com o IoT hub primário e outros arquivos de específicas da aplicação na região primária. Para simplificar este passo, deverá utilizar idempotent operações. Operações Idempotent minimizar os efeitos de lado de uma eventual de distribuição consistente de eventos e de duplicados ou fora de ordem entrega de eventos. Além disso, a lógica de aplicação deve ser concebida para tolerar possíveis inconsistências ou o estado "ligeiramente" desatualizado. Esta situação pode ocorrer devido à mais tempo demora para o sistema a "heal" com base nos objetivos de ponto de recuperação (RPO).

## <a name="next-steps"></a>Passos seguintes
Siga estas ligações para saber mais sobre o IoT Hub do Azure:

* [Introdução aos Hubs IoT (Tutorial)][lnk-get-started]
* [O que é o IoT Hub do Azure?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
