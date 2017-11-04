---
title: "Criar uma VM (clássica) com vários NICs - Azure PowerShell | Microsoft Docs"
description: "Saiba como criar uma VM (clássica) com vários NICs com o PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 37f1f5bbd5f39290121414a4c5532abdb2b6f9ae
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Criar uma VM (clássica) com vários NICs com o PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma das suas VMs. Vários NICs ativar a separação de tipos de tráfego em NICs. Por exemplo, um NIC poderá comunicar com a Internet, enquanto outro comunica apenas com recursos internos, não ligados à Internet. A capacidade de separar o tráfego de rede em vários NICs é necessária para muitas virtual os dispositivos de rede, tais como a entrega de aplicações e soluções de otimização de WAN.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como efetuar estes passos, utilizando o [modelo de implementação do Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Os seguintes passos utilizam um grupo de recursos denominado *IaaSStory* para os servidores WEB e um grupo de recursos denominado *IaaSStory-back-end* para os servidores de base de dados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar os servidores de base de dados, terá de criar o *IaaSStory* grupo de recursos com todos os recursos necessários para este cenário. Para criar estes recursos, conclua os passos que se seguem. Criar uma rede virtual, seguindo os passos a [criar uma rede virtual](virtual-networks-create-vnet-classic-netcfg-ps.md) artigo.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Criar as VMs do back-end
As VMs do back-end dependem a criação dos recursos seguintes:

* **Sub-rede de back-end**. Os servidores de base de dados irão fazer parte de uma sub-rede separada, para segregar o tráfego. O script abaixo espera esta sub-rede existir numa vnet com o nome *WTestVnet*.
* **Conta de armazenamento para discos de dados**. Para um melhor desempenho, os discos de dados nos servidores de base de dados utilizará a tecnologia de unidade (SSD) de estado sólido, que requer uma conta de armazenamento premium. Certifique-se a localização do Azure, pode implementa para suportar o armazenamento premium.
* **Conjunto de disponibilidade**. Todos os servidores de base de dados serão adicionados a uma único disponibilidade definido, para garantir a pelo menos uma das VMs está ativo e em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Passo 1 – iniciar o script
Pode transferir o script do PowerShell completo utilizado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga os passos abaixo para alterar o script para funcionar no seu ambiente.

1. Alterar os valores das variáveis abaixo com base no seu grupo de recursos existente implementado acima no [pré-requisitos](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Altere os valores das variáveis abaixo com base nos valores que pretende utilizar para a implementação de back-end.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passo 2 – criar recursos necessários para as suas VMs
Terá de criar um novo serviço de nuvem e de uma conta de armazenamento para os discos de dados para todas as VMs. Também tem de especificar uma imagem e uma conta de administrador local para as VMs. Para criar estes recursos, execute os seguintes passos:

1. Crie um novo serviço de nuvem.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Crie uma nova conta de armazenamento premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Defina a conta de armazenamento criada acima, como a conta de armazenamento atual da sua subscrição.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Selecione uma imagem para a VM.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Defina as credenciais da conta de administrador local.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Passo 3 – criar VMs
Tem de utilizar um ciclo para criar VMs tantos à medida que pretende e criar o NICs e VMs necessário dentro do ciclo. Para criar o NICs e VMs, execute os seguintes passos.

1. Iniciar um `for` cíclicas repetir os comandos para criar uma VM e dois NICs como tantas vezes quantas as necessárias, com base no valor da `$numberOfVMs` variável.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Criar um `VMConfig` objeto Especifica a imagem, tamanho e conjunto de disponibilidade para a VM.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Aprovisione a VM como uma VM do Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Predefinir a NIC e atribua-lhe um endereço IP estático.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Adicione uma segunda NIC para cada VM.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Crie a discos de dados para cada VM.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Criar cada VM e de fim do ciclo.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Passo 4 - execute o script
Agora que transferiu e alterou o script baseia nas suas necessidades, runt o script para criar a base de dados de back-end VMs com vários NICs.

1. Guarde o script e execute-à partir de **PowerShell** linha de comandos, ou **ISE do PowerShell**. Irá ver o resultado inicial, conforme mostrado abaixo.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Preencha as informações necessárias no pedido de credenciais e clique **OK**. O seguinte resultado é devolvido.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Passo 5 - configurar o encaminhamento dentro do sistema de operativo da VM

Azure DHCP atribui um gateway predefinido para a primeira interface de rede (principal) ligada à máquina virtual. Azure não atribuir um gateway predefinido para interfaces de rede (secundário) adicionais ligado a uma máquina virtual. Por conseguinte, não é possível comunicar com recursos fora da sub-rede de uma interface de rede secundária, por predefinição. Interfaces de rede secundárias podem, no entanto, comunicar com os recursos fora da sua sub-rede. Para configurar o encaminhamento para interfaces de rede secundárias, consulte o artigo [encaminhamento dentro de um sistema de operativo da máquina virtual com várias interfaces de rede](virtual-network-network-interface-vm.md#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces).
