---
title: "Configurar endereços IP privados para as VMs (clássica) - portal do Azure | Microsoft Docs"
description: "Saiba como configurar endereços IP privados para máquinas virtuais (clássicas) no portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bde6de3495c2909b63b1f85e420a4ff5e7ac2c1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurar endereços IP privados para uma máquina virtual (clássica) no portal do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP privado estático no modelo de implementação Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os passos de exemplo abaixo esperam num ambiente simple que já criado. Se pretender executar os passos, como são apresentados neste documento, criar primeiro o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático quando criar uma VM
Para criar uma VM chamada *DNS01* no *front-end* sub-rede de uma VNet com o nome *TestVNet* com um IP privado estático de *192.168.1.101*, siga os passos abaixo:

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **novo** > **computação** > **Windows Server 2012 R2 Datacenter**, tenha em atenção que o **selecionar um modelo de implementação** já lista mostra **clássico**e, em seguida, clique em **criar**.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. No **criar VM** painel, introduza o nome da VM que sejam criados (*DNS01* no nosso cenário), a conta de administrador local e a palavra-passe.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Clique em **configuração opcional** > **rede** > **rede Virtual**e, em seguida, clique em **TestVNet**. Se **TestVNet** não está disponível, certifique-se de que está a utilizar o *EUA Central* localização e tiver criado o ambiente de teste descrito no início deste artigo.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. No **rede** painel, certifique-se de que a sub-rede selecionada atualmente *front-end*, em seguida, clique em **endereços IP**, em **atribuição de endereços IP** clique em **estático**e, em seguida, introduza *192.168.1.101* para **endereço IP** como mostrado abaixo.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Clique em **OK** no **endereços IP** painel, em seguida, clique em **OK** no **rede** painel e clique em **OK** no **configuração opcional** painel.
7. No **criar VM** painel, clique em **criar**. Repare no mosaico abaixo apresentado no dashboard.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter privadas informações endereços IP estáticos para uma VM
Para ver as estáticas informações de endereço IP privadas para a VM criada com os passos acima, execute os passos abaixo.

1. No portal do Azure do Azure, clique em **Procurar tudo** > **máquinas virtuais (clássicas)** > **DNS01** > **todas as definições** > **endereços IP** e repare o endereço IP e a atribuição de endereços IP, como mostrado abaixo.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma VM
Para remover o endereço IP privado estático de VM criada acima, siga os passos abaixo.

1. Do **endereços IP** painel mostrado acima, clique em **dinâmica** à direita do **atribuição de endereços IP**, em seguida, clique em **guardar**e, em seguida, clique em **Sim**.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático para uma VM existente
Para adicionar um endereço IP privado estático para a VM criada utilizando os passos acima, siga os passos abaixo:

1. Do **endereços IP** painel mostrado acima, clique em **estático** à direita do **atribuição de endereços IP**.
2. Tipo *192.168.1.101* para **endereço IP**, em seguida, clique em **guardar**e, em seguida, clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [reservado de IP público](virtual-networks-reserved-public-ip.md) endereços.
* Saiba mais sobre [instância ao nível do IP público (ILPIP)](virtual-networks-instance-level-public-ip.md) endereços.
* Consulte o [reservado APIs REST do IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).

