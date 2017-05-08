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
ms.date: 04/13/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: df6f1b86b706d58a5c07a4f3de43a1872da61511
ms.contentlocale: pt-pt
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual do Windows com o portal do Azure

As máquinas virtuais podem ser criadas através do portal do Azure. Este método fornece uma interface de utilizador baseada no browser para criar e configurar máquinas virtuais e todos os recursos relacionados. Estes passos do Guia de Introdução explicam a criação de uma máquina virtual com o portal do Azure. Depois de concluída a implementação, vamos ligar ao servidor e instalar o IIS.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

2. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

3. Selecione **Computação** a partir do painel **Novo**, selecione *Windows Server 2016 Datacenter* a partir do painel **Computação** e, em seguida, clique no botão **Criar**.

4. Preencha o formulário **Noções básicas** da máquina virtual. O nome de utilizador e palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. Para o **Grupo de recursos** crie um novo. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são criados e geridos coletivamente. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. Escolha um tamanho para a VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. 

    ![Captura de ecrã que mostra os tamanhos de VM](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

6. No painel definições, selecione *Sim* em **Utilizar discos geridos**, mantenha as predefinições para o resto das definições e clique em **OK**.

7. Na página de resumo, clique em **Ok** para iniciar a implementação da máquina virtual.

8. Para monitorizar o estado de implementação, clique na máquina virtual. A VM pode ser encontrada no dashboard do portal do Azure ou ao selecionar **Máquinas Virtuais** a partir do menu do lado esquerdo. Quando a VM tiver sido criada, o estado será alterado de *Em implementação* para *Em execução*.

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web 

Para permitir tráfego para o IIS, tem de abrir a porta 80 ao tráfego da Web. Este passo orienta-o ao longo da criação de uma regra de grupo de segurança de rede (NSG) para permitir ligações de entrada na porta 80.

1. No painel da máquina virtual, na secção **Essenciais**, clique no nome do **Grupo de recursos**.
2. No painel do grupo de recursos, clique no **Grupo de segurança de rede**, na lista de recursos. O nome do NSG deve ser o nome da VM com *-nsg* acrescentado ao fim.
3. Clique no cabeçalho **Regra de Segurança de Entrada** para abrir a lista de regras de entrada. Deverá ver uma regra para RDP já na lista.
4. Clique em **+ Adicionar** para abrir o painel **Adicionar regra de segurança de entrada**.
5. Em **Nome**, escreva *IIS*. Certifique-se de que o **Intervalo da porta** está definido como *80* e a **Ação** está definida como *Permitir*. Clique em **OK**.


## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto para a máquina virtual.

1. Clique no botão **Ligar** no painel da máquina virtual. É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (ficheiro .rdp).

    ![Portal 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) a partir da Mac App Store.

3. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual e, em seguida, clique em **Ok**.

4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.


## <a name="install-iis-using-powershell"></a>Instalar o IIS com o PowerShell

Na máquina virtual, abra uma linha de comandos do PowerShell e execute o seguinte comando para instalar o IIS e ativar a regra de firewall local para permitir o tráfego da Web:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Obtenha o *Endereço IP público* a partir do painel da VM e utilize-o para visitar a página Web predefinida. 

![Site predefinido do IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazê-lo, selecione o grupo de recursos a partir do painel da máquina virtual e clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre instalação de uma função e a configuração de firewall](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar amostras de CLI de implementação de VM](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

