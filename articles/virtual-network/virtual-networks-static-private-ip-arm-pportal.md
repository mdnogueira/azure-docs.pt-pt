---
title: "Configurar endereços IP privados para as VMs - portal do Azure | Microsoft Docs"
description: "Saiba como configurar endereços IP privados para as máquinas virtuais no portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 672462fad715758e50680fa5bade4b1f9d50e6e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configurar endereços IP privados para uma máquina virtual utilizando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [CLI do Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Portal do Azure (clássica)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (Clássico)](virtual-networks-static-private-ip-classic-ps.md)
> * [CLI do Azure (clássica)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Também pode [gerir o endereço IP privado estático no modelo de implementação clássica](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os passos de exemplo abaixo esperam num ambiente simple que já criado. Se pretender executar os passos, como são apresentados neste documento, criar primeiro o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Como criar uma VM de teste privados endereços IP estáticos
Não é possível definir um endereço IP privado estático durante a criação de uma VM no modo de implementação Resource Manager através do portal do Azure. Tem de criar primeiro a VM, tehn definir o IP privado estático.

Para criar uma VM chamada *DNS01* no *front-end* sub-rede de uma VNet com o nome *TestVNet*, siga os passos abaixo.

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **novo** > **computação** > **Windows Server 2012 R2 Datacenter**, tenha em atenção que o **selecionar um modelo de implementação** já lista mostra **Resource Manager**e, em seguida, clique em **criar**, como mostrado na imagem abaixo.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. No **Noções básicas** painel, introduza o nome da VM que sejam criados (*DNS01* no nosso cenário), a conta de administrador local e a palavra-passe, conforme mostrado na imagem abaixo.
   
    ![Painel Básico](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Certifique-se a **localização** seleccionado é *EUA Central*, em seguida, clique em **selecionar existente** em **grupo de recursos**, em seguida, clique em **Grupo de recursos** novamente, em seguida, clique em *TestRG*e, em seguida, clique em **OK**.
   
    ![Painel Básico](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. No **escolher um tamanho** painel, selecione **A1 padrão**e, em seguida, clique em **selecione**.
   
    ![Escolha um painel de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. No **definições** painel, certifique-se de que as seguintes propriedades forem definidas são definidas com os valores abaixo e, em seguida, clique em **OK**.
   
    -**Conta de armazenamento**: *vnetstorage*
   
   * **Rede**: *TestVNet*
   * **Sub-rede**: *front-end*
     
     ![Escolha um painel de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. No **resumo** painel, clique em **OK**. Repare no mosaico abaixo apresentado no dashboard.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter privadas informações endereços IP estáticos para uma VM
Para ver as estáticas informações de endereço IP privadas para a VM criada com os passos acima, execute os passos abaixo.

1. No portal do Azure do Azure, clique em **Procurar tudo** > **máquinas virtuais** > **DNS01** > **todas as definições**   >  **Interfaces de rede** e, em seguida, clique na interface de rede apenas listada.
   
    ![Implementar o mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. No **interface de rede** painel, clique em **todas as definições** > **endereços IP** e repare o **atribuição** e  **Endereço IP** valores.
   
    ![Implementar o mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático para uma VM existente
Para adicionar um endereço IP privado estático para a VM criada utilizando os passos acima, siga os passos abaixo:

1. Do **endereços IP** painel mostrado acima, clique em **estático** em **atribuição**.
2. Tipo *192.168.1.101* para **endereço IP**e, em seguida, clique em **guardar**.
   
    ![Criar a VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Se depois de clicar em **guardar** , tenha em atenção que a atribuição ainda está definida **dinâmica**, significa que o endereço IP que escreveu já está em utilização. Tente um endereço IP diferente.
> 
> 

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma VM
Para remover o endereço IP privado estático de VM criada acima, conclua o passo seguinte:

Do **endereços IP** painel mostrado acima, clique em **dinâmica** em **atribuição**e, em seguida, clique em **guardar**.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [reservado de IP público](virtual-networks-reserved-public-ip.md) endereços.
* Saiba mais sobre [instância ao nível do IP público (ILPIP)](virtual-networks-instance-level-public-ip.md) endereços.
* Consulte o [reservado APIs REST do IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).

