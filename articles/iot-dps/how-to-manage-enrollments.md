---
title: "Como gerir inscrições de dispositivos para o Azure IoT Hub | Microsoft Docs"
description: "Como gerir inscrições de dispositivos para o seu serviço DPS no Portal do Azure"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c230e73f83d8acd0f142e037f70a80c9e0e4107e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-device-enrollments-in-the-iot-hub-device-provisioning-service"></a>Como gerir inscrições de dispositivos em que o serviço de aprovisionamento de dispositivos do IoT Hub

A *inscrição de dispositivos* cria um registo de um único dispositivo ou um grupo de dispositivos que poderão, a determinada altura registar com o serviço de aprovisionamento de dispositivos do Azure IoT Hub. O registo de inscrição contém a configuração pretendida inicial para os dispositivos como parte do que a inscrição, incluindo o hub IoT pretendido. Este artigo mostra como gerir inscrições de dispositivos para o seu serviço de aprovisionamento.


## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivos

Existem duas formas de poder inscrever os seus dispositivos com o serviço de aprovisionamento:

1. Um **grupo inscrição** é uma entrada para um grupo de dispositivos que partilham um mecanismo de atestado comuns dos certificados x. 509, assinados pela mesma raiz AC. Recomendamos que utilize um grupo de inscrição para um grande número de dispositivos que partilham uma configuração desejada inicial ou para dispositivos que todos os mesmo inquilino. Tenha em atenção que só podem inscrever dispositivos que utilizam o mecanismo de atestado de x. 509 como *grupos inscrição*. 

    Pode criar um grupo de inscrição no portal para um grupo de dispositivos utilizando os seguintes passos.

    1. Inicie sessão no portal do Azure e clique em **todos os recursos** no menu da esquerda.
    2. Clique no serviço de aprovisionamento de dispositivos de mensagens em fila que pretende inscrever o seu dispositivo a partir da lista de recursos.
    3. No seu serviço de aprovisionamento, clique em **gerir inscrições**, em seguida, selecione **inscrição grupos** separador.
    4. Clique em de **adicionar** botão na parte superior e introduza as informações necessárias para a entrada da lista de inscrição. Carregue o certificado de raiz para o grupo de dispositivos. 
    5. Clique em **Guardar**. Na criação com êxito do seu grupo de inscrição, deverá ver o nome do grupo aparecer sob o **inscrição grupos** separador. 

        ![Grupo de inscrição no portal](./media/how-to-manage-enrollments/group-enrollment.png)

    
2. Um **inscrição individuais** é uma entrada para um único dispositivo pode registar. As inscrições individuais podem utilizar qualquer um dos x509 certificados ou SAS tokens (por um TPM real ou virtual) como mecanismos de atestado. Recomendamos que utilize inscrições individuais para os dispositivos que requerem configurações iniciais exclusivas, ou para dispositivos que só podem utilizar os tokens SAS através do TPM ou virtual TPM como o mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Pode criar um registo individual no portal utilizando os seguintes passos. 

    1. Inicie sessão no portal do Azure e clique em **todos os recursos** no menu da esquerda.
    2. Clique no serviço de aprovisionamento de dispositivos de mensagens em fila que pretende inscrever o seu dispositivo a partir da lista de recursos.
    3. No seu serviço de aprovisionamento, clique em **gerir inscrições**, em seguida, selecione **inscrições individuais** separador.
    4. Clique em de **adicionar** botão na parte superior. 
    5. Selecione o mecanismo de segurança para o dispositivo e introduza as informações necessárias para a entrada da lista de inscrição. Carregar um certificado assinado se o seu dispositivo implementa x. 509. 
    6. Clique em **Guardar**. Na criação com êxito do seu grupo de inscrição, deverá ver o seu dispositivo são apresentados no **inscrições individuais** separador. 

        ![Inscrição individuais no portal](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição
Pode atualizar uma entrada de registo existente no portal utilizando os seguintes passos.

1. Abra o seu serviço de aprovisionamento de dispositivos no portal do Azure e clique em **gerir inscrições**. 
2. Navegue para a entrada de inscrição que pretende modificar. Clique na entrada, o que abre uma informações de resumo sobre a inscrição do dispositivo. 
3. Nesta página, pode modificar os itens que não seja o tipo de segurança e credenciais, tais como o IoT hub do dispositivo devem ser associadas a, bem como o ID de dispositivo. Também pode modificar o estado de duplo de dispositivos inicial. 
4. Depois de concluída, clique em **guardar** para atualizar a inscrição do dispositivo. 

    ![Inscrição de atualização no portal](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>Remover uma inscrição de dispositivos
Em casos onde os dispositivos não têm de ser aprovisionada para qualquer hub IoT, pode remover a entrada de inscrição relacionado no portal utilizando os seguintes passos.

1. Abra o seu serviço de aprovisionamento de dispositivos no portal do Azure e clique em **gerir inscrições**. 
2. Navegue para e selecione a entrada de inscrição que pretende remover. 
3. Clique em de **eliminar** botão na parte superior e, em seguida, selecione **Sim** quando lhe for pedido para confirmar. 
5. Assim que a ação for concluída, verá a entrada removida da lista de inscrições de dispositivos. 
 
    ![Remova a inscrição no portal](./media/how-to-manage-enrollments/remove-enrollment.png)



