---
title: Como implementar o Windows 10 no Azure com direitos de alojamento de multi-inquilino
description: "Saiba como maximizar as vantagens do Windows Software Assurance para colocar licenças no local do Azure"
services: virtual-machines-windows
documentationcenter: 
author: xujing
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 8/20/2017
ms.author: xujing
ms.openlocfilehash: e3209abd17c7ba3e39a67f834be69f113c27a021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implementar o Windows 10 no Azure com direitos de alojamento de multi-inquilino 
Para clientes com Windows 10 Enterprise E3/E5 por utilizador ou de acesso de ambiente de trabalho virtuais do Windows por utilizador (licenças de subscrição de utilizador ou licenças de subscrição do suplemento do utilizador), o multi-inquilino de alojamento direitos para Windows 10 permite-lhe trazer as suas licenças do Windows 10 para a nuvem e executar máquinas virtuais do Windows 10 no Azure sem pagar outra licença. Para obter mais informações, consulte [de alojamento do multi-inquilino para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Este artigo mostra-lhe implementar o benefício de licenciamento para imagens de ambiente de trabalho do Windows 10. Pode consultar as seguintes opções para [híbrida do Azure utilizar benefícios para imagens do Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implementar a imagem do Windows 10 do Azure Marketplace 
Para implementações de modelo do Powershell, o CLI e o Azure Resource Manager, pode encontrar a imagem do Windows 10 com o seguinte publishername, oferta, sku.

| SO  |      Nome do Editor      |  Oferta | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows 10  | RS2 Pro   |
| N Pro do Windows 10  | MicrosoftWindowsDesktop | Windows 10  | RS2 ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Carregar o Windows 10 VHD para o Azure
Se estiver a carregar um VHD de 10 Windows generalizado, tenha em atenção de que Windows 10 não tem uma conta de administrador incorporada ativada por predefinição. Para ativar a conta de administrador incorporada, incluem o seguinte comando como parte da extensão de Script personalizado.

```powershell
Net user <username> /active:yes
```

É o seguinte fragmento de powershell para marcar todas as contas de administrador como o Active Directory, incluindo o administrador incorporado. Neste exemplo é útil se o nome de utilizador de administrador incorporado é desconhecido.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Para mais informações: 
* [Como carregar o VHD para o Azure](upload-generalized-managed.md)
* [Como preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implementar o Windows 10 com direitos de alojamento multi-inquilino
Certifique-se de que tem [instalado e configurado o Azure PowerShell mais recente](/powershell/azure/overview). Depois de ter preparado o VHD, carrega o VHD para a conta de armazenamento do Azure utilizando o `Add-AzureRmVhd` cmdlet da seguinte forma:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implementar utilizando a implementação de modelos do Azure Resource Manager** dentro os modelos do Resource Manager, um parâmetro adicional para `licenseType` pode ser especificado. Pode ler mais sobre [criação de modelos Azure Resource Manager](../../resource-group-authoring-templates.md). Assim que tiver o VHD carregado para o Azure, edite modelo do Resource Manager para incluir o tipo de licença como parte do fornecedor de computação e implementar o modelo como normal:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**Implementar através do PowerShell** quando implementar a VM do Windows Server através do PowerShell, tem um parâmetro adicional para `-LicenseType`. Assim que tiver o VHD carregado para o Azure, criar uma VM utilizando `New-AzureRmVM` e especifique o tipo de licenciamento da seguinte forma:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Certifique-se de que a VM está a utilizar o benefício de licenciamento
Depois de ter implementado a VM através de qualquer um dos métodos de implementação do PowerShell ou Gestor de recursos, verifique se o tipo de licença com `Get-AzureRmVM` da seguinte forma:
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

O resultado será semelhante ao seguinte exemplo para o Windows 10 com o tipo de licença correta:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Saída Isto contrasta com a seguinte VM implementada sem licenciamento benefício de utilização de híbrida do Azure, tal como uma VM implementada diretamente a partir da galeria do Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Informações adicionais sobre a associação do Azure AD
>[!NOTE]
>Azure Aprovisiona todas as VMs do Windows com a conta de administrador incorporada, que não pode ser utilizada para associar o AAD. Por exemplo, *definições > conta > acesso profissional ou escolar > + Connect* não funcionará. Tem de criar e iniciar sessão como uma segunda conta de administrador para efetuar a adesão mannually do Azure AD. Também pode configurar o Azure AD através de um pacote de aprovisionamento, utilize a ligação é o *passos* secção para saber mais.
>
>

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [configurar VDA para Windows 10](https://docs.microsoft.com/en-us/windows/deployment/vda-subscription-activation)
- Saiba mais sobre [de alojamento do multi-inquilino para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


