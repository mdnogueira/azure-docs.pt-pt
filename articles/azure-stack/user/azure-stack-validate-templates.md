---
title: "Utilizar a validação do modelo para procurar modelos a pilha do Azure | Microsoft Docs"
description: "Verificação de modelos de implementação para a pilha do Azure"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c99e5ebc2612e10f42bddbbd2f1c17d7404305d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Procurar os modelos de pilha do Azure com a validação do modelo

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Pode utilizar a ferramenta de validação do modelo para verificar se o Azure Resource Manager [modelos](azure-stack-arm-templates.md) estão prontos para a pilha do Azure. A ferramenta de validação do modelo está disponível como parte das ferramentas de pilha do Azure. Transferir as ferramentas de pilha do Azure utilizando os passos descritos no [descarregar as ferramentas a partir do GitHub](azure-stack-powershell-download.md) artigo. 

Para validar os modelos, utilize os seguintes módulos do PowerShell e o ficheiro JSON localizado no **TemplateValidator** e **CloudCapabilities** pastas: 

 - AzureRM.CloudCapabilities.psm1 cria um ficheiro JSON de capacidades de nuvem que representam os serviços e as versões numa nuvem, como a pilha do Azure.
 - AzureRM.TemplateValidator.psm1 utiliza um ficheiro JSON de capacidades de nuvem para testar os modelos de implementação na pilha do Azure.
 - AzureStackCloudCapabilities_with_AddOns_20170627.json é um ficheiro de capacidades de nuvem de predefinição.  Pode criar as suas próprias ou utilizar este ficheiro para começar a utilizar. 

Neste tópico, execute a validação contra os modelos e, opcionalmente, criar um ficheiro de capacidades de nuvem.

## <a name="validate-templates"></a>Validar modelos
Nestes passos, validar modelos utilizando o módulo do AzureRM.TemplateValidator PowerShell. Pode utilizar os seus próprios modelos ou validar o [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  Importe o módulo PowerShell do AzureRM.TemplateValidator.psm1:
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  Execute a validação do modelo:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Avisos de validação do modelo ou erros são registados na consola do PowerShell e também são registados para um ficheiro HTML no diretório de origem. Um exemplo da saída de relatório de validação tem o seguinte aspeto:

![relatório de validação de exemplo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parâmetros

| Parâmetro | Descrição | Necessário |
| ----- | -----| ----- |
| TemplatePath | Especifica o caminho para recursivamente localizar modelos do Resource Manager | Sim | 
| TemplatePattern | Especifica o nome dos ficheiros de modelo para fazer corresponder. | Não |
| CapabilitiesPath | Especifica o caminho para o ficheiro JSON de capacidades de nuvem | Sim | 
| IncludeComputeCapabilities | Inclui a avaliação dos recursos IaaS como extensões de VM e tamanhos de VM | Não |
| IncludeStorageCapabilities | Inclui a avaliação dos recursos de armazenamento como tipos SKU | Não |
| Relatório | Especifica o nome do relatório HTML gerado | Não |
| Verboso | Os registos de erros e avisos para a consola do | Não|


### <a name="examples"></a>Exemplos
Neste exemplo valida todas as [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates) transferido localmente e também valida as extensões contra capacidades do Kit de desenvolvimento de pilha do Azure e tamanhos de VM.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>Criar ficheiro de capacidades de nuvem
Os ficheiros transferidos incluem uma predefinição *AzureStackCloudCapabilities_with_AddOns_20170627.json* ficheiro, que descreve as versões de serviço disponíveis no Kit de desenvolvimento de pilha do Azure com os serviços de PaaS instalados.  Se instalar fornecedores de recursos adicionais, pode utilizar o módulo do AzureRM.CloudCapabilities PowerShell para criar um ficheiro JSON, incluindo os serviços de novo.  

1.  Certifique-se de que tem ligação à pilha do Azure.  Estes passos podem ser executados a partir do anfitrião de kit de desenvolvimento de pilha do Azure ou pode utilizar [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para ligar a partir da sua estação de trabalho. 
2.  Importe o módulo PowerShell do AzureRM.CloudCapabilities:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Utilize o cmdlet Get-CloudCapabilities para obter versões de service e crie um ficheiro JSON de capacidades de nuvem:

    ```PowerShell
    Get-AzureRMCloudCapabilities -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>Passos seguintes
 - [Implementar modelos pilha do Azure](azure-stack-arm-templates.md)
 - [Desenvolver modelos para a pilha do Azure](azure-stack-develop-templates.md)

