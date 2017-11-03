---
title: "Através de perfis de versão de API na pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre perfis de versão de API na pilha do Azure."
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
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: b9a010409dc7f49333ab188b89280f3ad54816c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gerir perfis de versão de API na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Os perfis de versão de API fornecem uma forma de gerir as diferenças de versão entre o Azure e pilha do Azure. Um perfil de versão de API é um conjunto de módulos do PowerShell de AzureRM com versões de API específicas. Cada plataforma de nuvem tem um conjunto de perfis de versão de API suportados. Por exemplo, pilha do Azure suporta uma versão de perfil com a data específica, tal como **2017-03-09-perfil**, e suporta o Azure o **mais recente** perfil da versão de API. Quando instalar um perfil, são instalados os módulos do PowerShell de AzureRM que correspondem para o perfil especificado.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Instalar o módulo do PowerShell necessário para utilizar perfis de versão de API

O **AzureRM.Bootstrapper** módulo que está disponível através da galeria do PowerShell fornece cmdlets do PowerShell que são necessárias para trabalhar com perfis de versão de API. Utilize o cmdlet seguinte para instalar o módulo de AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
O módulo de AzureRM.Bootstrapper está em pré-visualização; os detalhes e a funcionalidade estão sujeitos a alterações. Para transferir e instalar a versão mais recente deste módulo a partir da galeria do PowerShell, execute o seguinte cmdlet:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Instalar um perfil

Utilize o **instalação AzureRmProfile** cmdlet com o **2017-03-09-perfil** perfil de versão de API para instalar os módulos de AzureRM necessários pela pilha de Azure. Tenha em atenção que os módulos de operador de pilha do Azure não estão instalados com este perfil da versão de API e deve ser instalados separadamente conforme especificado no passo 3 do [instale o PowerShell para Azure pilha](azure-stack-powershell-install.md) artigo.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Instalar e importar módulos num perfil

Utilize o **utilize AzureRmProfile** cmdlet para instalar e importar módulos que estão associados um perfil de versão de API. Pode importar apenas um perfil de versão de API numa sessão do PowerShell. Para importar um perfil de versão de API diferente, tem de abrir uma nova sessão do PowerShell. O cmdlet de utilização-AzureRMProfile executa as seguintes tarefas:  
1. Verifica se os módulos do PowerShell associados ao perfil de versão de API especificado estão instalados no âmbito atual.  
2. Transfere e instala os módulos se não estiver já instalados.   
3. Importa os módulos para a sessão atual do PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Para instalar e importar módulos de AzureRM selecionados a partir de um perfil de versão de API, execute o cmdlet de utilização-AzureRMProfile com o **módulo** parâmetro:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Obter os perfis instalados

Utilize o **Get-AzureRmProfile** cmdlet para obter a lista de perfis disponíveis de versão de API: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Perfis de atualização

Utilize o **atualização AzureRmProfile** cmdlet para atualizar os módulos num perfil de versão de API para a versão mais recente dos módulos que estão disponíveis no PSGallery. É recomendado executar sempre o **atualização AzureRmProfile** cmdlet numa nova sessão do PowerShell para evitar conflitos ao importar os módulos. O cmdlet Update-AzureRmProfile executa as seguintes tarefas:

1. Verifica se as versões mais recentes de módulos estão instaladas no perfil de versão de API especificado para o âmbito atual.  
2. Pede-lhe instalar, se não estiver já instalado.  
3. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Para remover as versões dos módulos anteriormente instaladas antes de atualizar para a versão mais recente, utilize o cmdlet Update-AzureRmProfile juntamente com o **- RemovePreviousVersions** parâmetro:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Este cmdlet é executado as seguintes tarefas:  

1. Verifica se as versões mais recentes de módulos estão instaladas no perfil de versão de API especificado para o âmbito atual.  
2. Remove as versões mais antigas de módulos do perfil de versão de API atual e na sessão atual do PowerShell.  
4. pede-lhe que instale a versão mais recente.  
5. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  
 
## <a name="uninstall-profiles"></a>Desinstalar perfis

Utilize o **desinstalação AzureRmProfile** cmdlet para desinstalar o perfil de versão de API especificada.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Passos seguintes
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o Azure pilha ambiente do utilizador do PowerShell](user/azure-stack-powershell-configure-user.md)  
