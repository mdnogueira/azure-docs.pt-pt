---
title: "Vários endereços IP para máquinas virtuais do Azure - Portal | Microsoft Docs"
description: "Saiba como atribuir vários endereços IP a uma máquina virtual utilizando o portal do Azure | Gestor de recursos."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
ms.openlocfilehash: d264bd47d76db8015a64f09248c57c94572e2693
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP para máquinas virtuais utilizando o portal do Azure

>[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
>
Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação Azure Resource Manager no portal do Azure. Vários endereços IP não não possível atribuir recursos criados através do modelo de implementação clássica. Para saber mais sobre modelos de implementação do Azure, leia o [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Se pretender criar uma VM com vários endereços IP ou um endereço IP privado estático, tem de criar, utilizar o PowerShell ou a CLI do Azure. Clique em Opções do PowerShell ou a CLI na parte superior deste artigo para saber como. Pode criar uma VM com um único endereço IP privado dinâmico e (opcionalmente) um único endereço IP público utilizando o portal, seguindo os passos a [criar uma VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [criar uma VM com Linux](../virtual-machines/linux/quick-create-portal.md) artigos. Depois de criar a VM, pode alterar o tipo de endereço IP dinâmico para estática e adicionar endereços IP adicionais através do portal, os seguintes passos de [endereços IP de adicionar a uma VM](#add) secção deste artigo.

## <a name="add"></a>Adicionar endereços IP para uma VM

Pode adicionar endereços IP públicos e privados a um NIC, efetuando os passos que se seguem. Os exemplos das secções seguintes partem do princípio de que já tem uma VM com as três configurações de IP descritas no [cenário](#Scenario) deste artigo, mas não é necessário que efetuar.

### <a name="coreadd"></a>Passos de núcleo

1. Navegue até ao portal do Azure em https://portal.azure.com e inicie sessão para a mesma, se necessário.
2. No portal, clique em **mais serviços** > tipo *máquinas virtuais* na caixa do filtro e, em seguida, clique em **máquinas virtuais**.
3. No **máquinas virtuais** painel, clique em que a VM que pretende adicionar IP endereços. Clique em **interfaces de rede** na máquina virtual painel que aparece e, em seguida, selecione a interface de rede que pretende adicionar o IP endereços. No exemplo mostrado na imagem seguinte, a NIC com o nome *myNIC* da VM com o nome *myVM* está selecionado:

    ![Interface de rede](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. No painel que aparece para a NIC tiver selecionado, clique em **configurações de IP**.

Conclua os passos de uma das seguintes secções que se seguem, com base no tipo de endereço IP que pretende adicionar.

### <a name="add-a-private-ip-address"></a>**Adicionar um endereço IP privado**

Conclua os seguintes passos para adicionar um novo endereço IP privado:

1. Concluir os passos a [principais passos](#coreadd) secção deste artigo.
2. Clique em **Adicionar**. No **configuração de IP adicionar** painel que aparece, criar uma configuração de IP com o nome *IPConfig 4* com *10.0.0.7* como um *estático* IP privado de endereços, em seguida, clique em **OK**.

    > [!NOTE]
    > Ao adicionar um endereço IP estático, tem de especificar um endereço válido, não utilizado na sub-rede que a NIC está ligada. Se o endereço que selecionou não estiver disponível, o portal irá mostrar um X para o endereço IP e terá de selecionar um diferente.

3. Assim que clicar em OK, o painel será fechado e verá que a nova configuração de IP listada. Clique em **OK** para fechar o **configuração de IP adicionar** painel.
4. Pode clicar em **adicionar** adicionar configurações de IP adicionais ou fechar todas as abra painéis para acabar de adicionar endereços IP.
5. Adicionar os endereços IP privados para o sistema operativo VM, efetuando os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo.

### <a name="add-a-public-ip-address"></a>Adicionar um endereço IP público

Um endereço IP público é adicionado ao associar um recurso de endereço IP público para uma nova configuração de IP ou uma configuração de IP existente.

> [!NOTE]
> Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Não há um limite ao número de endereços IP públicos que podem ser utilizadas numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
> 

### <a name="create-public-ip"></a>Criar um recurso de endereço IP público

Um endereço IP público é uma definição para um recurso de endereço IP público. Se tiver um recurso de endereço IP público que não está atualmente associado a uma configuração de IP que pretende associar a uma configuração de IP, ignorar os passos seguintes e execute os passos de uma das seguintes secções que se seguem, forem necessárias. Se não tiver um recurso de endereço IP público disponível, execute os seguintes passos para criar um:

1. Navegue até ao portal do Azure em https://portal.azure.com e inicie sessão para a mesma, se necessário.
3. No portal, clique em **novo** > **redes** > **endereço IP público**.
4. No **Criar endereço IP público** painel apresentado, introduza um **nome**, selecione um **atribuição de endereços IP** tipo, um **subscrição**, um **Grupo de recursos**e um **localização**, em seguida, clique em **criar**, conforme mostrado na imagem seguinte:

    ![Criar um recurso de endereço IP público](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Conclua os passos de uma das seguintes secções que se seguem para associar o recurso de endereço IP público para uma configuração de IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associe o recurso de endereço IP público para uma nova configuração de IP

1. Concluir os passos a [principais passos](#coreadd) secção deste artigo.
2. Clique em **Adicionar**. No **configuração de IP adicionar** painel que aparece, criar uma configuração de IP com o nome *IPConfig 4*. Ativar o **endereço IP público** e selecione um existente, disponível endereço recurso de IP público do **escolher endereço IP público** painel que aparece.

    Depois de selecionar o recurso de endereço IP público, clique em **OK** e o painel será fechada. Se não tiver um endereço IP público existente, pode criar um, efetuando os passos a [criar um recurso de endereço IP público](#create-public-ip) secção deste artigo. 

3. Reveja a nova configuração de IP. Apesar de explicitamente não foi atribuído um endereço IP privado, um foi atribuído automaticamente a configuração de IP, porque todas as configurações de IP tem de ter um endereço IP privado.
4. Pode clicar em **adicionar** adicionar configurações de IP adicionais ou fechar todas as abra painéis para acabar de adicionar endereços IP.
5. Adicione o endereço IP privado para o sistema operativo VM, efetuando os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione o endereço IP público para o sistema operativo.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associe o recurso de endereço IP público para uma configuração de IP existente

1. Concluir os passos a [principais passos](#coreadd) secção deste artigo.
2. Clique em que pretende adicionar o recurso de endereço IP público para a configuração de IP.
3. No painel de IPConfig que é apresentado, clique em **endereço IP**.
4. No **escolher endereço IP público** painel que aparece, selecione um endereço IP público.
5. Clique em **guardar** e irão fechar os painéis. Se não tiver um endereço IP público existente, pode criar um, efetuando os passos a [criar um recurso de endereço IP público](#create-public-ip) secção deste artigo.
3. Reveja a nova configuração de IP.
4. Pode clicar em **adicionar** adicionar configurações de IP adicionais ou fechar todas as abra painéis para acabar de adicionar endereços IP. Adicione o endereço IP público para o sistema operativo.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
