---
title: "Efetuar operações de API de tabela de base de dados do Azure Cosmos com o PowerShell | Microsoft Docs"
description: "Como realizar operações de API de tabela de base de dados do Azure Cosmos com o PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d0f835db8a9fbe3833a9c7931ad1d8b4a778f016
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Efetuar operações de API de tabela de base de dados do Azure Cosmos com o Azure PowerShell 

>[!NOTE]
>API de tabela de base de dados do Azure Cosmos fornece as funcionalidades premium para armazenamento de tabelas como chave na mão distribuição global, leituras de latência baixa e escritas, indexação secundário automática e débito dedicado. Na maioria dos casos, os comandos do PowerShell no trabalho artigo para armazenamento de API de tabela de base de dados do Azure Cosmos e tabelas do Azure, mas este artigo é específica para API de tabela de base de dados do Azure Cosmos. Se estiver a utilizar o Table storage do Azure, consulte o artigo [operações de armazenamento de Azure Table executar com o Azure PowerShell](table-storage-how-to-use-powershell.md).
>

API de tabela de base de dados do Azure Cosmos permite-lhe armazenar e consultar conjuntos enormes de dados estruturados não relacionais. Componentes principais do serviço são propriedades, tabelas e entidades. Uma tabela é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades que são todos os pares nome-valor. Este artigo pressupõe que já estiver familiarizado com os conceitos de API de tabela de base de dados do Azure Cosmos. Para obter informações detalhadas, consulte [introdução à API de tabela de base de dados do Azure Cosmos](table-introduction.md) e [compilar uma aplicação .NET através da API de tabela](create-table-dotnet.md).

Este artigo procedimentos abrange operações de API de tabela comuns. Saiba como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Obter uma tabela
> * Adicione as entidades da tabela
> * Consulta uma tabela
> * Eliminar entidades da tabela

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos necessitam do Azure PowerShell versão do módulo 4.4.0 ou posterior. Na janela do PowerShell, execute `Get-Module -ListAvailable AzureRM` para localizar a versão. Se será apresentado nada ou terá de atualizar, consulte [módulo Azure PowerShell instalar](/powershell/azure/install-azurerm-ps). 

Depois do Azure PowerShell está instalado ou atualizado, tem de instalar o módulo **AzureRmStorageTable**, que tem os comandos para gerir as entidades. Para instalar este módulo, executar o PowerShell como administrador e utilize o **Install-Module** comando.

```powershell
Install-Module AzureRmStorageTable
```

Em seguida, instale as assemblagens de base de dados do Azure Cosmos localmente para utilizar estes cmdlets do PowerShell. Para obter instruções sobre como fazê-lo, consulte [módulo do PowerShell de tabelas de armazenamento do Azure RM para tabelas de base de dados do Cosmos](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/).

Para experimentar os exercícios do seguintes, precisa de uma conta de base de dados de base de dados do Azure Cosmos. Se ainda não tiver um, crie uma nova base de dados de Cosmos Azure conta através do [portal do Azure](https://portal.azure.com). Para ajudar a criar uma nova conta de base de dados, consulte [BD do Azure Cosmos: criar uma conta de base de dados](create-table-dotnet.md#create-a-database-account).

Obter o grupo de recursos e nome da conta de base de dados a partir do portal do; é necessário estes valores a colocar no script para aceder as tabelas. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Criar uma tabela ou referenciar uma tabela

Para criar uma tabela ou para obter uma referência a uma tabela, utilize **Get-AzureStorageTableTable**. Se chamar este cmdlet com o nome de uma tabela que não existe, cria uma nova tabela com esse nome e devolve uma referência para a tabela de novo. Se a tabela existe devolve uma referência à tabela existente.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Não é possível listar as tabelas na conta de base de dados do Azure Cosmos através do PowerShell, mas pode iniciar sessão no portal e ver a tabela. Agora vamos ver como gerir as entidades na tabela.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Eliminar uma tabela 

PowerShell não suporta a eliminação de tabelas da base de dados do Azure Cosmos. Para eliminar uma tabela, vá para o [portal do Azure](https://azure.portal.com), localize a conta de base de dados do Azure Cosmos estiver a utilizar, em seguida, localizar e eliminar a tabela. 

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um novo grupo de recursos e criar uma nova conta de base de dados do Azure Cosmos desse grupo, pode remover todos os recursos que criou neste exercício ao remover o grupo de recursos. Este comando elimina todos os recursos contidos no grupo, bem como o grupo de recursos em si.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este artigo procedimentos, aprendeu sobre operações de API de tabela comuns com o PowerShell, incluindo como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Obter uma tabela
> * Adicione as entidades da tabela
> * Consulta uma tabela
> * Eliminar entidades da tabela

Para obter mais informações, veja os artigos seguintes:

* [Trabalhar com tabelas de armazenamento do Azure a partir do PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.