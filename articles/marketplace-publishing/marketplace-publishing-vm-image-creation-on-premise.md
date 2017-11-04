---
title: "Criar uma imagem de máquina virtual no local para o Azure Marketplace | Microsoft Docs"
description: Compreender e executar os passos para criar uma imagem VM no local e implementar no Azure Marketplace para outras pessoas para comprar.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 8f6b9a9293dc149586e6e5fd55028170ea825b07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Desenvolver uma imagem de máquina virtual no local para o Azure Marketplace
Recomendamos vivamente que desenvolver Azure discos rígidos virtuais (VHDs) diretamente na nuvem utilizando o protocolo de ambiente de trabalho remoto. No entanto, se necessário, é possível transferir um VHD e desenvolvê-lo utilizando a infraestrutura no local.  

Para o desenvolvimento no local, tem de transferir o sistema operativo VHD da VM criada. Estes passos seriam ocorrer como parte do passo 3.3, acima.  

## <a name="download-a-vhd-image"></a>Transferir uma imagem VHD
### <a name="locate-a-blob-url"></a>Localizar um URL do blob
Para transferir o VHD, de localizar primeiro o URL do blob para o disco do sistema operativo.

Localizar o URL do blob da nova [portal do Microsoft Azure](https://portal.azure.com):

1. Aceda a **procurar** > **VMs**e, em seguida, selecione a VM implementada.
2. Em **configurar**, selecione o **discos** mosaico, que abre o painel de discos.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Selecione o **disco do SO**, que abre outro painel que apresenta as propriedades de disco, incluindo a localização do VHD.
4. Copie este URL do blob.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Agora, elimine a VM implementada sem eliminar os discos de cópia de segurança. Também pode parar a VM em vez de eliminá-lo. Não transferir o VHD de sistema operativo quando a VM está em execução.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Transferir um VHD
Depois de já conhece o URL do blob, pode transferir o VHD utilizando o [portal do Azure](http://manage.windowsazure.com/) ou PowerShell.  

> [!NOTE]
> No momento da criação deste guia, a funcionalidade para transferir um VHD ainda não está presente no novo portal do Microsoft Azure.  
> 
> 

**Transferir o VHD de sistema operativo através de atual [portal do Azure](http://manage.windowsazure.com/)**

1. Se não o fez, já, inicie sessão no portal do Azure.
2. Clique em de **armazenamento** separador.
3. Selecione a conta de armazenamento no qual está armazenado o VHD.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Esta ação apresenta as propriedades de conta de armazenamento. Selecione o **contentores** separador.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Selecione o contentor no qual o VHD é armazenado. Por predefinição, quando criado no portal, o VHD é armazenado num contentor de VHD.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Selecione o VHD de sistema operativo correto comparando o URL para o guardou.
7. Clique em **Transferir**.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Transferir um VHD com o PowerShell
Além de utilizar o portal do Azure, pode utilizar o [guardar-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) cmdlet para transferir o VHD de sistema operativo.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Por exemplo, guardar-AzureVhd-origem "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" - LocalFilePath "C:\Users\Administrator\Desktop\baseimagevm.vhd" - StorageKey<String>

> [!NOTE]
> **Guardar-AzureVhd** também tem um **NumberOfThreads** opção que pode ser utilizada para aumentar o paralelismo para tornar a melhor utilização de largura de banda disponível para transferência.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Carregar o VHD para uma conta de armazenamento do Azure
Se preparou VHDs no local, terá de carregá-los para uma conta de armazenamento no Azure. Este passo ocorre depois de criar o VHD no local, mas antes de obter a certificação para a imagem VM.

### <a name="create-a-storage-account-and-container"></a>Criar uma conta de armazenamento e um contentor
Recomendamos que os VHDs ser carregada para uma conta de armazenamento numa região nos Estados Unidos. Todos os VHDs para um único SKU devem ser colocados num contentor único dentro de uma única conta de armazenamento.

Para criar uma conta de armazenamento, pode utilizar o [portal do Microsoft Azure](https://portal.azure.com/), PowerShell ou o Linux ferramenta da linha de comandos.  

**Criar uma conta do storage a partir do portal do Microsoft Azure**

1. Clique em **Novo**.
2. Selecione **armazenamento**.
3. Preencha o nome da conta de armazenamento e, em seguida, selecione uma localização.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Clique em **Criar**.
5. O painel para a conta de armazenamento criada deve ser aberto. Se não, selecione **procurar** > **contas do Storage**. No painel de conta de armazenamento, selecione a conta de armazenamento criada.
6. Selecione **contentores**.
   
   ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. No painel de contentores, selecione **adicionar**e, em seguida, introduza um nome de contentor e as permissões de contentor. Selecione **privada** para permissões do contentor.

> [!TIP]
> Recomendamos que crie um contentor por SKU que estiver a planear a publicação.
> 
> 

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Criar uma conta de armazenamento utilizando o PowerShell
Com o PowerShell, criar uma conta de armazenamento utilizando o [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) cmdlet.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Em seguida, pode criar um contentor dentro dessa conta de armazenamento utilizando o [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) cmdlet.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Os comandos partem do princípio de que o contexto de conta de armazenamento atual já foi definido no PowerShell.   Consulte [configurar o Azure PowerShell](marketplace-publishing-powershell-setup.md) para obter mais detalhes sobre a configuração do PowerShell.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Criar uma conta de armazenamento utilizando a ferramenta da linha de comandos para Mac e Linux
> De [ferramenta da linha de comandos do Linux](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), criar uma conta de armazenamento da seguinte forma.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Crie um contentor da seguinte forma.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Carregar um VHD
Depois da conta de armazenamento e o contentor são criados, pode carregar os VHDs preparadas. Pode utilizar o PowerShell, a ferramenta de linha de comandos do Linux ou outras ferramentas de gestão de armazenamento do Azure.

### <a name="upload-a-vhd-via-powershell"></a>Carregar um VHD através do PowerShell
Utilize o [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) cmdlet.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Carregar um VHD, utilizando a ferramenta de linha de comandos para Mac e Linux
Com o [ferramenta da linha de comandos do Linux](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), utilize o seguinte: criar a imagem de vm do azure <image name> – localização <Location of the data center> – SO Linux<LocationOfLocalVHD>

## <a name="see-also"></a>Consultar também
* [Criar uma imagem de máquina virtual para o Marketplace](marketplace-publishing-vm-image-creation.md)
* [Configurar o Azure PowerShell](marketplace-publishing-powershell-setup.md)

