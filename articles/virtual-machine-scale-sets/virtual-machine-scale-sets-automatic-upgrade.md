---
title: "Conjuntos de dimensionamento de atualizações automáticas de SO com a máquina virtual do Azure | Microsoft Docs"
description: "Saiba como atualizar automaticamente o SO em instâncias VM um conjunto de dimensionamento"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: guybo
ms.openlocfilehash: 32358b23bb0a0a878e986150dd992513579d61c4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>As atualizações automáticas de SO de conjunto de dimensionamento de máquina virtual do Azure

Atualização automática de imagem de SO é uma funcionalidade de pré-visualização para conjuntos de dimensionamento de máquina virtual do Azure que atualiza automaticamente todas as VMs para a imagem de SO mais recente.

A atualização automática do SO tem as seguintes características:

- Depois de configurar, a imagem de SO mais recente publicada por editores de imagem é aplicada automaticamente a escala definir sem intervenção do utilizador.
- Atualiza lotes de instâncias de uma forma graduais sempre que for publicada uma nova imagem de plataforma pelo fabricante.
- Integra a sonda de estado de funcionamento da aplicação (opcional, mas vivamente recomendado de segurança).
- Funciona para todos os tamanhos VM.
- Imagens de plataforma funciona para Windows e Linux.
- Pode ativamente as atualizações automáticas em qualquer altura (atualizações do SO pode ser iniciadas manualmente, bem como).
- O disco de SO de uma VM é substituído com o novo disco de SO criado com a versão mais recente de imagem. Extensões configuradas e scripts de dados personalizados são executados ao dados persistente discos são retidos.


## <a name="preview-notes"></a>Notas de pré-visualização 
Enquanto na pré-visualização, as seguintes limitações e restrições aplicam-se:

- SO automático atualiza suporte apenas [três SKUs do SO](#supported-os-images). Não há nenhum SLA ou garantias. Recomendamos que utilize as atualizações automáticas em cargas de trabalho críticas produção durante a pré-visualização.
- Suporte para conjuntos de dimensionamento de clusters de Service Fabric está disponível em breve.
- A encriptação de disco do Azure (atualmente em pré-visualização) é **não** atualmente suportado com a máquina virtual escala conjunto SO a atualização automática.
- Experiência do portal estará disponível em breve.


## <a name="register-to-use-automatic-os-upgrade"></a>Registar para utilizar a atualização automática de SO
Para utilizar a funcionalidade de atualização automática do SO, registe o fornecedor de pré-visualização com [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) da seguinte forma:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Demora cerca de 10 minutos para que o estado de registo para o relatório como *registada*. Pode verificar o estado do registo atual com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Depois de registado, certifique-se de que o *Microsoft. Compute* fornecedor está registado com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) da seguinte forma:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

Recomendamos que as aplicações utilizem sondas de estado de funcionamento. Para registar a funcionalidade de fornecedor de sondas de estado de funcionamento, utilize [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) da seguinte forma:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
```

Novamente, demora cerca de 10 minutos para que o estado de registo para o relatório como *registada*. Pode verificar o estado do registo atual com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Uma vez registado Certifique-se de que o *Network* fornecedor está registado com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) da seguinte forma:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```


## <a name="supported-os-images"></a>Imagens de SO suportadas
Apenas determinadas imagens da plataforma de SO são atualmente suportadas. Atualmente não é possível utilizar imagens personalizadas que tiver criou si próprio. O *versão* propriedade da imagem de plataforma tem de ser definida *mais recente*.

Os SKUs seguintes são atualmente suportados (mais serão adicionado):
    
| Publicador               | Oferta         |  Sku               | Versão  |
|-------------------------|---------------|--------------------|----------|
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | mais recente   |
| MicrosoftWindowsServer  | WindowsServer | Centro de dados de 2016    | mais recente   |
| Canónico               | UbuntuServer  | 16.04 LTS          | mais recente   |


## <a name="application-health"></a>Estado de funcionamento da aplicação
Durante uma atualização do SO, instâncias de VM num conjunto de dimensionamento sejam atualizadas um batch de cada vez. A atualização deve continuar apenas se a aplicação de cliente está em bom estada de instâncias de VM atualizado. Recomendamos que a aplicação fornece sinais de estado de funcionamento para o motor de atualização de SO de conjunto de dimensionamento. Por predefinição, durante as atualizações do SO a plataforma considera o estado de energia VM e a extensão de estado para determinar se uma instância VM está em bom estada após uma atualização de aprovisionamento. Durante a atualização de SO de uma instância VM, o disco de SO numa instância de VM é substituído por um novo disco com base na versão mais recente de imagem. Uma vez concluída a atualização do SO, as extensões configuradas são executadas nestas VMS. Apenas quando todas as extensões numa VM são aprovisionadas com êxito, é a aplicação considerada em bom estado. 

Opcionalmente, pode ser configurado um conjunto de dimensionamento com sondas de estado de funcionamento da aplicação para fornecer a plataforma de informações exatas relacionadas no estado da aplicação. Sondas de estado de funcionamento de aplicação são personalizada carga Balanceador de sondas que são utilizadas como um sinal de estado de funcionamento. A aplicação em execução numa instância VM de conjunto de dimensionamento pode responder a pedidos HTTP ou TCP externos que indica se é bom estado de funcionamento. Para obter mais informações sobre como funcionam os pesquisas do Balanceador de carga personalizada, consulte o artigo [sondas de Balanceador de carga de compreender](../load-balancer/load-balancer-custom-probe-overview.md). Uma pesquisa de estado de funcionamento de aplicação não é necessária para as atualizações automáticas do SO, mas é vivamente recomendável.

