---
title: Adicionar uma imagem de VM a pilha do Azure | Microsoft Docs
description: "Adicione a Windows ou Linux VM imagem personalizada sua organização para os inquilinos utilizarem."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 54b6a6984e66f32642336f4ea5e1e9f4ec9d03f3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Disponibilizar uma imagem de máquina virtual personalizada na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Na pilha do Azure, os operadores podem disponibilizar imagens da máquina virtual personalizada para os seus utilizadores. Essas imagens podem ser referenciadas pelas modelos Azure Resource Manager, ou pode adicioná-los para a IU do Azure Marketplace como um item do Marketplace. 

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Adicionar uma imagem VM no Marketplace utilizando o PowerShell

Execute os seguintes pré-requisitos a partir de [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou a partir de um baseados em Windows clientes externos, se estiver [ligado através de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Instale o PowerShell para a pilha do Azure](azure-stack-powershell-install.md).  

2. Transferir o [ferramentas necessárias para trabalhar com a pilha de Azure](azure-stack-powershell-download.md).  

3. Preparar uma imagem de disco rígido virtual sistema operativo Windows ou Linux no formato VHD (não utilize o formato VHDX).
   
   * Para as imagens do Windows, para obter instruções sobre como preparar a imagem, consulte [carrega uma imagem de VM do Windows Azure para implementações do Resource Manager](../virtual-machines/windows/upload-generalized-managed.md).
   * Para imagens de Linux, consulte [máquinas virtuais Linux de implementar no Azure pilha](azure-stack-linux.md). Conclua os passos para preparar a imagem ou utilizar uma imagem do Linux de pilha do Azure existente, tal como descrito no artigo.  

Para adicionar a imagem para a pilha do Azure Marketplace, conclua os seguintes passos:

1. Importe os módulos de ligar e ComputeAdmin:
   
   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ``` 

2. Inicie sessão no seu ambiente de pilha do Azure. Execute um dos seguintes scripts, dependendo se implementou o ambiente de pilha do Azure utilizando o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS). (Substitui o Azure AD `tenantName`, `GraphAudience` ponto final, e `ArmEndpoint` valores para refletir a configuração do seu ambiente.)

    * **Azure Active Directory**. Utilize o seguinte cmdlet:

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

   * **Serviços de Federação do Active Directory**. Utilize o seguinte cmdlet:
    
        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
        Add-AzureRMEnvironment `
          -Name "AzureStackAdmin" `
          -ArmEndpoint $ArmEndpoint

        Set-AzureRmEnvironment `
          -Name "AzureStackAdmin" `
          -GraphAudience $GraphAudience `
          -EnableAdfsAuthentication:$true

        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin 

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID 
        ```
    
3. Adicione a imagem VM ao invocar o `Add-AzsVMImage` cmdlet. No `Add-AzsVMImage` cmdlet, especifique `osType` como Windows ou Linux. Inclua o publicador, oferta, SKU e versão para a imagem VM. Para obter informações sobre parâmetros permitidas, consulte [parâmetros](#parameters). Os parâmetros são utilizados por modelos Azure Resource Manager para fazer referência a imagem VM. O exemplo seguinte invoca o script:
     
  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

O comando faz o seguinte:

* Autentica para o ambiente de pilha do Azure.
* Carrega o VHD local para uma conta de armazenamento temporário recentemente criado.
* Adiciona a imagem VM para o repositório de imagens VM.
* Cria um item do Marketplace.

Para verificar que o comando foi executado com êxito, no portal, aceda ao Marketplace. Certifique-se de que a imagem VM está disponível no **máquinas virtuais** categoria.

![Imagem de VM adicionada com êxito](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-by-using-powershell"></a>Remover uma imagem de VM com o PowerShell

Quando já não necessita da imagem de máquina virtual que carregou, pode eliminar do Marketplace utilizando o cmdlet seguinte:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Parâmetros

| Parâmetro | Descrição |
| --- | --- |
| **fabricante** |O segmento de nome do publicador da imagem VM que os utilizadores utilizam quando implementam a imagem. Um exemplo é **Microsoft**. Não inclua um espaço ou outros carateres especiais neste campo. |
| **oferta** |O segmento de nome de oferta da imagem VM que os utilizadores utilizam quando implementam a imagem VM. Um exemplo é **WindowsServer**. Não inclua um espaço ou outros carateres especiais neste campo. |
| **SKU** |O segmento de nome SKU da imagem de VM que os utilizadores utilizam quando implementam a imagem VM. Um exemplo é **Datacenter2016**. Não inclua um espaço ou outros carateres especiais neste campo. |
| **versão** |A versão da imagem de VM que os utilizadores utilizam quando implementam a imagem VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Não inclua um espaço ou outros carateres especiais neste campo. |
| **osType** |O osType da imagem tem de ser um **Windows** ou **Linux**. |
| **osDiskLocalPath** |O caminho local para o disco de SO VHD que estiver a carregar como uma imagem VM para a pilha do Azure. |
| **dataDiskLocalPaths** |Uma matriz opcional local caminhos para discos de dados que podem ser também carregados como parte da imagem de VM. |
| **CreateGalleryItem** |Sinalizador booleano que determina se deve criar um item no Marketplace. Por predefinição, está definido como **verdadeiro**. |
| **título** |O nome a apresentar do item do Marketplace. Por predefinição, está definido para o `Publisher-Offer-Sku` valor da imagem de VM. |
| **Descrição** |A descrição do item do Marketplace. |
| **localização** |A localização onde a imagem VM deve ser publicada. Por predefinição, este valor é definido como **local**.|
| **osDiskBlobURI** |(Opcional) Este script também aceita um URI de armazenamento de BLOBs para `osDisk`. |
| **dataDiskBlobURIs** |(Opcional) Este script também aceita uma matriz de armazenamento de BLOBs URIs para adição de discos de dados para a imagem. |

## <a name="add-a-vm-image-through-the-portal"></a>Adicionar uma imagem VM através do portal

> [!NOTE]
> Com este método, tem de criar o item do Marketplace em separado.

As imagens devem ser capazes de ser referenciado por um URI de armazenamento de Blobs. Preparar uma imagem de sistema operativo Windows ou Linux no formato VHD (não VHDX) e, em seguida, carregue a imagem para uma conta de armazenamento no Azure ou a pilha do Azure. Se a imagem já está carregada para o armazenamento de Blobs do Azure ou a pilha do Azure, pode ignorar o passo 1.

1. [Carregar uma imagem de VM do Windows Azure para implementações do Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, para uma imagem do Linux, siga as instruções descritas em [máquinas virtuais Linux de implementar no Azure pilha](azure-stack-linux.md). Antes de carregar a imagem, é importante a ter em consideração os seguintes fatores:

   * É mais eficiente para carregar uma imagem para pilha Blob storage do Azure que para o armazenamento de Blobs do Azure porque demora menos tempo para enviar a imagem para o repositório de imagens de pilha do Azure. 
   
   * Ao carregar o [imagem de VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), certifique-se de que substitui o **iniciar sessão no Azure** passo com o [configurar o ambiente de PowerShell o operador de pilha do Azure](azure-stack-powershell-configure-admin.md) passo.  

   * Tome nota do Blob storage URI onde carregar a imagem. Armazenamento de Blobs do URI tem o seguinte formato:  *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* . vhd.

   * Para tornar o blob anonimamente acessível, vá para o contentor de blob de conta de armazenamento onde a imagem VM VHD foi carregada. Selecione **Blob**e, em seguida, selecione **política de acesso**. Opcionalmente, pode em vez disso, gerar uma assinatura de acesso partilhado do contentor e incluí-la como parte do URI de blob.

   ![Ir para blobs de conta de armazenamento](./media/azure-stack-add-vm-image/image1.png)

   ![Acesso de blob de conjunto para público](./media/azure-stack-add-vm-image/image2.png)

2. Inicie sessão no Azure pilha como operador. No menu, selecione **mais serviços** > **fornecedores de recursos**. Em seguida, selecione **computação** > **imagens da VM** > **adicionar**.

3. Em **adicionar uma imagem de VM**, introduza o publicador, oferta, SKU e versão da imagem de máquina virtual. Consulte estes segmentos de nome para a imagem VM em modelos do Resource Manager. Certifique-se de que seleciona o **osType** valor corretamente. Para **URI de Blob do disco OD**, introduza o URI de Blob onde a imagem foi carregada. Em seguida, selecione **criar** para começar a criar a imagem de VM.
   
   ![Begin para criar a imagem](./media/azure-stack-add-vm-image/image4.png)

   Quando a imagem é criada com êxito, o estado de imagem VM mudar para **com êxito**.

4. Para disponibilizar a imagem de máquina virtual mais prontamente para consumo dos utilizadores na IU, é uma boa ideia [criar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Passos seguintes

[Aprovisionar uma máquina virtual](azure-stack-provision-vm.md)