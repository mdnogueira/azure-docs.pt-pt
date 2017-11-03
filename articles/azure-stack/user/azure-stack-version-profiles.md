---
title: "Gerir perfis de versão de API no Azure pilha | Microsoft Docs"
description: "Saiba mais sobre perfis de versão de API na pilha do Azure"
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
ms.date: 03/21/2017
ms.author: sngun
ms.openlocfilehash: f986abebaf9117b040c149c10f6b2358bbdc0f44
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gerir perfis de versão de API na pilha do Azure

A funcionalidade de perfis de versão do App Service do Azure fornece uma forma de gerir as diferenças de versão entre o Azure e Azure pilha. Um perfil de versão de API é um conjunto de módulos do PowerShell de AzureRM com versões de API específicas. Cada plataforma de nuvem tem um conjunto de perfis de versão de API suportados. Por exemplo, pilha do Azure suporta uma versão de perfil específico, com a data, tais como **2017-03-09-perfil**, e suporta o Azure o *mais recente* perfil da versão de API. Quando instalar um perfil, são instalados os módulos do PowerShell de AzureRM que correspondem para o perfil especificado.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Instalar o módulo do PowerShell necessário para utilizar perfis de versão de API

O **AzureRM.Bootstrapper** módulo que está disponível através da galeria do PowerShell fornece cmdlets do PowerShell que são necessárias para trabalhar com perfis de versão de API. Utilize o cmdlet seguinte para instalar o **AzureRM.Bootstrapper** módulo:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
O **AzureRM.Bootstrapper** módulo é uma versão de pré-visualização, pelo que os detalhes e a funcionalidade estão sujeitos a alterações. Para transferir e instalar a versão mais recente deste módulo a partir da galeria do PowerShell, execute o seguinte cmdlet:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Instalar um perfil

Utilize o **instalação AzureRmProfile** cmdlet com o **2017-03-09-perfil** perfil de versão de API para instalar os módulos de AzureRM necessários pela pilha de Azure. 

>[!NOTE]
>Os módulos de administrador de nuvem do Azure pilha não estão instalados com este perfil da versão de API. Os módulos de administrador devem ser instalados separadamente conforme especificado no passo 3 do [instale o PowerShell para Azure pilha](azure-stack-powershell-install.md) artigo.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Instalar e importar módulos num perfil

Utilize o **utilize AzureRmProfile** cmdlet para instalar e importar módulos que estão associados um perfil de versão de API. Pode importar apenas um perfil de versão de API numa sessão do PowerShell. Para importar um perfil de versão de API diferente, tem de abrir uma nova sessão do PowerShell. O **utilize AzureRMProfile** cmdlet executa as seguintes tarefas:  
1. Verifica se os módulos do PowerShell associados ao perfil de versão de API especificado estão instalados no âmbito atual.  
2. Transfere e instala os módulos se não estiver já instalados.   
3. Importa os módulos para a sessão atual do PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Para instalar e importar módulos de AzureRM selecionados a partir de um perfil de versão de API, execute o **utilize AzureRMProfile** cmdlet com o *módulo* parâmetro:

```PowerShell
# Installs and imports the Compute, Storage, and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Obter os perfis instalados

Utilize o **Get-AzureRmProfile** cmdlet para obter a lista de perfis disponíveis de versão de API: 

```PowerShell
# Lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# Lists the API version profiles that are installed on your machine.
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Perfis de atualização

Utilize o **atualização AzureRmProfile** cmdlet para atualizar os módulos num perfil de versão de API para a versão mais recente dos módulos que estão disponíveis na galeria do PowerShell. Recomendamos que execute o **atualização AzureRmProfile** cmdlet numa nova sessão do PowerShell para evitar conflitos quando importar módulos. O **atualização AzureRmProfile** cmdlet executa as seguintes tarefas:

1. Verifica se as versões mais recentes dos módulos estão instalados no perfil de versão de API especificado para o âmbito atual.  
2. Pede-lhe para instalar os módulos se não estiver já instalados.  
3. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Para remover as versões dos módulos anteriormente instaladas antes de atualizar para a versão mais recente, utilize o **atualização AzureRmProfile** cmdlet juntamente com o *- RemovePreviousVersions* parâmetro:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Este cmdlet é executado as seguintes tarefas:  

1. Verifica se as versões mais recentes dos módulos estão instalados no perfil de versão de API especificado para o âmbito atual.  
2. Remove as versões anteriores dos módulos do perfil de versão de API atual e na sessão atual do PowerShell.  
3. Pede-lhe que instale a versão mais recente dos módulos.  
4. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  
 
## <a name="uninstall-profiles"></a>Desinstalar perfis

Utilize o **desinstalação AzureRmProfile** cmdlet para desinstalar o perfil de versão de API especificada:

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Passos seguintes
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o Azure pilha ambiente do utilizador do PowerShell](azure-stack-powershell-configure-user.md)  
