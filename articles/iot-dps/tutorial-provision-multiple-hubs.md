---
title: "Os hubs IoT com balanceamento de utilização do Azure IoT Hub aprovisionamento serviço dispositivos para aprovisionar dispositivos em carga | Microsoft Docs"
description: "Aprovisionamento de dispositivos automático DPS em carga balanceamento os hubs IoT no Portal do Azure"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Aprovisionar dispositivos entre os hubs IoT do balanceamento de carga

Este tutorial mostra como aprovisionar dispositivos para vários hubs IoT com balanceamento de carga com o serviço de aprovisionamento de dispositivos (DPS). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para aprovisionar um segundo dispositivo a um IoT hub segundo 
> * Adicione uma entrada da lista de inscrição para o segundo dispositivo
> * Defina a política de alocação de DPS para **mesmo de distribuição**
> * Ligue o novo IoT hub para DPS

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial baseia-se anterior [dispositivo aprovisionar a um concentrador](tutorial-provision-device-to-hub.md) tutorial.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Utilizar o portal do Azure para aprovisionar um segundo dispositivo a um IoT hub segundo

Siga os passos a [dispositivo aprovisionar a um concentrador](tutorial-provision-device-to-hub.md) tutorial para aprovisionar um segundo dispositivo ao IoT hub outro.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Adicione uma entrada da lista de inscrição para o segundo dispositivo

A lista de inscrição informa o DPS qual o método de atestado (o método para confirmar a identidade de dispositivo) está a utilizar com o dispositivo. O passo seguinte consiste em Adicionar uma entrada da lista de inscrição para o dispositivo segundo. 

1. Na página do seu DPS, clique em **gerir inscrições**. O **adicionar a entrada da lista de inscrição** é apresentada a página. 
2. Na parte superior da página, clique em **adicionar**.
2. Preencha os campos e, em seguida, clique em **guardar**.

## <a name="set-the-dps-allocation-policy"></a>Definir a política de alocação de DPS

A política de alocação é uma definição de DPS que determina a forma como os dispositivos são atribuídos a um IoT hub. Existem três políticas de alocação suportados: 

1. **Latência mais baixa**: dispositivos são aprovisionados para um hub IoT com base no hub com a latência mais baixa para o dispositivo.
2. **Uniformemente ponderado distribuição** (predefinição): os hubs IoT ligado são igualmente ter, provavelmente, os dispositivos aprovisionados aos mesmos. Esta é a predefinição. Se estiver a aprovisionar dispositivos ao IoT hub apenas uma, pode manter esta definição. 
3. **Configuração estático através da lista de inscrição**: especificação do hub IoT pretendida na lista de inscrição tem prioridade sobre a política de alocação de nível de DPS.

Siga estes passos para definir a política de alocação:

1. Para definir a política de alocação, na página de DPS clique **gerir a política de alocação**.
2. Defina a política de alocação para **uniformemente ponderado distribuição**.
3. Clique em **Guardar**.

## <a name="link-the-new-iot-hub-to-dps"></a>Ligue o novo IoT hub para DPS

Ligue o DPS e IoT hub para que DPS podem registar dispositivos a esse concentrador.

1. No **todos os recursos** página, clique em DPS que criou anteriormente.
2. Na página de DPS, clique em **os hubs IoT ligado**.
3. Clique em **Adicionar**.
4. No **adicionar ligação ao IoT hub** página, utilize os botões de opção para especificar se o IoT hub ligado está localizado na subscrição atual, ou numa subscrição diferente. Em seguida, escolha o nome do hub IoT do **IoT hub** caixa.
5. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para aprovisionar um segundo dispositivo a um IoT hub segundo 
> * Adicione uma entrada da lista de inscrição para o segundo dispositivo
> * Defina a política de alocação de DPS para **mesmo de distribuição**
> * Ligue o novo IoT hub para DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
