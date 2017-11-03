---
title: Descarregar as ferramentas de pilha do Azure a partir do GitHub | Microsoft Docs
description: "Saiba como transferir as ferramentas que são necessárias para trabalhar com a pilha do Azure."
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
ms.openlocfilehash: c0a4f337c055f4b62d986e2a3c3ce7b962aceae9
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Descarregar as ferramentas de pilha do Azure a partir do GitHub

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

**Ferramentas de AzureStack** é um repositório do GitHub que aloja os módulos do PowerShell para gerir e implementar recursos com pilha do Azure. Se estiver a planear estabelecer conectividade VPN, pode transferir estes módulos do PowerShell para o Kit de desenvolvimento de pilha do Azure ou para um cliente externo baseado em Windows. Para obter estas ferramentas, clone o repositório do GitHub ou transferir o **AzureStack ferramentas** pasta. 

Para clonar o repositório, transfira [Git para Windows](https://git-scm.com/download/win), abra uma linha de comandos e execute o seguinte script:

```PowerShell
# Change directory to the root directory. 
cd \

# Clone the repository.
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory.
cd AzureStack-Tools
```

Para transferir a pasta Ferramentas, execute o seguinte script:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funcionalidade fornecida por módulos

O **AzureStack ferramentas** repositório inclui módulos do PowerShell que suportam as seguintes funcionalidades para a pilha do Azure:  

| Funcionalidade | Descrição | Quem pode utilizar este módulo? |
| --- | --- | --- |
| [Capacidades de nuvem](user/azure-stack-validate-templates.md) | Utilize este módulo para obter as capacidades de nuvem de uma nuvem. Por exemplo, ao utilizar este módulo, pode obter as capacidades de nuvem, tais como versão de API e de recursos do Azure Resource Manager. Também pode obter as extensões VM para a pilha do Azure e nuvens do Azure utilizando este módulo. | Os operadores da nuvem e de utilizadores |
| [Administração de computação de pilha do Azure](azure-stack-add-vm-image.md) | Utilize este módulo para adicionar ou remover uma imagem de VM do marketplace pilha do Azure. | Operadores da nuvem |
| [Administração de infraestrutura de pilha do Azure](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | Utilize este módulo para gerir VMs de infraestrutura de pilha do Azure, alertas, atualizações e assim sucessivamente. |  Operadores da nuvem|
| [Política do Gestor de recursos para a pilha do Azure](user/azure-stack-policy-module.md) | Utilize este módulo para configurar uma subscrição do Azure ou um grupo de recursos do Azure com a disponibilidade de serviço e controlo de versões do mesma como pilha do Azure. | Os operadores da nuvem e de utilizadores |
| [Registar com o Azure](azure-stack-register.md) | Utilize este módulo para registar a sua instância do kit de desenvolvimento com o Azure. Após o registo, pode transferir os itens do marketplace a partir do Azure e utilizá-los na pilha do Azure. | Operadores da nuvem |
| [Implementação de pilha do Azure](azure-stack-run-powershell-script.md) | Utilize este módulo para preparar o computador do anfitrião de pilha do Azure para implementar e volte a implementar utilizando a imagem de disco rígido virtual (VHD) de pilha do Azure. | Operadores da nuvem|
| [Ligar a pilha do Azure](azure-stack-connect-powershell.md) | Utilize este módulo para ligar a uma instância de pilha do Azure através do PowerShell bem como para configurar a conectividade VPN pilha do Azure. | Os operadores da nuvem e de utilizadores |
| [Administração de serviço de pilha do Azure](azure-stack-create-offer.md) | Utilize este módulo para criar uma oferta de inquilino predefinido com quotas ilimitadas em toda a computação, armazenamento do Azure, rede e serviços do Cofre de chaves.   | Operadores da nuvem|
| [A validação do modelo](user/azure-stack-validate-templates.md) | Utilize este módulo para verificar se um existente ou um novo modelo pode ser implementado com pilha do Azure. | Os operadores da nuvem e de utilizadores|


## <a name="next-steps"></a>Passos seguintes
* [Configurar o Azure pilha ambiente do utilizador do PowerShell](user/azure-stack-powershell-configure-user.md)   
* [Ligar ao Kit de desenvolvimento de pilha do Azure através de uma VPN](azure-stack-connect-azure-stack.md)  
