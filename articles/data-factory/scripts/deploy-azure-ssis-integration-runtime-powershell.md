---
title: "PowerShell script - implementar o tempo de execução do Azure-SSIS integração | Microsoft Docs"
description: "Este script do PowerShell cria um tempo de execução de integração do Azure-SSIS que pode ser executados pacotes SSIS na nuvem."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.openlocfilehash: 94839decb082121e3e11d7c6041422ca1bea8861
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell script - implementar runtime de integração do Azure-SSIS

Este script do PowerShell de exemplo cria um tempo de execução de integração de SSIS do Azure que pode executar os pacotes SSIS no Azure.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode utilizar o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover a fábrica de dados do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [Novo-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Conjunto AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Criar uma fábrica de dados. |
| [Conjunto AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime) | Cria um tempo de execução de integração do Azure-SSIS que pode ser executados pacotes SSIS na nuvem |
| [Início AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime) | Inicia o tempo de execução de integração de SSIS do Azure. |
| [Get-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntime) | Obtém informações sobre o tempo de execução de integração de SSIS do Azure. |
| [Get-AzureRmDataFactoryV2IntegrationRuntimeStatus](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntimestatus) | Obtém informações sobre o estado do tempo de execução de integração SSIS do Azure. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos de script do PowerShell do Azure Data Factory adicionais podem ser encontrados no [exemplos do PowerShell do Azure Data Factory](../samples-powershell.md).