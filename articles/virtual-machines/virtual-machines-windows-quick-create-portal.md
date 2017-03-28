---
title: "Guia de Introdução do Azure - Criar Portal da VM do Windows | Microsoft Docs"
description: "Guia de Introdução do Azure - Criar Portal da VM do Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a13ac5ab425ccbbe53d77cb9f5a8ebf02d009370
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual do Windows com o portal do Azure

As máquinas virtuais podem ser criadas através do portal do Azure. Este método fornece uma interface de utilizador baseada no browser para criar e configurar máquinas virtuais e todos os recursos relacionados. Estes passos do Guia de Introdução explicam a criação de uma máquina virtual com o portal do Azure. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

2. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

3. Selecione **Computação** a partir do painel **Novo**, selecione **Windows Server 2016 Datacenter** a partir do painel **Computação** e, em seguida, clique no botão **Criar**.

4. Preencha o formulário **Noções básicas** da máquina virtual. O nome de utilizador e palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. Para o **Grupo de recursos** crie um novo. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são criados e geridos coletivamente. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. Escolha um tamanho da VM e clique em **Selecionar**. 

6. No painel definições, selecione **Sim** em **Utilizar discos geridos**, mantenha as predefinições para o resto das definições e clique em **OK**.

7. Na página de resumo, clique em **Ok** para iniciar a implementação da máquina virtual.

8. Para monitorizar o estado de implementação, clique na máquina virtual. A VM pode ser encontrada no dashboard do portal do Azure ou ao selecionar **Máquinas Virtuais** a partir do menu do lado esquerdo. Quando a VM tiver sido criada, o estado será alterado de **Em implementação** para **Em execução**.

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto para a máquina virtual.

1. Clique no botão **Ligar** no painel da máquina virtual. É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (ficheiro .rdp).

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) a partir da Mac App Store.

3. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual e, em seguida, clique em **Ok**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazê-lo, selecione o grupo de recursos a partir do painel da máquina virtual e clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre instalação de uma função e a configuração de firewall](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar amostras de CLI de implementação de VM](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
