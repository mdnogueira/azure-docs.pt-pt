---
title: Instale o PowerShell para a pilha do Azure | Microsoft Docs
description: Saiba como instalar o PowerShell para a pilha do Azure.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sngun
ms.openlocfilehash: ae489cfab1effca8fa33c4e2693b8b5229eda9c5
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="install-powershell-for-azure-stack"></a>Instale o PowerShell para a pilha do Azure  

Pilha do Azure compatíveis módulos do PowerShell do Azure são necessários para funcionar com a pilha do Azure. Neste guia, iremos orientá-lo pelos passos necessários para instalar o PowerShell para a pilha do Azure. Pode utilizar os passos descritos neste artigo do Kit de desenvolvimento de pilha do Azure ou de um cliente externo baseado no Windows, se estiver ligado através de VPN.

Este artigo descreve em pormenor as instruções para instalar o PowerShell para a pilha do Azure. No entanto, se pretender instalar e configurar o PowerShell rapidamente, pode utilizar o script que é fornecido no tópico "Começar a trabalhar com o PowerShell". 

> [!NOTE]
> Os seguintes passos necessitam do PowerShell 5.0. Para verificar a sua versão, execute $PSVersionTable.PSVersion e comparar a versão "Principal".

Comandos do PowerShell para a pilha do Azure são instalados através da galeria do PowerShell. Para o repositório de PSGallery regiser, abra uma sessão do PowerShell elevada do kit de desenvolvimento ou de um cliente externo baseado no Windows se estiverem ligados através de VPN e execute o seguinte comando:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Desinstalar versões existentes do PowerShell

Antes de instalar a versão necessária, certifique-se de que desinstalar quaisquer módulos do PowerShell do Azure existentes. Pode desinstalá-las utilizando um dos seguintes dois métodos:

* Para desinstalar os módulos do PowerShell existentes, inicie sessão para o kit de desenvolvimento ou para o cliente externo baseados em Windows se estiver a planear estabelecer uma ligação VPN. Feche todas as sessões ativas do PowerShell e execute o seguinte comando: 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Inicie sessão para o kit de desenvolvimento ou para o cliente externo baseados em Windows se estiver a planear estabelecer uma ligação VPN. Eliminar todas as pastas que começam por "Azure" do `C:\Program Files (x86)\WindowsPowerShell\Modules` e `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` pastas. Eliminar estas pastas remove quaisquer módulos do PowerShell existentes dos âmbitos de utilizador "global" e "AzureStackAdmin". 

As secções seguintes descrevem os passos necessários para instalar o PowerShell para a pilha do Azure. PowerShell pode ser instalado na pilha do Azure que manipula no ligado, ligado parcialmente ou um cenário de desligado. 

## <a name="install-powershell-in-a-connected-scenario"></a>Instale o PowerShell num cenário ligado 

Azure pilha compatíveis AzureRM os módulos são instalados através de perfis de versão de API. Pilha do Azure requer o **2017-03-09-perfil** perfil de versão de API, o que está disponível ao instalar o módulo de AzureRM.Bootstrapper. Para mais informações sobre perfis de versão de API e os cmdlets fornecidos pelo-las, consulte o [gerir perfis de versão de API](azure-stack-version-profiles.md). Para além de módulos AzureRM, deve também instalar os módulos do PowerShell de pilha específicos do Azure. Execute o seguinte script do PowerShell para instalar estes módulos na sua estação de trabalho de desenvolvimento:

> [!IMPORTANT]
> A versão do módulo do PowerShell AzureRM 1.2.11 vem com uma lista de alterações de última hora. Para atualizar a partir de 1.2.10 versão, consulte o guia de migração em [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

Para confirmar a instalação, execute o seguinte comando:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  Se a instalação foi bem-sucedida, os módulos AzureRM e AzureStack são apresentados no resultado.

## <a name="install-powershell-in-a-disconnected-or-in-a-partially-connected-scenario"></a>Instalar o PowerShell num desligado ou um cenário parcialmente ligado

Num cenário de desligado, tem de transferir primeiro os módulos do PowerShell para uma máquina que tenha acesso à internet e, em seguida, transfere-os para o Kit de desenvolvimento de pilha do Azure para a instalação.

> [!IMPORTANT]
> A versão do módulo do PowerShell AzureRM 1.2.11 vem com uma lista de alterações de última hora. Para atualizar a partir de 1.2.10 versão, consulte o guia de migração em [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

1. Inicie sessão computador onde tem conectividade internet e utilize o seguinte script de transferir o AzureRM e pacotes de AzureStack no seu computador local:

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11 
   ```

2. Copie os pacotes transferidos através de um dispositivo USB.

3. Inicie sessão para o kit de desenvolvimento e copie os pacotes do dispositivo USB para uma localização no kit de desenvolvimento. 

4. Agora tem de registar nesta localização como o repositório de predefinido e instalar os módulos AzureRM e AzureStack deste repositório:

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>Passos seguintes

* [Descarregar as ferramentas de pilha do Azure a partir do GitHub](azure-stack-powershell-download.md)
* [Configurar o Azure pilha ambiente do utilizador do PowerShell](azure-stack-powershell-configure-user.md)  
* [Gerir perfis de versão de API na pilha do Azure](azure-stack-version-profiles.md)  
