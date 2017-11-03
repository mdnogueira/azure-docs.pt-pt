---
title: "Transmissão de credenciais para o Azure utilizando o DSC | Microsoft Docs"
description: "Obter uma descrição geral em segurança a ser transmitidos credenciais para máquinas virtuais do Azure utilizando a configuração de estado pretendido do PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
ms.openlocfilehash: acd768c0219ec23c0453a65c575faf5213d9c616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Transmissão de credenciais para o processador de extensão de DSC do Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Este artigo abrange a extensão de configuração de estado pretendido do Azure. Uma descrição geral do processador de extensão de DSC pode ser encontrada em [introdução para o processador de extensão de configuração de estado pretendido do Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="passing-in-credentials"></a>Transmissão de credenciais
Como parte do processo de configuração, pode precisar de configurar contas de utilizador, aceder aos serviços, ou instale um programa num contexto de utilizador. Para fazer tudo isto, terá de fornecer credenciais. 

DSC permite parametrizadas configurações que credenciais são transmitidas para a configuração e armazenadas em segurança em ficheiros MOF. O processador de extensão do Azure simplifica a gestão de credenciais ao fornecer uma gestão automática de certificados. 

Considere o seguinte script de configuração de DSC que cria uma conta de utilizador local com a palavra-passe especificada:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

É importante incluir *localhost nó* como parte da configuração. Se esta instrução está em falta, os seguintes passos não funcionam conforme especificamente procura o processador de extensão para a instrução de localhost de nó. Também é importante incluir a typecast *[PsCredential]*, como a extensão para encriptar a credencial é acionado deste tipo específico. 

Publicar este script para o blob storage:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Definir a extensão de DSC do Azure e forneça a credencial:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Como as credenciais são protegidas
Executar este código solicita uma credencial. Assim que é fornecido, este é armazenado na memória brevemente. Quando está publicado com `Set-AzureVmDscExtension` cmdlet, são transmitidos através de HTTPS para a VM, onde do Azure armazena-encriptado no disco, utilizando o certificado da VM local. Em seguida, brevemente é desencriptado na memória e encriptado novamente transmiti-lo no DSC.

Este comportamento é diferente do [utilizar configurações seguras sem o processador de extensão](https://msdn.microsoft.com/powershell/dsc/securemof). O ambiente do Azure fornece uma forma para transmitir dados de configuração de forma segura através de certificados. Ao utilizar o processador de extensão de DSC, é necessário fornecer $CertificatePath ou um $CertificateID / entrada $Thumbprint ConfigurationData.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o processador de extensão de DSC do Azure, consulte [introdução para o processador de extensão de configuração de estado pretendido do Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Examine o [modelo Azure Resource Manager para a extensão de DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre o PowerShell DSC, [visitar o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Para encontrar funcionalidades adicionais que pode ser geridas com o PowerShell DSC, [procurar na galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) mais recursos de DSC.

