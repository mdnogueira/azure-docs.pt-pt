---
title: Descarregar as ferramentas de pilha do Azure a partir do GitHub | Microsoft Docs
description: "Saiba como transferir as ferramentas necessárias para trabalhar com a pilha do Azure."
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
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 69ca030d1b7601df424fa9446e1d194a3f6bd50a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Descarregar as ferramentas de pilha do Azure a partir do GitHub

Ferramentas de AzureStack é um repositório do GitHub que aloja os módulos do PowerShell que pode utilizar para gerir e implementar recursos com pilha do Azure. Pode transferir e utilizar estes módulos do PowerShell para o Kit de desenvolvimento de pilha do Azure ou para um cliente externo baseado em windows, se estiver a planear estabelecer conectividade VPN. Para obter estas ferramentas, clone o repositório do GitHub ou transferir a pasta Ferramentas AzureStack. 

Para clonar o repositório, transfira [Git](https://git-scm.com/download/win) para Windows, abra uma janela de linha de comandos e execute o seguinte script:

```PowerShell
# Change directory to the root directory 
cd \

# clone the repository
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory
cd AzureStack-Tools
```

Para transferir a pasta Ferramentas, execute o seguinte script:

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Funcionalidades fornecidas pelos módulos

O repositório de ferramentas AzureStack contém módulos do PowerShell que suportam as seguintes funcionalidades para a pilha do Azure:  

| Funcionalidade | Descrição | Quem pode utilizar este módulo? |
| --- | --- | --- |
| [Capacidades de nuvem](azure-stack-validate-templates.md) | Utilize este módulo para obter as capacidades de nuvem de uma nuvem. Por exemplo, pode obter as capacidades de nuvem, tais como a versão de API, os recursos do Azure Resource Manager, etc. as extensões de VM para a pilha do Azure e nuvens do Azure utilizando este módulo. | Os administradores de nuvem e os utilizadores. |
| [Política do Gestor de recursos para a pilha do Azure](azure-stack-policy-module.md) | Utilize este módulo para configurar uma subscrição do Azure ou um grupo de recursos do Azure com a disponibilidade de serviço e controlo de versões do mesma como pilha do Azure. | Os administradores de nuvem e de utilizadores |
| [Ligar a pilha do Azure](azure-stack-connect-azure-stack.md) | Utilize este módulo para ligar a uma instância de pilha do Azure através do PowerShell bem como para configurar a conectividade VPN pilha do Azure. | Os administradores de nuvem e de utilizadores |
| [A validação do modelo](azure-stack-validate-templates.md) | Utilize este módulo para verificar se um existente ou um novo modelo pode ser implementado com pilha do Azure. | Os administradores de nuvem e de utilizadores |


## <a name="next-steps"></a>Passos seguintes
* [Configurar o Azure pilha ambiente do utilizador do PowerShell](azure-stack-powershell-configure-user.md)   
* [Ligar ao Kit de desenvolvimento de pilha do Azure através de uma VPN](azure-stack-connect-azure-stack.md)  
