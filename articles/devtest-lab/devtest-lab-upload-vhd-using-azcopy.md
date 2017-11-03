---
title: Carregar o ficheiro VHD para o Azure DevTest Labs utilizando o AzCopy | Microsoft Docs
description: "Carregar o ficheiro VHD a conta de armazenamento do laboratório utilizando o AzCopy"
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
ms.openlocfilehash: a4f43354740d9f17570932b0b9c753f46d67dc33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Carregar o ficheiro VHD a conta de armazenamento do laboratório utilizando o AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os ficheiros VHD podem ser utilizados para criar imagens personalizadas, que são utilizadas para aprovisionar as máquinas virtuais. Os seguintes passos guiá-lo utilizando o utilitário de linha de comandos do AzCopy para carregar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de carregar o ficheiro VHD, o [passos secção](#next-steps) lista alguns artigos que mostram como criar uma imagem personalizada do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs no Azure, consulte [sobre discos e VHD para as máquinas virtuais](../virtual-machines/linux/about-disks-and-vhds.md)

> [!NOTE] 
>  
> O AzCopy é um utilitário da linha de comandos só de Windows.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos guiá-lo através do carregar um ficheiro VHD para utilizar o Azure DevTest Labs [AzCopy](http://aka.ms/downloadazcopy). 

1. Obter o nome do laboratório conta do storage no portal do Azure:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel do laboratório, selecione **configuração**. 

1. No laboratório **configuração** painel, selecione **imagens personalizadas (VHDs)**.

1. No **imagens personalizadas** painel, selecione **+ adicionar**. 

1. No **imagem personalizada** painel, selecione **VHD**.

1. No **VHD** painel, selecione **carregar um VHD com o PowerShell**.

    ![Carregar o VHD com o PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. O **carregar uma imagem através do PowerShell** painel mostra uma chamada para o **Add-AzureVhd** cmdlet. O primeiro parâmetro (*destino*) contém o URI para um contentor do blob (*carrega*) no seguinte formato:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Anote o URI completo porque é utilizado em passos posteriores.

1. Carregar o ficheiro VHD utilizando o AzCopy:
 
1. [Transfira e instale a versão mais recente do AzCopy](http://aka.ms/downloadazcopy).

1. Abra uma janela de comandos e navegue para o diretório de instalação do AzCopy. Opcionalmente, pode adicionar a localização de instalação do AzCopy para o caminho do sistema. Por predefinição, o AzCopy é instalado ao diretório seguinte:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Utilizar a conta chave e BLOBs de contentor de armazenamento URI, execute o seguinte comando na linha de comandos. O *vhdFileName* . o valor tem de ser entre aspas. O processo de carregamento de um ficheiro VHD pode ser demorado, consoante o tamanho do ficheiro VHD e a velocidade da ligação.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs de um ficheiro VHD utilizando o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um ficheiro VHD utilizando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)