Se o conjunto de dimensionamento é configurado para utilizar vários grupos de colocação, as sondas utilizando um [padrão Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) têm de ser utilizados.


### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>A configurar uma sonda de Balanceador de carga do personalizada como a pesquisa de estado de funcionamento da aplicação numa escala definido
Como melhor prática, crie uma sonda do Balanceador de carga explicitamente para o estado de funcionamento do conjunto de dimensionamento. Pode ser utilizado o mesmo ponto final de uma pesquisa HTTP existente ou uma sonda TCP, mas uma sonda do Estado de funcionamento pode necessitar de um comportamento diferente de uma sonda do Balanceador de carga tradicional. Por exemplo, uma sonda do Balanceador de carga tradicional pode devolver danificada se a carga sobre a instância for demasiado elevada, enquanto que não pode ser apropriada para determinar o estado de funcionamento de instância durante uma atualização automática do SO. Configure a sonda para ter uma elevada taxa de pesquisa de menos de dois minutos.

A sonda do Balanceador de carga é possível referenciar o *networkProfile* da escala definida e pode ser associado a um interno ou público destinado ao balanceador de carga da seguinte forma:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Impor uma política de atualização de imagem de SO em toda a sua subscrição
Para atualizações de segurança, recomenda vivamente para impor uma política de atualização. Esta política pode necessitar de sondas de estado de funcionamento da aplicação na sua subscrição. A seguinte política do Azure Resource Manager rejeita implementações que não têm a imagem do SO automatizada atualizar as definições configuradas:

1. Obter a definição de política do Azure Resource Manager incorporada com [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) da seguinte forma:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Atribuir a política para uma subscrição com [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) da seguinte forma:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Configurar automaticamente atualizações
Para configurar atualizações automáticas, certifique-se de que o *automaticOSUpgrade* propriedade está definida como *verdadeiro* no conjunto de dimensionamento definição de modelo. Pode configurar esta propriedade com o Azure PowerShell ou o 2.0 CLI do Azure.

O exemplo seguinte utiliza o Azure PowerShell (4.4.1 ou posterior) para configurar atualizações automáticas para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


O exemplo seguinte utiliza a CLI do Azure (2.0.20 ou posterior) para configurar atualizações automáticas para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azure-cli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Verificar o estado de uma atualização automática do SO
Pode verificar o estado da atualização do SO mais recente efetuada no seu conjunto com o Azure PowerShell, 2.0 do Azure CLI ou REST APIs de dimensionamento.

### <a name="azure-powershell"></a>Azure PowerShell
Para o exemplo seguinte utiliza o Azure PowerShell (4.4.1 ou posterior) para verificar o estado para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
O exemplo seguinte utiliza a CLI do Azure (2.0.20 ou posterior) para verificar o estado para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azure-cli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>API REST
O exemplo seguinte utiliza a API REST para verificar o estado para o conjunto nomeado de dimensionamento *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

A chamada GET devolve propriedades semelhante ao seguinte exemplo de saída:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Execução de atualização automática do SO
Para expandir-se na utilização de sondas de estado de funcionamento de aplicações, atualizações de SO do conjunto de dimensionamento executar os seguintes passos:

1. Se mais de 20% de instâncias de mau estado de funcionamento, parar a atualização; caso contrário, a prosseguir.
2. Identifique o lote seguinte de instâncias de VM para atualizar, com um ter máximo 20% do total de instâncias de batch.
3. Atualize o sistema operativo do lote seguinte de instâncias VM.
4. Se mais de 20% de instâncias atualizadas mau estado de funcionamento, parar a atualização; caso contrário, a prosseguir.
5. Se o cliente tiver configurado sondas de estado de funcionamento de aplicações, a atualização deve aguardar até 5 minutos para as pesquisas para se tornarem bom estado de funcionamento, em seguida, continua imediatamente no lote seguinte; caso contrário,-aguarda 30 minutos antes de passar para o lote seguinte.
6. Se é restantes que são instâncias para atualizar, goto passo 1) do lote seguinte; caso contrário, a atualização estiver concluída.

A escala definir verificações de motor de atualização de SO para o estado de funcionamento de instância VM geral antes de atualizar todos os lotes. Durante a atualização de um lote, poderão existir outros planeada em simultâneo ou a manutenção não planeada acontecer nos centros de dados do Azure que possam afetar a disponibilidade das suas VMs. Por conseguinte, é possível que temporariamente mais de 20% instâncias podem estar inativo. Nestes casos, no fim do lote atual, o conjunto de dimensionamento deixa de atualização.


## <a name="deploy-with-a-template"></a>Implementar com um modelo

Pode utilizar o modelo seguinte para implementar um conjunto de dimensionamento que utiliza as atualizações automáticas <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>sucessiva automática atualiza - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Passos seguintes
Para obter mais exemplos sobre como utilizar as atualizações automáticas de SO com conjuntos de dimensionamento, consulte o [repositório do GitHub para funcionalidades de pré-visualização](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
