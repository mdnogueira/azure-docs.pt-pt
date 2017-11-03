---
title: "Gerir soluções do Azure com o PowerShell | Microsoft Docs"
description: Utilize o Azure PowerShell e do Resource Manager para gerir os recursos.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: ae5ccb83a0088cb7c9668f18620b74f9f3f1e9b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Gerir os recursos com o Azure PowerShell e do Resource Manager

Neste artigo, irá aprender a gerir as suas soluções com o Azure PowerShell e o Azure Resource Manager. Se não estiver familiarizado com o Resource Manager, consulte [descrição geral do Gestor de recursos](resource-group-overview.md). Este artigo incida no tarefas de gestão. Irá:

1. Criar um grupo de recursos
2. Adicione um recurso para o grupo de recursos
3. Adicionar uma etiqueta para o recurso
4. Recursos com base nos valores de etiqueta ou nomes de consulta
5. Aplicar e remover um bloqueio num recurso
6. Eliminar um grupo de recursos

Este artigo mostra como implementar um modelo do Resource Manager para a sua subscrição. Para essas informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md).

## <a name="get-started-with-azure-powershell"></a>Introdução ao Azure PowerShell

Se não tiver instalado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Se tiver instalado o Azure PowerShell no passado, mas não tiver atualizado-recentemente, considere instalar a versão mais recente. Pode atualizar a versão através do mesmo método que utilizou para instalá-lo. Por exemplo, se tiver utilizado o Web Platform Installer, inicie-o novamente e procurar por uma atualização.

Para verificar a sua versão do módulo de recursos do Azure, utilize o seguinte cmdlet:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Este artigo foi atualizado para a versão 3.3.0. Se tiver uma versão anterior, sua experiência pode não corresponder aos passos apresentados neste artigo. Para obter documentação sobre os cmdlets nesta versão, consulte [AzureRM.Resources módulo](/powershell/module/azurerm.resources).

## <a name="log-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure
Antes de trabalhar na sua solução, tem de iniciar sessão sua conta.

Para iniciar sessão sua conta do Azure, utilize o **Login-AzureRmAccount** cmdlet.

```powershell
Login-AzureRmAccount
```

O cmdlet pede-lhe as credenciais de início de sessão da conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell.

O cmdlet devolve informações sobre a sua conta e a subscrição a utilizar para as tarefas.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

Se tiver mais do que uma subscrição, pode mudar para uma subscrição diferente. Em primeiro lugar, vamos ver todas as subscrições para a sua conta.

```powershell
Get-AzureRmSubscription
```

Devolve ativados e desativados subscrições.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Para mudar para uma subscrição diferente, forneça o nome de subscrição com o **Set-AzureRmContext** cmdlet.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de implementar quaisquer recursos na sua subscrição, tem de criar um grupo de recursos que irá conter os recursos.

Para criar um grupo de recursos, utilize o **New-AzureRmResourceGroup** cmdlet. O comando utiliza o **nome** parâmetro para especificar um nome para o grupo de recursos e o **localização** parâmetro para especificar a localização.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

O resultado é o seguinte formato:

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Se precisar de obter o grupo de recursos mais tarde, utilize o seguinte cmdlet:

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

Para obter todos os grupos de recursos na sua subscrição, não especifique um nome de:

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Adicionar recursos a um grupo de recursos

Para adicionar um recurso para o grupo de recursos, pode utilizar o **New-AzureRmResource** cmdlet ou um cmdlet que é específico para o tipo de recurso que está a criar (como **New-AzureRmStorageAccount**). Pode encontrar mais fáceis de utilizar um cmdlet que é específico para um tipo de recurso, pois inclui os parâmetros para as propriedades que são necessários para o novo recurso. Para utilizar **New-AzureRmResource**, tem de saber todas as propriedades para definir sem pedidos para os mesmos.

No entanto, a adição de um recurso através de cmdlets poderá causar confusão futura porque o novo recurso não existe um modelo do Resource Manager. A Microsoft recomenda que define a infraestrutura para a sua solução do Azure num modelo do Resource Manager. Modelos permitem-lhe implementar repetidamente e fiável a sua solução. Para este artigo, crie uma conta de armazenamento com um cmdlet do PowerShell, mas mais tarde gerar um modelo do seu grupo de recursos.

O cmdlet seguinte cria uma conta de armazenamento. Em vez de utilizar o nome apresentado no exemplo, forneça um nome exclusivo para a conta de armazenamento. O nome tem de ter entre 3 e 24 carateres de comprimento e utilizar apenas números e letras minúsculas. Se utilizar o nome apresentado no exemplo, receberá um erro porque este nome já se encontra em utilização.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Se precisar de obter este recurso mais tarde, utilize o seguinte cmdlet:

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Adicionar uma etiqueta

