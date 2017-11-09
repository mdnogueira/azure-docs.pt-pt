---
title: Criar e carregar uma imagem de VM com o Powershell | Microsoft Docs
description: "Saiba como criar e carregar uma imagem do Windows Server generalizada (VHD) com o modelo de implementação clássica e o Azure Powershell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: c2540120bcb1eca9f4ba62c7dbc0675343bf4f99
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Criar e carregar um VHD do Windows Server para o Azure
Este artigo mostra como carregar as suas próprias imagens VM generalizada como um disco rígido virtual (VHD), pelo que pode ser utilizado para criar máquinas virtuais. Para obter mais detalhes sobre discos e VHDs no Microsoft Azure, consulte [sobre discos e VHD para as máquinas virtuais](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Também pode [carregar](../upload-generalized-managed.md) uma máquina virtual utilizando o modelo Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* **Uma subscrição do Azure** -se não tiver uma, pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[Microsoft Azure PowerShell](/powershell/azure/overview)**  -tem o módulo PowerShell do Microsoft Azure instalado e configurado para utilizar a sua subscrição.
* **A. Ficheiro VHD** - Windows suportados armazenadas num ficheiro. vhd e ligada a uma máquina virtual de sistema operativo. Verifique se as funções de servidor em execução no VHD são suportadas pelo Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

    > [!IMPORTANT]
    > O formato VHDX não é suportado no Microsoft Azure. Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o [Convert-VHD cmdlet](http://technet.microsoft.com/library/hh848454.aspx). Para obter mais informações, consulte este [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Passo 1: Prep o VHD
Antes de carregar o VHD para o Azure, tem de ser generalizado utilizando a ferramenta Sysprep. Isto prepara o VHD para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [como Sysprep de utilização: uma introdução](http://technet.microsoft.com/library/bb457073.aspx). Cópia de segurança da VM antes de executar o Sysprep.

Da máquina virtual que o sistema operativo foi instalado, execute o seguinte procedimento:

1. Inicie sessão no sistema operativo.
2. Abra uma janela de linha de comandos como administrador. Altere o diretório para **%windir%\system32\sysprep**e, em seguida, execute `sysprep.exe`.

    ![Abra uma janela da linha de comandos](./media/createupload-vhd/sysprep_commandprompt.png)
3. O **ferramenta de preparação de sistema** é apresentada a caixa de diálogo.

   ![Iniciar o Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. No **ferramenta de preparação de sistema**, selecione **introduza sistema fora de caixa de experiência de primeira execução (OOBE)** e certifique-se de que **Generalize** está marcada.
5. No **as opções de encerramento**, selecione **encerramento**.
6. Clique em **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Passo 2: Criar uma conta do storage e um contentor
Necessita de uma conta de armazenamento no Azure, para que tenha um local para carregar o ficheiro. vhd. Este passo mostra como criar uma conta ou obter as informações que precisa de uma conta existente. Substitua as variáveis no &lsaquo; Retos &rsaquo; pelas suas informações.

1. Iniciar sessão

    ```powershell
    Add-AzureAccount
    ```

2. Defina a sua subscrição do Azure.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Crie uma nova conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo, 3 a 24 carateres. O nome pode ser qualquer combinação de letras e números. Terá também de especificar uma localização, como "Leste EUA"

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Defina a nova conta de armazenamento como predefinição.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Crie um novo contentor.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>Passo 3: Carregar o ficheiro. vhd
Utilize o [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd) para carregar o VHD.

A partir da janela do PowerShell do Azure que utilizou no passo anterior, escreva o seguinte comando e substitua as variáveis no &lsaquo; Retos &rsaquo; pelas suas informações.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Passo 4: Adicione a imagem à sua lista de imagens personalizadas
Utilize o [adicionar AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) cmdlet para adicionar a imagem à lista de imagens personalizadas.

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Passos seguintes
Agora, pode [criar uma VM personalizada](createportal.md) utilizando a imagem carregou.
