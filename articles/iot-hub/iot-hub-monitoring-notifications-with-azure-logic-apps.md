---
title: "Monitorização remota do IoT e notificações com Azure Logic Apps | Microsoft Docs"
description: "Utilizar o Azure Logic Apps para a monitorização de temperatura IoT no seu IoT hub e enviar automaticamente notificações por e-mail para a caixa de correio para qualquer anomalias detetado."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IOT monitorização, as notificações do iot, a monitorização de temperatura iot"
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 7a611912ae55eb22103539dbba9f1a06aaa543b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitorização remota do IoT e notificações com Azure Logic Apps ligar o seu IoT hub e a caixa de correio

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Aplicações lógicas do Azure fornece uma forma de automatizar os processos como uma série de passos. Pode ligar uma aplicação lógica em vários serviços e protocolos. Começa com um acionador, tais como ' Quando uma conta é adicionada' e seguido por uma combinação de ações, como 'enviar uma notificação push'. Esta funcionalidade torna as Logic Apps uma solução de IoT perfeita para IoT monitorização, tais como permanecendo alerta para deteção de anomalias, entre outros cenários de utilização.

## <a name="what-you-learn"></a>O que irá aprender

Saiba como criar uma aplicação lógica que liga o seu IoT hub e a caixa de correio para monitorização de temperatura e notificações. Quando a temperatura for superior a 30 C, a aplicação cliente marca `temperatureAlert = "true"` na mensagem envia ao seu IoT hub. A mensagem aciona a aplicação lógica para enviar uma notificação por e-mail.

## <a name="what-you-do"></a>O que fazer

* Criar um espaço de nomes de barramento de serviço e adicionar-lhe uma fila.
* Adicione um ponto final e uma regra de encaminhamento ao seu IoT hub.
* Criar, configurar e testar uma aplicação lógica.

## <a name="what-you-need"></a>Do que precisa

* Tutorial [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluir que inclui os seguintes requisitos:
  * Uma subscrição ativa do Azure.
  * Um hub IoT do Azure na sua subscrição.
  * Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Criar o espaço de nomes do service bus e adicionar-lhe uma fila

### <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes de barramento de serviço

1. No [portal do Azure](https://portal.azure.com/), clique em **novo** > **integração empresarial com** > **Service Bus**.
1. Forneça as seguintes informações:

   **Nome**: O nome do service bus.

   **Escalão de preço**: clique em **básico** > **selecione**. O escalão básico é suficiente para este tutorial.

   **Grupo de recursos**: utilizar o mesmo grupo de recursos que utiliza o seu IoT hub.

   **Localização**: utilizar a mesma localização que utiliza o seu IoT hub.
1. Clique em **Criar**.

   ![Criar um espaço de nomes de barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Adicionar uma fila de barramento de serviço

1. Abra o espaço de nomes de barramento de serviço e, em seguida, clique em **+ fila**.
1. Introduza um nome para a fila e, em seguida, clique em **criar**.
1. Abrir a fila de barramento de serviço e, em seguida, clique em **políticas de acesso partilhado** > **+ adicionar**.
1. Introduza um nome para a política, a verificação **gerir**e, em seguida, clique em **criar**.

   ![Adicionar uma fila de barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Adicionar um ponto final e uma regra de encaminhamento ao seu IoT hub

### <a name="add-an-endpoint"></a>Adicionar um ponto final

1. Abra o seu IoT hub, clique em pontos finais > + Adicionar.
1. Introduza as seguintes informações:

   **Nome**: O nome do ponto final.

   **Tipo de ponto final**: selecione **fila do Service Bus**.

   **Espaço de nomes do Service Bus**: selecione o espaço de nomes que criou.

   **Fila do Service Bus**: selecione a fila que criou.
1. Clique em **OK**.

   ![Adicionar um ponto final ao seu IoT hub no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Adicionar uma regra de encaminhamento

1. No seu IoT hub, clique em **rotas** > **+ adicionar**.
1. Introduza as seguintes informações:

   **Nome**: O nome da regra de encaminhamento.

   **Origem de dados**: selecione **DeviceMessages**.

   **Ponto final**: selecione o ponto final que criou.

   **Cadeia de consulta**: introduza `temperatureAlert = "true"`.
1. Clique em **Guardar**.

   ![Adicionar uma regra de encaminhamento no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar uma aplicação lógica

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica

1. No [portal do Azure](https://portal.azure.com/), clique em **novo** > **integração empresarial com** > **aplicação lógica**.
1. Introduza as seguintes informações:

   **Nome**: O nome da aplicação lógica.

   **Grupo de recursos**: utilizar o mesmo grupo de recursos que utiliza o seu IoT hub.

   **Localização**: utilizar a mesma localização que utiliza o seu IoT hub.
1. Clique em **Criar**.

### <a name="configure-the-logic-app"></a>Configurar a aplicação lógica

1. Abra a aplicação lógica que se abre no Designer de aplicações lógicas.
1. No Designer de aplicações lógica, clique em **aplicação lógica em branco**.

   ![Começar com uma aplicação de lógica em branco no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Clique em **Service Bus**.

   ![Selecionar barramento de serviço para iniciar a criação da sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Clique em **barramento de serviço – quando uma ou mais mensagens chegam numa fila (a conclusão automática)**.
1. Crie uma ligação de barramento de serviço.
   1. Introduza um nome de ligação.
   1. Clique no espaço de nomes de barramento de serviço > a política de barramento de serviço > **criar**.

      ![Criar uma ligação de barramento de serviço para a sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Clique em **continuar** depois de criar a ligação de barramento de serviço.
   1. Seleccione a fila que criou e introduza `175` para **máximo de mensagem**

      ![Especifique o número máximo de mensagem para a ligação de barramento de serviço na sua aplicação lógica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Clique em "Guardar" botão para guardar as alterações.

1. Crie uma ligação de serviço de SMTP.
   1. Clique em **novo passo** > **adicionar uma ação**.
   1. Tipo `SMTP`, clique em de **SMTP** no resultado da pesquisa de serviço e, em seguida, clique em **SMTP - enviar correio eletrónico**.

      ![Criar uma ligação de SMTP na sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Introduza as informações de SMTP da sua caixa de correio e, em seguida, clique em **criar**.

      ![Introduza as informações de ligação de SMTP na sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Obter as informações de SMTP para [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), e [Yahoo correio](https://help.yahoo.com/kb/SLN4075.html).
   1. Introduza o seu endereço de correio eletrónico para **de** e **para**, e `High temperature detected` para **requerente** e **corpo**.
   1. Clique em **Guardar**.

A aplicação lógica está por ordem de trabalho quando o guardou.

## <a name="test-the-logic-app"></a>Testar a aplicação lógica

1. Iniciar a aplicação de cliente que implementa para o seu dispositivo no [ESP8266 estabelecer ligação ao IoT Hub do Azure](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Aumentar a temperatura de ambiente em torno SensorTag ser acima c de 30. Por exemplo, claro uma vela em torno do seu SensorTag.
1. Deverá receber uma notificação por e-mail enviada pela aplicação lógica.

   > [!NOTE]
   > O fornecedor de serviços de e-mail poderá ter de verificar a identidade do remetente certificar-se de que o que envia a mensagem de e-mail é.

## <a name="next-steps"></a>Passos seguintes

Uma aplicação lógica que liga o seu IoT hub e a caixa de correio para notificações e a monitorização de temperatura foi criada com êxito.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
