---
title: "Serviço os conceitos do serviço de aprovisionamento de dispositivos do Azure IoT Hub | Microsoft Docs"
description: "Descreve conceitos aprovisionamento do serviço específicos para dispositivos com DPS e o IoT Hub"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 10/03/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 96c63e5d0379150ea619dbbe912a21e373f808af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceitos do serviço de aprovisionamento de dispositivos IoT Hub

Aprovisionamento de serviço do dispositivos do Hub IoT é um serviço de programa auxiliar do IoT Hub que utiliza para configurar o dispositivo de zero touch aprovisionamento para um hub IoT especificado. Com o serviço de aprovisionamento de dispositivos, pode aprovisionar milhões de dispositivos de forma segura e escalável.

Aprovisionamento de dispositivos é um processo de dois parte. A primeira parte consiste em estabelecer a ligação inicial entre o dispositivo e a solução de IoT por *registar* o dispositivo. A segunda parte estiver a aplicar o adequado *configuração* para o dispositivo com base nos requisitos específicos da solução. Assim que os dois passos foram concluídos, pode indicar que o dispositivo foi totalmente *aprovisionado*. Serviço de aprovisionamento de dispositivos automatiza os dois passos para fornecer uma experiência totalmente integrada de aprovisionamento para o dispositivo.

Este artigo fornece uma descrição geral dos conceitos aprovisionamento mais aplicáveis para gerir o *serviço*. Este artigo é mais relevante para pessoas fictícias envolvidas no [passo de configuração de nuvem](about-iot-dps.md#cloud-setup-step) de preparar para a implementação de um dispositivo.

## <a name="service-operations-endpoint"></a>Ponto final de operações de serviço

O ponto final de operações de serviço é o ponto final para gerir as definições de serviço e manter a lista de inscrição. Este ponto final só é utilizada pelo administrador do serviço; Não é utilizado pelos dispositivos.

## <a name="device-provisioning-endpoint"></a>Ponto final de aprovisionamento de dispositivos

O dispositivo de ponto final de aprovisionamento é o ponto de final central comunicar com todos os dispositivos para o aprovisionamento. O URL é igual para todos os serviços de aprovisionamento eliminar a necessidade de reflash dispositivos com novas informações de ligação em cenários de cadeia de fornecimento. O [âmbito ID](#id-scope) garante o isolamento de inquilino.

## <a name="linked-iot-hubs"></a>Ligado aos hubs IoT

Serviço de aprovisionamento de dispositivos apenas pode aprovisionar dispositivos para os hubs IoT que foram associados ao mesmo. Ligar um IoT hub para o serviço de aprovisionamento de dispositivos fornece as permissões de leitura/escrita do serviço para o registo do IoT hub dispositivo; com a ligação, o serviço de aprovisionamento de dispositivos pode registar um ID de dispositivo e definir a configuração inicial no dispositivo duplo. Podem ser ligados aos hubs IoT em qualquer região do Azure. Pode ligar os hubs de outras subscrições ao seu serviço de aprovisionamento.

## <a name="allocation-policy"></a>Política de alocação

O nível de serviço, a definição que determina a forma como o serviço de aprovisionamento de dispositivos atribui dispositivos a um IoT hub. Existem três políticas de alocação suportados:
* **Uniformemente ponderado distribuição**: ligados aos hubs IoT são igualmente ter, provavelmente, os dispositivos aprovisionados aos mesmos. A predefinição. Se estiver a aprovisionar dispositivos ao IoT hub apenas uma, pode manter esta definição.
* **Latência mais baixa**: dispositivos são aprovisionados para um hub IoT com a latência mais baixa para o dispositivo. Se vários hubs IoT ligados iriam fornecer a mesma latência mais baixa, o serviço de aprovisionamento codifica dispositivos desses hubs
* **Configuração estático através da lista de inscrição**: especificação do hub IoT pretendida na lista de inscrição tem prioridade sobre a política de alocação de nível de serviço.

## <a name="enrollment"></a>Inscrição

Uma inscrição é o registo de dispositivos ou grupos de dispositivos que podem para alguns dos pontos de registo. O registo de inscrição contém as informações sobre o dispositivo ou grupo de dispositivos, incluindo o método de atestado para os dispositivos e, opcionalmente, inicial configuração pretendida, assim o desejar IoT hub e o dispositivo pretendidos ID. Existem dois tipos de inscrições suportados pelo serviço de aprovisionamento de dispositivos.

### <a name="enrollment-group"></a>Grupo de inscrição

Um grupo de inscrição é um grupo de dispositivos que partilham um mecanismo de atestado específico. Todos os dispositivos no grupo de inscrição apresentam os certificados x. 509 que tenham sido iniciados pela mesma raiz AC. Grupos de inscrição só podem utilizar o mecanismo de atestado de x. 509. O nome do grupo de inscrição e o nome do certificado tem de ser alfanumérico, minúsculas e podem conter hífenes.

> [!TIP]
> Recomendamos que utilize um grupo de inscrição para um grande número de dispositivos que partilham uma configuração desejada inicial ou para dispositivos que todos os mesmo inquilino.

### <a name="individual-enrollment"></a>Inscrição individuais

Uma inscrição individuais é uma entrada para um único dispositivo pode registar. As inscrições individuais podem utilizar certificados x. 509 ou tokens SAS (por um TPM real ou virtual) como mecanismos de atestado. O ID de registo de inscrição individuais é alfanuméricos, minúsculas e pode conter hífenes. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

> [!TIP]
> Recomendamos que utilize inscrições individuais para os dispositivos que requerem configurações iniciais exclusivas ou para dispositivos que só podem utilizar os tokens SAS através do TPM ou virtual TPM como o mecanismo de atestado.

## <a name="registration"></a>Registo

Um registo é o registo de um dispositivo com êxito ao registar/aprovisionamento para um IoT Hub através do serviço de aprovisionamento de dispositivos. Registos de registo são criados automaticamente; também podem ser eliminadas, mas não podem ser atualizadas.

## <a name="operations"></a>Operações

As operações são a unidade de faturação do serviço de aprovisionamento de dispositivos. Uma operação é a conclusão com êxito de uma instrução para o serviço. As operações incluem o registo de dispositivos - e novos registos -, bem como alterações do lado do serviço, como adicionar e atualizar entradas de listas de inscrição.
