---
title: "Guia de Introdução do Azure - Criar Portal da VM | Microsoft Docs"
description: "Guia de Introdução do Azure - Criar Portal da VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: bcfd830a5e2f39f36460990cae7e84b04d9a5fbb
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual Linux com o portal do Azure

As máquinas virtuais podem ser criadas através do portal do Azure. Este método fornece uma interface de utilizador baseada no browser para criar e configurar máquinas virtuais e todos os recursos relacionados. Estes passos do Guia de Introdução explicam a criação de uma máquina virtual com o portal do Azure. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Precisa de um par de chaves SSH para concluir este guia de introdução. Se tiver um par de chaves SSH existente, este passo pode ser ignorado. Se estiver a utilizar um computador Windows, siga as instruções presentes [aqui](./virtual-machines-linux-ssh-from-windows.md). 

A partir de uma shell Bash, execute este comando e siga as direções no ecrã. A saída do comando inclui o nome de ficheiro do ficheiro de chave pública. O conteúdo deste ficheiro é necessário ao criar a máquina virtual.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Computação** a partir do painel **Novo**, selecione **Ubuntu Server 16.04 LTS** a partir do painel **Computação** e, em seguida, clique no botão **Criar**.

3. Preencha o formulário **Noções básicas** da máquina virtual. Para **Tipo de autenticação**, selecione **SSH**. Ao colar na sua **chave pública SSH**, tenha atenção para remover quaisquer espaços em branco à esquerda ou à direita. Para o **Grupo de recursos** crie um novo. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são criados e geridos coletivamente. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

4. Escolha um tamanho da VM e clique em **Selecionar**. 

    ![Selecione um tamanho para a VM no painel do portal](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

5. No painel definições, selecione **Sim** em **Utilizar discos geridos**, mantenha as predefinições para o resto das definições e clique em **OK**.

6. Na página de resumo, clique em **Ok** para iniciar a implementação da máquina virtual.

7. Para monitorizar o estado de implementação, clique na máquina virtual. A VM pode ser encontrada no dashboard do portal do Azure ou ao selecionar **Máquinas Virtuais** a partir do menu do lado esquerdo. Quando a VM tiver sido criada, o estado será alterado de **Em implementação** para **Em execução**.

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Depois de a implementação estar concluída, crie uma ligação de SSH com a máquina virtual.

1. Clique no botão **Ligar** no painel da máquina virtual. O botão de ligação apresenta uma cadeia de ligação SSH que pode ser utilizada para ligar à máquina virtual.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Execute o seguinte comando para criar uma sessão SSH. Substitua a cadeia de ligação que copiou a partir do portal do Azure.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazê-lo, selecione o grupo de recursos a partir do painel da máquina virtual e clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre a criação de máquinas virtuais altamente disponíveis](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explorar amostras de CLI de implementação de VM](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

