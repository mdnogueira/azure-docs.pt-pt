---
title: Capturar uma imagem de uma VM com Linux | Microsoft Docs
description: "Saiba como capturar uma imagem de uma baseado em Linux máquina virtual do Azure (VM) criada com o modelo de implementação clássica."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: ecde5dd3211bfbb290e6910d7d55136d079c6cf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>How to capture a classic Linux virtual machine as an image (Como capturar uma máquina virtual do Linux clássica como uma imagem)
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Este artigo mostra como capturar uma máquina de virtual do Azure (VM) clássica com o Linux como uma imagem para criar outras máquinas virtuais. Esta imagem inclui o disco do SO e discos de dados ligados à VM. Não inclui configuração de rede, por isso terá de configurar que quando criar outra VM a partir da imagem.

Azure armazena a imagem em **imagens**, juntamente com quaisquer imagens carregar. Para obter mais informações sobre imagens, consulte [sobre imagens de Máquina Virtual no Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Antes de começar
Estes passos partem do princípio de que já criou uma VM do Azure utilizando o modelo de implementação clássica e configurado o sistema operativo, incluindo anexar qualquer discos de dados. Se precisar de criar uma VM, leia [como criar uma Máquina Virtual Linux][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Capturar a máquina virtual
1. [Ligar à VM](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) utilizando um cliente SSH à sua escolha.
2. Na janela do SSH, escreva o seguinte comando. O resultado da `waagent` pode variar devido às ligeiramente dependendo da versão deste utilitário:

    ```bash
    sudo waagent -deprovision+user
    ```

    O comando anterior tenta limpar o sistema e torná-lo adequado para reprovisioning. Esta operação efetua as seguintes tarefas:

   * Remove as chaves de anfitrião do SSH (se Provisioning.RegenerateSshHostKeyPair 'y' no ficheiro de configuração)
   * Limpa configuração nameserver /etc/resolv.conf
   * Remove o `root` palavra-passe de utilizador do etc/sombra de volumes (se Provisioning.DeleteRootPassword 'y' no ficheiro de configuração)
   * Remove as concessões de cliente DHCP em cache
   * Repõe o nome do anfitrião para localhost.localdomain
   * Elimina o último aprovisionado conta de utilizador (obtida /var/lib/waagent) **e dados associados**.

     > [!NOTE]
     > Desaprovisionamento elimina os ficheiros e dados para a imagem "generalizar". Apenas execute este comando numa VM que pretende capturar como um novo modelo de imagem. Garante que a imagem é limpa de todas as informações confidenciais ou de que é adequada para a redistribuição para terceiros.

3. Tipo **y** para continuar. Pode adicionar o `-force` parâmetro para evitar este passo de confirmação.
4. Tipo **saída** para fechar o cliente SSH.

   > [!NOTE]
   > Os restantes passos partem do princípio de que já tem [instalado a CLI do Azure](../../../cli-install-nodejs.md) no computador cliente. Também podem ser feitos os seguintes passos [portal do Azure](http://portal.azure.com).

5. A partir do computador cliente, abra CLI do Azure e o início de sessão para a sua subscrição do Azure. Para obter mais informações, leia o artigo [ligar a uma subscrição do Azure a partir da CLI do Azure](../../../xplat-cli-connect.md).

   > [!NOTE]
   > No portal do Azure, inicie sessão no portal.

6. Certifique-se de que estejam no modo de gestão do serviço:

    ```azurecli
    azure config mode asm
    ```

7. Encerre a VM que já está a ser desaprovisionada. O exemplo seguinte será encerrado a VM com o nome `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Se necessário, pode ver uma lista de todas as VMs criadas na sua subscrição através da utilização`azure vm list`

   > [!NOTE]
   > Se estiver a utilizar o portal do Azure, selecione a VM e clique em **parar** para encerre a VM.

8. Quando a VM é parada, capture a imagem. O exemplo seguinte captura de VM com o nome `myVM` e cria uma imagem generalizada com o nome `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    O `-t` subcomando elimina a máquina virtual original.

    > [!NOTE]
    > No portal do Azure, pode capturar uma imagem selecionando **imagem** no hub menu. Terá de fornecer as seguintes informações para a imagem: nome, grupo de recursos, localização, tipo de sistema operativo e caminho do blob de armazenamento.

9. A nova imagem está agora disponível na lista de imagens que pode ser utilizado para configurar qualquer nova VM. Pode vê-la com o comando:

   ```azurecli
   azure vm image list
   ```

   No [portal do Azure](http://portal.azure.com), a nova imagem aparece no **imagens da VM (clássica)** que pertence ao **computação** serviços. Pode aceder ao **imagens da VM (clássica)** clicando _mais serviços_ na parte inferior do Azure service lista e, em seguida, procura **computação** serviços.   

   ![Captura de imagens com êxito](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Passos seguintes
A imagem está pronta para ser utilizado para criar as VMs. Pode utilizar o comando da CLI do Azure `azure vm create` e forneça o nome da imagem que criou. Para obter mais informações, consulte [utilizando a CLI do Azure com o modelo de implementação clássica](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Em alternativa, utilize o [portal do Azure](http://portal.azure.com) para criar uma VM personalizada utilizando o **imagem** método e selecionar a imagem que criou. Para obter mais informações, consulte [como criar uma VM personalizada][How to Create a Custom Virtual Machine].

**Consulte também:** [guia de utilizador do agente Linux do Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk.md
[How to Create a Linux Virtual Machine]:create-custom.md
