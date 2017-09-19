---
title: Configurar o Aprovisionamento de Dispositivos no portal do Azure | Microsoft Docs
description: "Início Rápido do Azure - configurar o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure no Portal do Azure"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: a96f64e41b090cb60bbbb007a3913fd23ce8f609
ms.contentlocale: pt-pt
ms.lasthandoff: 09/09/2017

---

# <a name="set-up-the-iot-hub-device-provisioning-service-preview-with-the-azure-portal"></a>Configurar o Serviço Aprovisionamento de Dispositivos no Hub IoT (pré-visualização) com o portal do Azure

Estes passos mostram como configurar os recursos da cloud do Azure no portal para aprovisionar os seus dispositivos. Incluem como criar o hub IoT, criar um Serviço Aprovisionamento de Dispositivos no Hub IoT e ligar os dois serviços. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Internet das Coisas**, selecione **Hub IoT** e clique no botão **Criar**. 

3. Dê um **nome** ao seu hub IoT. Selecione a partir das opções disponíveis relativas aos preços, introduza as [unidades do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/), selecione o número de partições para as mensagens do dispositivo para a cloud e a subscrição que vai ser utilizada para este recurso. Introduza o nome de um grupo de recursos novo ou já existente e selecione a localização. Quando terminar, clique em **Criar**.

    ![Introduza as informações básicas sobre Hub IoT no painel do portal](./media/quick-setup-auto-provision/create-iot-hub-portal.png)  

4. Após o hub IoT ter sido implementado com êxito, o painel de resumo do mesmo abre-se automaticamente.


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Criar uma instância nova do Serviço Aprovisionamento de Dispositivos no Hub IoT

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. *Procure no Marketplace* o **Serviço Aprovisionamento de Dispositivos**. Selecione **Serviço Aprovisionamento de Dispositivos do IuT (pré-visualização)** e clique no botão **Criar**. 

3. Dê um **nome** à sua instância do Serviço Aprovisionamento de Dispositivos. Selecione a subscrição que vai ser utilizada para esta instância e escolha um grupo de recursos novo ou já existente. Selecione a localização. Quando terminar, clique em **Criar**.

    ![Introduza as informações básicas da instância do DPS no painel do portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Após o serviço ter sido implementado com êxito, o painel de resumo do mesmo abre-se automaticamente.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Ligar o hub IoT e o seu Serviço Aprovisionamento de Dispositivos

1. Clique no botão **Todos os recursos**, no menu do lado esquerda do portal do Azure. Selecione a instância do Serviço Aprovisionamento de Dispositivos que criou na secção anterior.  

2. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Hubs IoT ligados**. Clique no botão **+ Adicionar**, na parte superior. 

3. No painel do portal **Adicionar ligação para o hub IoT**, selecione a subscrição atual ou introduza o nome e a cadeia de ligação de outra subscrição. Selecione o nome do hub na lista pendente. Quando terminar, clique em **Guardar**. 

    ![Ligar o nome do hub para ligar à instância do DPS no painel do portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Agora, deverá ver o hub selecionado no painel **Hubs IoT ligados**. 



## <a name="clean-up-resources"></a>Limpar recursos

Outros Guias de Introdução desta coleção têm por base este Guia de Introdução. Se pretender continuar a trabalhar com Inícios Rápidos subsequentes ou com os tutoriais, não limpe os recursos criados neste Início Rápido. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos criados por este Início Rápido no portal do Azure.

1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  
2. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  

## <a name="next-steps"></a>Passos seguintes

Neste Início Rápido, implementou um hub do IoT e uma instância do Serviço Aprovisionamento de Dispositivos e ligou ambos os recursos. Para saber como utilizar esta configuração para aprovisionar um dispositivo simulado, avance para o Início Rápido para a criação de dispositivos simulados.

> [!div class="nextstepaction"]
> [Quickstart to create simulated device](./quick-create-simulated-device.md) (Início rápido para criar dispositivos simulados)

