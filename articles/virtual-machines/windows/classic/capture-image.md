---
title: Capturar uma imagem de VM Windows do Azure | Microsoft Docs
description: "Capturar uma imagem de uma máquina virtual do Windows no Azure criada com o modelo de implementação clássica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 6fa6d3099d8427a186e6095fdcbbc327d1acab7f
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capturar uma imagem de uma máquina virtual do Windows no Azure criada com o modelo de implementação clássica.
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para informações de modelo do Resource Manager, consulte [capturar uma imagem gerida de uma VM no Azure generalizada](../capture-image-resource.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Este artigo mostra como capturar uma máquina virtual do Azure a executar o Windows, para que possa utilizar como uma imagem para criar outras máquinas virtuais. Esta imagem inclui o disco de sistema operativo e qualquer discos de dados que estão anexados à máquina virtual. Não inclui as configurações de rede, pelo que terá de configurar as configurações de rede quando cria as outras máquinas virtuais que utilizam a imagem.

Azure armazena a imagem em **imagens da VM (clássica)**, um **computação** serviço que está listado quando visualizar todos os serviços do Azure. Este é o mesmo local onde estão armazenadas as imagens de carregar. Para obter mais informações sobre imagens, consulte [sobre imagens de máquinas virtuais](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar
Estes passos partem do princípio de que já criou uma máquina virtual do Azure e configurar o sistema operativo, incluindo anexar qualquer discos de dados. Se ainda não o fez ainda, consulte os artigos seguintes para obter informações sobre como criar e preparar a máquina virtual:

* [Criar uma máquina virtual a partir de uma imagem](createportal.md)
* [Como anexar um disco de dados para uma máquina virtual](attach-disk.md)
* Certifique-se que as funções de servidor são suportadas com o Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Este processo elimina a máquina virtual original após a respetiva captura.
>
>

Antes de capturar uma imagem de uma máquina virtual do Azure, recomenda-se a máquina virtual de destino a cópia de segurança. Máquinas virtuais do Azure pode ser feitas através de cópia de segurança do Azure. Para obter detalhes, veja [Fazer cópia de segurança de máquinas virtuais do Azure](../../../backup/backup-azure-vms.md). Estão disponíveis outras soluções de parceiros certificados. Para saber o que está atualmente disponível, procure no Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Capturar a máquina virtual
1. No [portal do Azure](http://portal.azure.com), **Connect** à máquina virtual. Para obter instruções, consulte [como iniciar sessão a uma máquina virtual com o Windows Server][How to sign in to a virtual machine running Windows Server].
2. Abra uma janela de linha de comandos como administrador.
3. Altere o diretório para `%windir%\system32\sysprep`, e, em seguida, execute o sysprep.exe.
4. O **ferramenta de preparação de sistema** é apresentada a caixa de diálogo. Faça o seguinte:

   * No **ação de limpeza de sistema**, selecione **introduza sistema Out-of-Box experiência (OOBE)** e certifique-se de que **Generalize** está marcada. Para obter mais informações sobre como utilizar o Sysprep, consulte [como Sysprep de utilização: uma introdução][How to Use Sysprep: An Introduction].
   * No **as opções de encerramento**, selecione **encerramento**.
   * Clique em **OK**.

   ![Executar o Sysprep](./media/capture-image/SysprepGeneral.png)
5. Sysprep será encerrado a máquina virtual, que altera o estado da máquina virtual no portal do Azure para **parado**.
6. No portal do Azure, clique em **máquinas virtuais (clássicas)** e selecione a máquina virtual que pretende capturar. O **imagens da VM (clássica)** grupo está listado em **computação** quando visualiza **mais serviços**.

7. Na barra de comandos, clique em **capturar**.

   ![Capturar a máquina virtual](./media/capture-image/CaptureVM.png)

   O **capturar a Máquina Virtual** é apresentada a caixa de diálogo.

8. No **nome da imagem**, escreva um nome para a nova imagem. No **etiqueta da imagem**, escreva uma etiqueta para a nova imagem.

9. Clique em **executei o Sysprep na máquina virtual**. Esta caixa de verificação refere-se para as ações com o Sysprep nos passos 3 a 5. Uma imagem _tem_ generalizado ao executar o Sysprep antes de adicionar uma imagem do Windows Server para o conjunto de imagens personalizadas.

10. Uma vez concluída a captura, a nova imagem ficarem disponível no **Marketplace**, no **computação**, **imagens da VM (clássica)** contentor.

    ![Captura de imagens com êxito](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Passos seguintes
A imagem está pronta para ser utilizada para criar máquinas virtuais. Para efetuar este procedimento, irá criar uma máquina virtual, selecionando o **mais serviços** item de menu na parte inferior do menu de serviços, em seguida, **imagens da VM (clássica)** no **computação** grupo. Para obter instruções, consulte [criar uma máquina virtual a partir de uma imagem](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
