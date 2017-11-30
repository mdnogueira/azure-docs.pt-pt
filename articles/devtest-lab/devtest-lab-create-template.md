---
title: Criar uma imagem personalizada do Azure DevTest Labs a partir de um ficheiro VHD | Microsoft Docs
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs de um ficheiro VHD utilizando o portal do Azure
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: a421327ab8794315005327833b873dc5ebd57e9e
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Criar uma imagem personalizada de um ficheiro VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instruções passo-a-passo

Os seguintes passos guiá-lo a criar uma imagem personalizada de um ficheiro VHD utilizando o portal do Azure:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel do laboratório, selecione **configuração**. 

1. No laboratório **configuração** painel, selecione **imagens personalizadas (VHDs)**.

1. No **imagens personalizadas** painel, selecione **+ adicionar**.

    ![Adicionar a imagem personalizada](./media/devtest-lab-create-template/add-custom-image.png)

1. Introduza o nome da imagem personalizada. Este nome é apresentado na lista de imagens de base, ao criar uma VM.

1. Introduza a descrição da imagem personalizada. Esta descrição é apresentada na lista de imagens de base, ao criar uma VM.

1. Selecione **VHD**.

1. Do **VHD** painel, selecione o ficheiro VHD pretendido.

1. Selecione **OK** para fechar o **VHD** painel.

1. Selecione **configuração do SO**.

1. No **configuração do SO** separador, selecione **Windows** ou **Linux**.

1. Se **Windows** está selecionada, especifique através da caixa de verificação se *Sysprep* tiver sido executado na máquina. 

1. Selecione **OK** para fechar o **configuração do SO** painel.

1. Selecione **OK** para criar a imagem personalizada.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Mensagens de blogue relacionados

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar as imagens personalizadas entre Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Passos seguintes

- [Adicionar uma VM para o laboratório](./devtest-lab-add-vm.md)
