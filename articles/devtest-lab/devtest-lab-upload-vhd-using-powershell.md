---
title: Carregar o ficheiro VHD para o Azure DevTest Labs com o PowerShell | Microsoft Docs
description: "Carregar ficheiro VHD para a conta de armazenamento do laboratório com o PowerShell"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: 3c43ef77b8fa10cd6dbd726968264f32f7a3dd0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Carregar ficheiro VHD para a conta de armazenamento do laboratório com o PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os ficheiros VHD podem ser utilizados para criar imagens personalizadas, que são utilizadas para aprovisionar as máquinas virtuais. Os seguintes passos guiá-lo através do PowerShell para carregar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de carregar o ficheiro VHD, o [passos secção](#next-steps) lista alguns artigos que mostram como criar uma imagem personalizada do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs no Azure, consulte [sobre discos e VHD para as máquinas virtuais](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos guiá-lo através do carregar um ficheiro VHD para o Azure DevTest Labs através do PowerShell. 

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel do laboratório, selecione **configuração**. 

1. No laboratório **configuração** painel, selecione **imagens personalizadas (VHDs)**.

1. No **imagens personalizadas** painel, selecione **+ adicionar**. 

1. No **imagem personalizada** painel, selecione **VHD**.

1. No **VHD** painel, selecione **carregar um VHD com o PowerShell**.

    ![Carregar o VHD com o PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. No **carregar uma imagem através do PowerShell** painel, copie o script de PowerShell gerado para um editor de texto.

1. Modificar o **LocalFilePath** parâmetro o **Add-AzureVhd** cmdlet para apontar para a localização do ficheiro VHD que pretende carregar.

1. Na linha de comandos do PowerShell, execute o **Add-AzureVhd** cmdlet (com a modificação **LocalFilePath** parâmetro).

> [!WARNING] 
> 
> O processo de carregamento de um ficheiro VHD pode ser demorado, consoante o tamanho do ficheiro VHD e a velocidade da ligação.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs de um ficheiro VHD utilizando o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um ficheiro VHD utilizando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