As etiquetas permitem-lhe organizar os recursos, de acordo com as propriedades diferentes. Por exemplo, pode ter vários recursos em grupos de recursos diferente que pertencem ao mesmo departamento. Pode aplicar uma etiqueta de departamento e o valor a esses recursos marcá-los como pertencentes à mesma categoria. Em alternativa, pode marcá-se um recurso é utilizado num ambiente de teste ou de produção. Neste artigo, aplicar etiquetas a apenas um recurso, mas no seu ambiente provavelmente faz sentido aplicar etiquetas a todos os seus recursos.

O cmdlet seguinte aplica-se duas etiquetas à sua conta de armazenamento:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

As etiquetas são atualizadas como um único objeto. Para adicionar uma etiqueta a um recurso que já inclua etiquetas, obtenha primeiro as etiquetas existentes. Adicionar a nova tag de objeto que contém as etiquetas existentes e volte a aplicar todas as etiquetas ao recurso.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Procurar recursos

Utilize o **localizar AzureRmResource** cmdlet para obter recursos para condições de pesquisa diferentes.

* Para obter um recurso de por nome, forneça o **ResourceNameContains** parâmetro:

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Para obter todos os recursos num grupo de recursos, forneça o **ResourceGroupNameContains** parâmetro:

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Para obter todos os recursos com um nome de tag e um valor, forneça o **TagName** e **TagValue** parâmetros:

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Para todos os recursos com um tipo de recurso específico, forneça o **ResourceType** parâmetro:

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="get-resource-id"></a>Obter ID de recurso

Vários comandos demorar um ID de recurso como um parâmetro. Para obter o ID de para um recurso e arquivo numa variável, utilize:

```powershell
$webappID = (Get-AzureRmResource -ResourceGroupName exampleGroup -ResourceName exampleSite).ResourceId
```

## <a name="lock-a-resource"></a>Um recurso de bloqueio

Quando precisar de certificar-se de um recurso crítico não acidentalmente eliminado ou modificado, aplicar um bloqueio para o recurso. Pode especificar um um **CanNotDelete** ou **ReadOnly**.

Para criar ou eliminar as bloqueios de gestão, tem de ter acesso a `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações. Das funções incorporadas, apenas o proprietário e o administrador de acesso de utilizador são concedidas essas ações.

Para aplicar um bloqueio, utilize o seguinte cmdlet:

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Não é possível eliminar o recurso bloqueado no exemplo anterior até que o bloqueio é removido. Para remover um bloqueio, utilize:

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Para obter mais informações sobre as bloqueios de definição, consulte [bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Remover recursos ou um grupo de recursos
Pode remover um recurso ou grupo de recursos. Quando remove um grupo de recursos, tem também de remover todos os recursos nesse grupo de recursos.

* Para eliminar um recurso do grupo de recursos, utilize o **remover AzureRmResource** cmdlet. Este cmdlet elimina os recursos, mas não eliminar o grupo de recursos.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Para eliminar um grupo de recursos e todos os respetivos recursos, utilize o **Remove-AzureRmResourceGroup** cmdlet.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

Para ambos os cmdlets, é-lhe pedido para confirmar que pretende remover o recurso ou grupo de recursos. Se a operação elimina com êxito o recurso ou grupo de recursos, devolve **verdadeiro**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Executar scripts de Gestor de recursos com a automatização do Azure

Este artigo mostra como executar operações básicas nos seus recursos com o Azure PowerShell. Para cenários de gestão mais avançados, normalmente, pretende criar um script e reutilizar nesse script conforme necessário ou com base numa agenda. [A automatização do Azure](../automation/automation-intro.md) fornece uma forma de automatizar frequentemente utilizadas scripts que gerem as suas soluções do Azure.

Os tópicos seguintes mostram como utilizar a automatização do Azure, o Gestor de recursos e o PowerShell para eficazmente efetuar tarefas de gestão:

- Para obter informações sobre como criar um runbook, consulte [o meu primeiro runbook do PowerShell](../automation/automation-first-runbook-textual-powershell.md).
- Para obter informações sobre como trabalhar com galleries de scripts, consulte [galleries módulos e Runbooks de automatização do Azure](../automation/automation-runbook-gallery.md).
- Para os runbooks que iniciar e parar máquinas virtuais, consulte [cenário de automatização do Azure: formatada em JSON utilizando etiquetas para criar um agendamento para a VM do Azure de arranque e encerramento](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- Para os runbooks que iniciar e parar off-hours de máquinas virtuais, consulte [VMs de início/paragem durante a solução de off-hours na automatização](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como criar modelos do Resource Manager, consulte [criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre a implementação de modelos, consulte [implementar uma aplicação com o modelo do Azure Resource Manager](resource-group-template-deploy.md).
* Pode mover recursos existentes para um novo grupo de recursos. Para obter exemplos, consulte [mover recursos para o novo grupo de recursos ou subscrição](resource-group-move-resources.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

