---
title: Migrar para o Gestor de recursos com o PowerShell | Microsoft Docs
description: "Este artigo explica a migração de plataforma suportada dos recursos IaaS como máquinas virtuais (VMs), redes virtuais (VNETs) e contas de armazenamento do clássico para o Azure Resource Manager (ARM) utilizando comandos do PowerShell do Azure"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 01bccb2f8d103faf77b39825a1f9ff663329ed7a
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar os recursos de IaaS do clássico para o Azure Resource Manager, utilizando o Azure PowerShell
Estes passos mostram como utilizar comandos do PowerShell do Azure para migrar a infraestrutura como um recursos de serviço (IaaS) do modelo de implementação clássica para o modelo de implementação Azure Resource Manager.

Se quiser, também pode migrar recursos utilizando o [Interface de linha de comandos do Azure (CLI do Azure)](../linux/migration-classic-resource-manager-cli.md).

* Para em segundo plano em cenários de migração suportados, consulte [plataforma suportada a migração de recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Para obter instruções de migração e orientações detalhadas, consulte [técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)

<br>
Eis uma fluxograma para identificar a ordem na qual passos têm de ser executada durante um processo de migração

![Captura de ecrã que mostra os passos da migração](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Passo 1: Plano de migração
Seguem-se algumas melhores práticas que recomendamos como avaliar migrar recursos IaaS do clássico para o Gestor de recursos:

* Leia o [suportados e não suportados funcionalidades e configurações](migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizam configurações não suportadas ou funcionalidades, recomendamos que aguarde para o suporte de configuração/funcionalidade para ser anunciada. Em alternativa, se se adapta às suas necessidades, remova essa funcionalidade ou mover-se fora do que a configuração para permitir a migração.
* Se tiver automatizada scripts que implementar a sua infraestrutura e aplicações hoje em dia, tente criar uma configuração de teste semelhantes utilizando estes scripts para a migração. Em alternativa, pode configurar ambientes de exemplo através do portal do Azure.

> [!IMPORTANT]
> Gateways de aplicação não são atualmente suportados para migração do clássico para o Gestor de recursos. Para migrar uma rede virtual clássica com um gateway de aplicação, remova o gateway antes de executar uma operação de preparar para mover a rede. Depois de concluir a migração, restabeleça a ligação do gateway no Gestor de recursos do Azure.
>
>Ligar para circuitos do ExpressRoute na outra subscrição gateways do ExpressRoute não podem ser migrados automaticamente. Nestes casos, remova o gateway do ExpressRoute, migrar a rede virtual e recrie o gateway. Consulte [migrar ExpressRoute circuitos e associadas redes virtuais do clássico para o modelo de implementação Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Passo 2: Instalar a versão mais recente do Azure PowerShell
Existem duas opções principais para instalar o Azure PowerShell: [galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI recebe atualizações mensais. Galeria do PowerShell recebe atualizações numa base contínua. Este artigo baseia-se no Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Passo 3: Certifique-se de que um administrador da subscrição no portal do Azure
Para efetuar a migração, tem de ser adicionado como coadministrador da subscrição no [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **subscrição**. Se não o vir, selecione **mais serviços**.
3. Localizar a entrada de subscrição adequado, em seguida, observar o **função MY** campo. Para um coadministrador, o valor deve ser _administrador da conta_.

Se não é possível adicionar um coadministrador, contacte um administrador de serviço ou coadministrador da subscrição para si adicionado.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Passo 4: Definir a sua subscrição e inscrever-se para a migração
Em primeiro lugar, inicie uma linha de comandos do PowerShell. Para a migração, terá de configurar o ambiente para ambos os clássica e Resource Manager.

Inicie sessão sua conta para o modelo do Resource Manager.

```powershell
    Login-AzureRmAccount
```

Obtenha as subscrições disponíveis, utilizando o seguinte comando:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Defina a sua subscrição do Azure para a sessão atual. Neste exemplo define o nome da subscrição predefinida para **minha subscrição do Azure**. Substitua o nome de subscrição de exemplo com os seus próprios.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registo é um passo de uso individual, mas deve fazê-lo uma vez antes de tentar efetuar a migração. Sem registar, consulte a seguinte mensagem de erro:
>
> *BadRequest: Subscrição não está registada para migração.*
>
>

Registar o fornecedor de recursos de migração utilizando o seguinte comando:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para concluir o registo. Pode verificar o estado da aprovação utilizando o seguinte comando:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Certifique-se de que RegistrationState `Registered` antes de continuar.

Agora iniciar sessão sua conta para o modelo clássico.

```powershell
    Add-AzureAccount
```

Obtenha as subscrições disponíveis, utilizando o seguinte comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina a sua subscrição do Azure para a sessão atual. Neste exemplo define a subscrição predefinida para **minha subscrição do Azure**. Substitua o nome de subscrição de exemplo com os seus próprios.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 5: Certifique-se de que tem suficiente vCPUs de Máquina Virtual do Azure Resource Manager na região do Azure da sua implementação atual ou o VNET
Pode utilizar o seguinte comando do PowerShell para verificar o número atual de vCPUs que tiver no Gestor de recursos do Azure. Para saber mais sobre as quotas de vCPU, consulte o artigo [limites e o Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Neste exemplo verifica a disponibilidade **EUA oeste** região. Substitua o nome da região de exemplo com os seus próprios.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Passo 6: Executar comandos para migrar os recursos IaaS
> [!NOTE]
> Todas as operações descritas aqui são idempotent. Se tiver um problema que não seja uma funcionalidade não suportada ou um erro de configuração, recomendamos que repita preparar, abortar ou consolidar a operação. A plataforma tenta, em seguida, repita a ação.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passo 6.1: Opção 1 - migrar máquinas virtuais de um serviço em nuvem (não numa rede virtual)
Obter a lista de serviços em nuvem, utilizando o comando seguinte e, em seguida, escolha o serviço em nuvem que pretende migrar. Se as VMs no serviço em nuvem estão numa rede virtual, ou se têm funções web ou de trabalho, o comando devolve uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obter o nome da implementação para o serviço em nuvem. Neste exemplo, o nome do serviço é **meu serviço**. Substitua o nome do serviço de exemplo com o seu próprio nome de serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço em nuvem para a migração. Tem duas opções à sua escolha.

* **Opção 1. Migrar as máquinas virtuais a uma rede virtual criada de plataforma**

    Em primeiro lugar, confirme se é possível migrar o serviço em nuvem através dos seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando anterior mostra todos os avisos e erros que bloquear a migração. Se a validação for bem sucedida, então pode mover para o **preparar** passo:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2. Migrar para uma rede virtual existente no modelo de implementação Resource Manager**

    Neste exemplo define o nome do grupo de recursos para **myResourceGroup**, o nome de rede virtual para **myVirtualNetwork** e o nome de sub-rede para **mySubNet**. Substitua os nomes de exemplo com os nomes dos seus recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Em primeiro lugar, confirme se é possível migrar a rede virtual com o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando anterior mostra todos os avisos e erros que bloquear a migração. Se a validação for bem sucedida, em seguida, pode continuar com o passo de preparação seguinte:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Depois da operação de Prepare for bem sucedida com qualquer uma das opções anteriores, consulta o estado de migração das VMs. Certifique-se de que estão no `Prepared` estado.

Neste exemplo define o nome VM para **myVM**. Substitua o nome de exemplo com o seu próprio nome VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração para os recursos preparadas utilizando o PowerShell ou o portal do Azure. Se não estiver preparada para migração e pretender voltar para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada procura boa, pode avançar e consolidar os recursos utilizando o seguinte comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passo 6.1: Opção 2 - migrar máquinas virtuais numa rede virtual

Para migrar máquinas virtuais numa rede virtual, migre a rede virtual. Automaticamente migrar as máquinas virtuais com a rede virtual. Escolha a rede virtual que pretende migrar.
> [!NOTE]
> [Migrar a máquina virtual clássica](migrate-single-classic-to-resource-manager.md) criando uma nova máquina virtual de Resource Manager com discos geridos utilizando os ficheiros VHD (SO e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome de rede virtual pode ser diferente do que é apresentado no Portal de novo. O novo Portal do Azure apresenta o nome como `[vnet-name]` mas o nome de rede virtual real é do tipo `Group [resource-group-name] [vnet-name]`. Antes de migrar, pesquisa o nome de rede virtual real utilizando o comando `Get-AzureVnetSite | Select -Property Name` ou vê-la no Portal do Azure antigo. 

Neste exemplo define o nome de rede virtual para **myVnet**. Substitua o nome de rede virtual de exemplo com os seus próprios.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contém web ou funções de trabalho ou VMs com configurações não suportadas, receberá uma mensagem de erro de validação.
>
>

Em primeiro lugar, confirme se é possível migrar a rede virtual, utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando anterior mostra todos os avisos e erros que bloquear a migração. Se a validação for bem sucedida, em seguida, pode continuar com o passo de preparação seguinte:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas utilizando o Azure PowerShell ou o portal do Azure. Se não estiver preparada para migração e pretender voltar para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada procura boa, pode avançar e consolidar os recursos utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

## <a name="step-62-migrate-a-storage-account"></a>Passo 6.2 migrar uma conta de armazenamento
Depois de concluir a migrar as máquinas virtuais, recomendamos que migre as contas de armazenamento.

Antes de migrar a conta de armazenamento, execute precedente verificações de pré-requisitos:

* **Migrar as máquinas virtuais clássicas cujos discos estão armazenados na conta de armazenamento**

    Precedente comando devolve RoleName e DiskName propriedades de todos os discos VM clássicos na conta de armazenamento. RoleName é o nome da máquina virtual ao qual está ligado um disco. Se precedente comando devolve discos, em seguida, certifique-se de que as máquinas virtuais para que estes discos estão anexados são migradas antes de migrar a conta de armazenamento.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **Eliminar desanexados discos VM clássicos armazenados na conta de armazenamento**

    Localize desanexados discos VM Clássicos no armazenamento conta através do seguinte comando:

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Se o acima comando devolve discos, em seguida, eliminar estes discos com o seguinte comando:

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **Eliminar imagens da VM armazenadas na conta de armazenamento**

    Precedente comando devolve todas as imagens VM com o disco de SO armazenado na conta de armazenamento.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     Precedente comando devolve todas as imagens VM com discos de dados armazenados na conta de armazenamento.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Elimine todas as imagens VM devolvidas pelo acima comandos utilizando o comando anterior:
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

Valide cada conta de armazenamento para migração utilizando o seguinte comando. Neste exemplo, o nome de conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo com o nome da sua própria conta de armazenamento.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

Passo seguinte é para preparar a conta de armazenamento para migração

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Verifique a configuração da conta do storage preparada utilizando o Azure PowerShell ou o portal do Azure. Se não estiver preparada para migração e pretender voltar para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Se a configuração preparada procura boa, pode avançar e consolidar os recursos utilizando o seguinte comando:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral da migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar a CLI para migrar os recursos IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas de Comunidade para prestar assistência com a migração de recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rever as perguntas mais frequentes sobre a migração de IaaS de recursos do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
