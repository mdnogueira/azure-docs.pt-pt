---
title: 'Script do PowerShell: copiar dados no local para o Azure utilizando o Data Factory | Microsoft Docs'
description: Este script do PowerShell copia dados a partir de uma base de dados do SQL Server no local para outro um armazenamento de Blobs do Azure.
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
ms.openlocfilehash: 7f062a58482ad72e3dd3844431205502b4c44786
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Utilizar o PowerShell para criar um pipeline de fábrica de dados para copiar os dados no local para Azure

Este script do PowerShell de exemplo cria um pipeline no Azure Data Factory que copia dados a partir de uma base de dados do SQL Server no local para um armazenamento de Blobs do Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Pré-requisitos

- **SQL Server**. Utilizar uma base de dados do SQL Server no local como uma **origem** do arquivo de dados neste exemplo.
- **Conta de Armazenamento do Azure**. Utilize o armazenamento de Blobs do Azure como um **destino/sink** do arquivo de dados neste exemplo. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter os passos para criar uma.
- **Autoalojado integração runtime**. Transferir o ficheiro MSI do [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=39717) e executá-la para instalar um tempo de execução de integração personalizada alojada no seu computador.  

### <a name="create-sample-database-in-sql-server"></a>Criar base de dados de exemplo no SQL Server
1. Base de dados do SQL Server no local, criar uma tabela com o nome **emp** utilizando o seguinte script SQL: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Inserir alguns dados de exemplo na tabela:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Este script cria ficheiros JSON que definem as entidades do Data Factory (serviço ligado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


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
| [Novo AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | Encripta credenciais de um serviço ligado e gera uma nova definição de serviço ligado com a credencial encriptada. 
| [Conjunto AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Cria um serviço ligado no factory de dados. Um serviço ligado liga um arquivo de dados ou a computação uma fábrica de dados. |
| [Conjunto AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Cria um conjunto de dados na fábrica de dados. Um conjunto de dados representa a entrada/saída para uma atividade num pipeline. | 
| [Conjunto AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Cria um pipeline na fábrica de dados. Um pipeline contém uma ou mais atividades que executa uma operação de determinadas. Este pipeline, uma atividade de cópia copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. |
| [AzureRmDataFactoryV2Pipeline invocar](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Cria uma execução para o pipeline. Por outras palavras, é executado o pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (de execução da atividade) no pipeline. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos de script do PowerShell do Azure Data Factory adicionais podem ser encontrados no [exemplos do PowerShell do Azure Data Factory](../samples-powershell.md).