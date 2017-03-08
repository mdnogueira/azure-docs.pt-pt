---
title: "Criar uma VM com Linux através do Portal do Azure | Microsoft Docs"
description: "Crie uma VM com Linux através do Portal do Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 1/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: beff4fb41ed46b016088734054e7a7897fed1a30
ms.openlocfilehash: 7287b87b1e50e28de06a5363a1f35bd7ac34d51c
ms.lasthandoff: 02/15/2017


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Criar uma VM com Linux no Azure através do Portal
Este artigo mostra-lhe como utilizar o [Portal do Azure](https://portal.azure.com/) para criar uma Máquina Virtual do Linux.

Os requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>Iniciar sessão
Inicie sessão no portal do Azure com a sua identidade da conta do Azure. Clique em **+Novo** no canto superior esquerdo:

![Criar recurso do Azure](./media/virtual-machines-linux-quick-create-portal/create_new_resource.png)

## <a name="choose-vm"></a>Escolher VM
Clique em **Computação** no **Marketplace** e, em seguida, selecione **Ubuntu Server 16.04 LTS** na lista de imagens **Aplicações em Destaque**.  Na parte inferior, certifique-se de que o modelo de implementação é `Resource Manager` e, em seguida, clique em **Criar**.

![Selecione uma imagem da máquina virtual do Azure Marketplace](./media/virtual-machines-linux-quick-create-portal/create_new_vm.png)

## <a name="enter-vm-options"></a>Introduzir as opções da VM
Na página **Noções básicas**, introduza:

* um nome para a VM
* o tipo de disco da VM (SSD por predefinição ou HDD)
* um nome de utilizador para o utilizador de administração
* defina o **Tipo de Autenticação** como **Chave pública SSH**
* a chave pública SSH como uma cadeia (a partir do diretório `~/.ssh/`)
* um nome do grupo de recursos ou selecione um existente

e clique em **OK** para continuar. O painel deve ter um aspeto semelhante à seguinte captura de ecrã:

![Introduza as opções de VM do Azure básicas](./media/virtual-machines-linux-quick-create-portal/enter_basic_vm_details.png)

## <a name="choose-vm-size"></a>Selecionar o tamanho da VM
Selecionar o tamanho da VM. Os exemplos seguintes selecionam **DS1_V2 Standard**, que instala o Ubuntu num SSD Premium. O **S** no tamanho da VM reconhece o suporte SSD. Clique em **Selecionar** para configurar as definições.

![Escolher um tamanho da VM do Azure](./media/virtual-machines-linux-quick-create-portal/select_vm_size.png)

## <a name="storage-and-network"></a>Armazenamento e rede
No painel **Definições**, pode optar por utilizar Managed Disks do Azure para a VM. A predefinição atual é a utilização de discos não geridos. Os Managed Disks do Azure processados pela plataforma do Azure e não necessitam de qualquer preparação ou localização para serem armazenados. Para mais informações sobre Managed Disks do Azure, veja [Azure Managed Disks Overview (Descrição geral dos Managed Disks do Azure)](../storage/storage-managed-disks-overview.md). Para discos não geridos, terá de criar ou selecionar uma conta de armazenamento para os discos rígidos virtuais:

![Selecione a conta de armazenamento para discos não geridos](./media/virtual-machines-linux-quick-create-portal/configure_non_managed_disks.png)

Se optar por utilizar Managed Disks do Azure, não existem opções de armazenamento adicional para configurar, conforme mostrado no seguinte exemplo:

![Escolha a opção de Managed Disks do Azure no portal](./media/virtual-machines-linux-quick-create-portal/select_managed_disks.png)

Deixe o lembrete das definições de rede como predefinição.

## <a name="confirm-vm-settings-and-launch"></a>Confirmar as definições da VM e iniciar
Confirme as definições para a nova VM com Ubuntu e clique em **OK**.

![Reveja as definições da VM do Azure e crie a VM](./media/virtual-machines-linux-quick-create-portal/review_final_vm_settings.png)

## <a name="select-the-vm-resource"></a>Selecione o recurso da VM
Abra a home page do Portal e selecione **Grupos de recursos** no menu no canto superior esquerdo. Se for necessário, clique nas três barras na parte superior do menu para expandir a lista da seguinte forma:

![Abra a lista de grupos de recursos](./media/virtual-machines-linux-quick-create-portal/select_resource_group.png)

Selecione o grupo de recursos e, em seguida, clique na sua nova VM:

![Localize as definições da NIC de VM do Azure](./media/virtual-machines-linux-quick-create-portal/select_vm_resource.png)

## <a name="find-the-public-ip"></a>Encontrar o IP público
Veja o **Endereço IP público** atribuído à VM:

![Obtenha o endereço IP público da VM do Azure](./media/virtual-machines-linux-quick-create-portal/view_public_ip_address.png)

## <a name="ssh-to-the-vm"></a>SSH para a VM
SSH para o IP público utilizando a chave pública SSH.  A partir de uma estação de trabalho Mac ou Linux, pode encaminhar o SSH diretamente a partir do Terminal. Se tiver uma estação de trabalho do Windows, tem de utilizar o PuTTY, o MobaXTerm ou o Cygwin para encaminhar o SSH para o Linux.  Se ainda não tiver, eis um documento que obtém a estação de trabalho do Windows preparado para encaminhar o SSH para o Linux.

[Como Utilizar chaves SSH com Windows no Azure](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ops@40.112.255.214
```

## <a name="next-steps"></a>Passos Seguintes
Acabou de criar uma VM com Linux rapidamente para fins de teste ou de demonstração. Para criar uma VM Linux personalizada de acordo com a sua infraestrutura, pode seguir qualquer um destes artigos.

* [Criar uma VM do Linux no Azure utilizando Modelos](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux Protegida por SSH no Azure através de Modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux através da CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


