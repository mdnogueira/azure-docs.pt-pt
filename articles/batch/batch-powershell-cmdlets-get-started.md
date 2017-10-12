---
title: "Introdução ao PowerShell para Azure Batch | Microsoft Docs"
description: "Introdução rápida aos cmdlets do Azure PowerShell que pode utilizar para gerir os recursos do Batch."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e33be6ed658e00250ea1e80cd7da4d348fb18296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Gerir os recursos do Batch com os cmdlets do PowerShell

Com os cmdlets do Azure Batch PowerShell, pode efetuar e encriptar muitas das mesmas tarefas que desempenha com as APIs do lote, o portal do Azure e a Interface de Linha de Comandos do Azure (CLI). Isto é uma introdução rápida aos cmdlets que pode utilizar para gerir as contas do Batch e trabalhar com os recursos do Batch, como conjuntos e tarefas.

Para obter uma lista completa de cmdlets do Batch e a sintaxe detalhada dos cmdlets, veja a [Referência de cmdlets do Azure Batch](/powershell/module/azurerm.batch/#batch).

Este artigo baseia-se nos cmdlets do Azure PowerShell versão 3.0.0. Recomendamos que atualize frequentemente o Azure PowerShell para tirar partido das atualizações e melhoramentos do serviço.

## <a name="prerequisites"></a>Pré-requisitos
Efetue as seguintes operações para utilizar o Azure PowerShell para gerir os recursos do Batch.

* [Instale e configure o Azure PowerShell](/powershell/azure/overview).
* Execute o cmdlet **Login-AzureRmAccount** para ligar à sua subscrição (os cmdlets do Azure Batch enviados no módulo do Azure Resource Manager):
  
    `Login-AzureRmAccount`
* **Registe no espaço de nomes do fornecedor do Batch**. Esta operação só tem de ser efetuada **uma vez por subscrição**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gerir contas e chaves do Batch
### <a name="create-a-batch-account"></a>Criar uma conta do Batch
**New-AzureRmBatchAccount** cria uma conta do Batch num grupo de recursos especificado. Caso ainda não tenha um grupo de recursos, crie um ao executar o cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Especifique uma das regiões do Azure no parâmetro **Localização**, por exemplo, “EUA Central”. Por exemplo:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Em seguida, crie uma conta do Batch no grupo de recursos ao especificar um nome de conta em <*nome_de_conta*> e a localização e o nome do grupo de recursos. Criar a conta do Batch pode demorar algum tempo a concluir. Por exemplo:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> O nome da conta do Batch tem de ser exclusivo para a região do Azure do grupo de recursos, conter entre 3 e 24 carateres e utilizar apenas letras minúsculas e números.
> 
> 

### <a name="get-account-access-keys"></a>Obter chaves de acesso à conta
**Get-AzureRmBatchAccountKeys** mostra as chaves de acesso associadas a uma conta do Azure Batch. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta que criou.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso
**New-AzureRmBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta do Azure Batch. Por exemplo, para gerar uma nova chave primária para a conta do Batch, escreva:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType**. Tem de regenerar as chaves primária e secundária em separado.
> 
> 

### <a name="delete-a-batch-account"></a>Eliminar uma conta do Batch
**Remove-AzureRmBatchAccount** elimina uma conta do Batch. Por exemplo:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando lhe for perguntado, confirme que pretende remover a conta. A remoção da conta pode demorar algum tempo a concluir.

## <a name="create-a-batchaccountcontext-object"></a>Criar um objeto BatchAccountContext
Para efetuar a autenticação utilizando os cmdlets do Batch PowerShell quando criar e gerir agrupamentos, tarefas e outros recursos do Batch, primeiro crie um objeto BatchAccountContext para armazenar o nome e as chaves da conta:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

O objeto BatchAccountContext é transmitido aos cmdlets que utilizam o parâmetro **BatchContext**.

> [!NOTE]
> Por predefinição, a chave primária da conta é utilizada na autenticação, mas pode selecionar explicitamente a chave a utilizar ao alterar a propriedade **KeyInUse** do objeto BatchAccountContext: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos do Batch
Utilize cmdlets como **New-AzureBatchPool**, **New-AzureBatchJob** e **New-AzureBatchTask** para criar recursos numa conta do Batch. Existem cmdlets **Get-** e **Set-** correspondentes para atualizar as propriedades dos recursos existentes e cmdlets **Remove-** para remover recursos numa conta do Batch.

Quando utiliza muitos destes cmdlets, além de transmitir um objeto de BatchContext, terá de criar ou passar objetos que contêm definições detalhadas de recursos, conforme mostrado no exemplo seguinte. Consulte a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### <a name="create-a-batch-pool"></a>Criar um conjunto do Batch
Quando criar ou atualizar um conjunto do Batch, selecione a configuração de serviço cloud ou a configuração de máquina virtual para o sistema operativo nos nós de computação (veja [Descrição geral da funcionalidade do Batch](batch-api-basics.md#pool)). Se especificar a configuração de serviço cloud, a imagem dos nós de computação é feita com uma das [versões de SO Convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Se optar pela configuração de máquina virtual, pode especificar uma das imagens de VM do Linux ou Windows suportadas que estão disponíveis no [Markteplace das Máquinas Virtuais do Azure][vm_marketplace] ou fornecer uma imagem personalizada que tenha preparado.

Quando executa o **New-AzureBatchPool**, introduza as definições do sistema operativo num objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o seguinte cmdlet cria um novo conjunto do Batch com nós de computação pequenos na configuração de serviço na nuvem, instalados com a versão mais recente do sistema operativo da família 3 (Windows Server 2012). Neste caso, o parâmetro **CloudServiceConfiguration** especifica a variável *$configuration* como o objeto PSCloudServiceConfiguration. O parâmetro **BatchContext** especifica uma variável *$context* definida anteriormente como objeto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

O número de destino dos nós de computação do novo conjunto é determinado por uma fórmula de dimensionamento automático. Neste caso, a fórmula é simplesmente **$TargetDedicated=4**, que indica que o número de nós de computação no conjunto é, no máximo, 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consulta para conjuntos, tarefas e outros detalhes
Utilize cmdlets, como **Get-AzureBatchPool**, **Get-AzureBatchJob** e **Get-AzureBatchTask** para consultar entidades criadas numa conta do Batch.

### <a name="query-for-data"></a>Consulta de dados
Por exemplo, utilize **Get-AzureBatchPools** para localizar os seus agrupamentos. Por predefinição, isto consulta todos os agrupamentos na sua conta, assumindo que já armazenou o objeto BatchAccountContext em *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Utilizar um filtro OData
Pode fornecer um filtro OData utilizando o parâmetro **Filter** para localizar apenas os objetos que lhe interessam. Por exemplo, pode localizar todos os agrupamentos com ids começados por "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Este método não é tão flexível como se utilizasse "Where-Object" num pipeline local. No entanto, a consulta é enviada diretamente para o serviço Batch para que todas as filtragens ocorram no lado do servidor, poupando a largura de banda da Internet.

### <a name="use-the-id-parameter"></a>Utilizar o parâmetro Id
Uma alternativa a um filtro OData é a utilização do parâmetro **Id**. Para consultar um agrupamento específico com o id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

O parâmetro **Id** suporta apenas a pesquisa de ids completos e não carateres universais ou filtros de estilo OData.

### <a name="use-the-maxcount-parameter"></a>Utilizar o parâmetro MaxCount
Por predefinição, cada cmdlet devolve um máximo de 1000 objetos. Se atingir este limite, refine o filtro para obter menos objetos ou defina explicitamente um limite máximo utilizando o parâmetro **MaxCount**. Por exemplo:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### <a name="use-the-powershell-pipeline"></a>Utilizar o pipeline do PowerShell
Os cmdlets do Batch podem tirar partido do pipeline do PowerShell para enviar dados entre cmdlets. Este procedimento tem o mesmo efeito que especificar um parâmetro, mas facilita o trabalho com várias entidades.

Por exemplo, localizar e apresentar todas as tarefas na sua conta:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Reinicie todos os nós de computação num conjunto:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gestão de pacotes de aplicações
Os pacotes de aplicações proporcionam uma forma simplificada para implementar aplicações nos nós de computação nos seus conjuntos. Com os cmdlets do PowerShell do Batch, pode carregar e gerir pacotes de aplicações na sua conta do Batch e implementar versões de pacotes em nós de computação.

**Criar** uma aplicação:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Adicionar** um pacote de aplicações:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Definir a **versão predefinida** da aplicação:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Listar** os pacotes de uma aplicação

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Eliminar** um pacote de aplicação

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Eliminar** uma aplicação

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Tem de eliminar todas as versões dos pacotes de aplicações de uma aplicação antes de eliminá-la. Se tentar eliminar uma aplicação que possua atualmente pacotes de aplicações, receberá um erro de “Conflito”.
> 
> 

### <a name="deploy-an-application-package"></a>Implementar um pacote de aplicações
Quando cria um conjunto, pode especificar um ou mais pacotes de aplicações para implementação. Quando especifica um pacote durante a criação de conjuntos, é implementado em cada nó no momento em que o nó é associado ao conjunto. Os pacotes também são implementados quando um nó é reiniciado ou recriado.

Especifique a opção `-ApplicationPackageReference` quando criar um conjunto para implementar pacotes de aplicações nos nós do conjunto à medida que são adicionados ao mesmo. Primeiro, crie um objeto **PSApplicationPackageReference** e configure-o com o ID da aplicação e a versão do pacote que pretende implementar nos nós de computação do conjunto:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Em seguida, crie o conjunto e especifique o objeto de referência do pacote como argumento para a opção `ApplicationPackageReferences`:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Pode encontrar mais informações sobre pacotes de aplicações em [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).

> [!IMPORTANT]
> Tem de [associar uma conta de armazenamento do Azure](#linked-storage-account-autostorage) à sua conta do Batch para utilizar pacotes de aplicações.
> 
> 

### <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto
Para atualizar as aplicações atribuídas a um conjunto existente, primeiro, crie um objeto PSApplicationPackageReference com as propriedades pretendidas (ID da aplicação e versão do pacote):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Em seguida, obtenha o conjunto do Batch, retire quaisquer pacotes existentes, adicione a nossa nova referência do pacote e atualize o serviço Batch com as novas definições do conjunto:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Atualizou as propriedades do conjunto no serviço Batch. Contudo, para implementar realmente o pacote de aplicação novo nos nós de computação no conjunto, tem de reiniciar ou recriar as imagens destes nós. Pode reiniciar todos os nós num conjunto com este comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> Pode implementar vários pacotes de aplicações nos nós de computação num conjunto. Se pretender *adicionar* um pacote de aplicação ao invés de substituir os pacotes implementados atualmente, omita a linha `$pool.ApplicationPackageReferences.Clear()` acima.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Para obter exemplos e a sintaxe detalhada dos cmdlets, veja a [Referência de cmdlets do Azure Batch](/powershell/module/azurerm.batch/#batch).
* Para obter mais informações sobre as aplicações e os pacotes de aplicações no Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/