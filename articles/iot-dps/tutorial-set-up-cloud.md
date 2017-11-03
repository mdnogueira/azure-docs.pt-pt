---
title: "Configurar a nuvem para o serviço de aprovisionamento de dispositivos do Azure IoT Hub no portal | Microsoft Docs"
description: "O aprovisionamento automático de dispositivos do IoT Hub no Portal do Azure"
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
ms.openlocfilehash: 088d127521ce89d3a82e30ad8797fe5746ae7e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Configurar recursos de nuvem para aprovisionamento de dispositivos com o serviço de aprovisionamento de dispositivos do IoT Hub

Este tutorial mostra como configurar a nuvem para a utilização do serviço de aprovisionamento de dispositivos do IoT Hub de aprovisionamento automático de dispositivos. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para criar um serviço de aprovisionamento de dispositivos do IoT Hub e obter o âmbito de ID
> * Criar um hub IoT
> * Ligação do hub IoT para o serviço de aprovisionamento de dispositivos
> * Definir a política de alocação no serviço de aprovisionamento de dispositivos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Criar uma instância de serviço de aprovisionamento de dispositivos e obter o âmbito de ID

Siga estes passos para criar uma nova instância de serviço de aprovisionamento de dispositivos.

1. No canto superior esquerdo do portal do Azure, clique em **novo**.
2. Na caixa de pesquisa, escreva **aprovisionamento de dispositivos**. 
3. Clique em **Service de aprovisionamento de dispositivos de IoT Hub**.
4. Preencha o **serviço de aprovisionamento de dispositivos IoT Hub** formulário com as seguintes informações:
    
   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome** | Um nome exclusivo | -- | 
   | **Subscrição** | A sua subscrição  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |   

   ![Introduza as informações básicas sobre o DPS no portal](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Clique em **Criar**.
6. O *âmbito ID* é utilizado para identificar os IDs de registo e fornece uma garantia que o ID de registo é exclusivo. Para obter este valor, clique em **descrição geral** para abrir o **Essentials** página para o serviço de aprovisionamento de dispositivos. Copiar o **ID âmbito** valor para uma localização temporária para utilização posterior.
7. Também anote o **ponto final de serviço** valor ou copie-o para uma localização temporária para utilização posterior. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Criou o seu hub IoT e tem agora o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir este tutorial.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>O serviço de aprovisionamento de dispositivos de ligação a um IoT hub

O passo seguinte é para ligar o serviço de aprovisionamento de dispositivos e de IoT hub para que o serviço de aprovisionamento de dispositivos do IoT Hub pode registar os dispositivos que hub. O serviço apenas pode aprovisionar dispositivos para os hubs IoT que foram associados para o serviço de aprovisionamento de dispositivos. Siga estes passos.

1. No **todos os recursos** página, clique na instância de serviço de aprovisionamento de dispositivos que criou anteriormente.
2. Na página do serviço de aprovisionamento de dispositivos, clique em **os hubs IoT ligado**.
3. Clique em **Adicionar**.
4. No **adicionar ligação ao IoT hub** página, utilize os botões de opção para especificar se o IoT hub ligado está localizado na subscrição atual, ou numa subscrição diferente. Em seguida, escolha o nome do hub IoT do **IoT hub** caixa.
5. Clique em **Guardar**.

   ![Ligar o nome do hub para ligar a DPS no portal](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Definir a política de alocação no serviço de aprovisionamento de dispositivos

A política de alocação é uma definição de serviço de aprovisionamento de dispositivos IoT Hub que determina a forma como os dispositivos são atribuídos a um IoT hub. Existem três políticas de alocação suportados: 

1. **Latência mais baixa**: dispositivos são aprovisionados para um hub IoT com base no hub com a latência mais baixa para o dispositivo.
2. **Uniformemente ponderado distribuição** (predefinição): os hubs IoT ligado são igualmente ter, provavelmente, os dispositivos aprovisionados aos mesmos. Esta é a predefinição. Se estiver a aprovisionar dispositivos ao IoT hub apenas uma, pode manter esta definição. 
3. **Configuração estático através da lista de inscrição**: especificação do hub IoT pretendida na lista de inscrição tem prioridade sobre a política de alocação de nível de serviço aprovisionamento de dispositivos.

Definir a política de alocação do serviço de aprovisionamento de dispositivos página, clique em **gerir a política de alocação**. Certifique-se a política de alocação está definida como **uniformemente ponderado distribuição** (predefinição). Se efetuar alterações, clique em **guardar** quando tiver terminado.

![Gerir a política de alocação](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Limpar recursos

Outros tutoriais nesta coleção tirar partido deste tutorial. Se pretender continuar com a trabalhar com subsequentes inícios rápidos ou com os tutoriais, não limpeza até os recursos que criou neste tutorial. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este tutorial no portal do Azure.

1. No menu da esquerda no portal do Azure, clique em **todos os recursos** e, em seguida, selecione a instância de serviço de aprovisionamento de dispositivos IoT Hub. Na parte superior do **todos os recursos** página, clique em **eliminar**.  
2. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Na parte superior do **todos os recursos** página, clique em **eliminar**.
 
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para criar um serviço de aprovisionamento de dispositivos do IoT Hub e obter o âmbito de ID
> * Criar um hub IoT
> * Ligação do hub IoT para o serviço de aprovisionamento de dispositivos
> * Definir a política de alocação no serviço de aprovisionamento de dispositivos

Avançar para o próximo tutorial para saber como configurar o seu dispositivo para o aprovisionamento.

> [!div class="nextstepaction"]
> [Configurar o dispositivo para o aprovisionamento](tutorial-set-up-device.md)
