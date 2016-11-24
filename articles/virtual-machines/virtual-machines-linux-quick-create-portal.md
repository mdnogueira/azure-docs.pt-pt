---
title: "Criar uma VM com Linux através do Portal do Azure | Microsoft Docs"
description: "Crie uma VM com Linux através do Portal do Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 3ad64861bc4c3b0a938c75990fc516ef634943ef


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Criar uma VM com Linux no Azure através do Portal
Este artigo mostra-lhe como utilizar o [Portal do Azure](https://portal.azure.com/) para criar uma Máquina Virtual do Linux.

Os requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>Iniciar sessão
Após iniciar sessão no Portal do Azure com a sua identidade da conta do Azure, clique em **+ Novo** no canto superior esquerdo:

![ecrã1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>Escolher VM
Clique em **Virtual Machines** no **Marketplace** e, em seguida, em **Ubuntu Server 14.04 LTS** na lista de imagens **Aplicações em Destaque**.  Na parte inferior, certifique-se de que o modelo de implementação é `Resource Manager` e, em seguida, clique em **Criar**.

![ecrã2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>Introduzir as opções da VM
Na página **Noções básicas**, introduza:

* um nome para a VM
* um nome de utilizador para o Utilizador de Admin
* o Tipo de Autenticação definido como **Chave pública SSH**
* a Chave pública SSH como uma cadeia (a partir do diretório `~/.ssh/`)
* um nome do grupo de recursos ou selecione um existente

e Clique em **OK** para continuar e escolha o tamanho da VM; deve ser algo semelhante à seguinte captura de ecrã:

![ecrã3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>Selecionar o tamanho da VM
Escolha o tamanho **DS1**, que instala o Ubuntu num SSD Premium, e clique em **Selecionar** para configurar as definições.

![ecrã4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>Armazenamento e rede
Em **Definições**, deixe as predefinições para os valores de Armazenamento e de Rede e clique em **OK** para ver o resumo.  Tenha em atenção que o tipo de disco foi definido como SSD Premium ao escolher DS1, o **S** significa SSD.

![ecrã5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>Confirmar as definições da VM e iniciar
Confirme as definições para a nova VM com Ubuntu e clique em **OK**.

![ecrã6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>Localizar o NIC da VM
Abra o Dashboard do Portal e, em **Interfaces de rede** escolha o seu NIC

![ecrã7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>Encontrar o IP público
Abrir o menu de Endereços IP públicos nas definições da NIC

![ecrã8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>SSH para a VM
SSH para o IP público utilizando a chave pública SSH.  A partir de uma estação de trabalho Mac ou Linux, pode encaminhar o SSH diretamente a partir do Terminal. Se tiver uma estação de trabalho do Windows, tem de utilizar o PuTTY, o MobaXTerm ou o Cygwin para encaminhar o SSH para o Linux.  Se ainda não tiver, eis um documento que obtém a estação de trabalho do Windows preparado para encaminhar o SSH para o Linux.

[Como Utilizar chaves SSH com Windows no Azure](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Passos Seguintes
Acabou de criar uma VM com Linux rapidamente para fins de teste ou de demonstração. Para criar uma VM Linux personalizada de acordo com a sua infraestrutura, pode seguir qualquer um destes artigos.

* [Criar uma VM do Linux no Azure utilizando Modelos](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux Protegida por SSH no Azure através de Modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux através da CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Nov16_HO2-->


