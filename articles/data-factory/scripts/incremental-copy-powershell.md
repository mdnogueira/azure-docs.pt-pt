---
title: 'Script do PowerShell: incrementalmente carregar dados, utilizando o Azure Data Factory | Microsoft Docs'
description: Este script do PowerShell mostra como utilizar o Azure Data Factory para copiar dados de forma incremental de uma base de dados do SQL do Azure para um armazenamento de Blobs do Azure...
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: spelluru
ms.openlocfilehash: 36a8c8c4ed83a56dfd0f487ec4bcf030e3890ef6
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Script do PowerShell - incrementalmente carga dados através da utilização do Azure Data Factory
Este script do PowerShell de exemplo carrega registos apenas novos ou atualizados a partir de um arquivo de dados de origem para um arquivo de dados dependente após a cópia inicial completa dos dados de origem para o sink.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Consulte [tutorial: cópia incremental](../tutorial-incremental-copy-powershell.md#prerequisites) para as pré-requisitos para executar este exemplo. 

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Este script cria ficheiros JSON que definem as entidades do Data Factory (serviço ligado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

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
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Conjunto AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Criar uma fábrica de dados. |
| [Conjunto AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Cria um serviço ligado no factory de dados. Um serviço ligado liga um arquivo de dados ou a computação uma fábrica de dados. |
| [Conjunto AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Cria um conjunto de dados na fábrica de dados. Um conjunto de dados representa a entrada/saída para uma atividade num pipeline. | 
| [Conjunto AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Cria um pipeline na fábrica de dados. Um pipeline contém uma ou mais atividades que executa uma operação de determinadas. Este pipeline, uma atividade de cópia copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. |
| [AzureRmDataFactoryV2Pipeline invocar](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Cria uma execução para o pipeline. Por outras palavras, é executado o pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (de execução da atividade) no pipeline. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos de script do PowerShell do Azure Data Factory adicionais podem ser encontrados no [scripts do PowerShell do Azure Data Factory](../samples-powershell.md).