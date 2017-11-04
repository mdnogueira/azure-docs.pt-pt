---
title: "Criar uma VM (clássica) com vários NICs - CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como criar uma VM (clássica) com vários NICs com a interface de linha de comandos do Azure (CLI) 1.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8555bd830583f51164d39ca0e7b95813b7d35965
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Criar uma VM (clássica) com vários NICs com a CLI do Azure 1.0

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma das suas VMs. Vários NICs ativar a separação de tipos de tráfego em NICs. Por exemplo, um NIC poderá comunicar com a Internet, enquanto outro comunica apenas com recursos internos, não ligados à Internet. A capacidade de separar o tráfego de rede em vários NICs é necessária para muitas virtual os dispositivos de rede, tais como a entrega de aplicações e soluções de otimização de WAN.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como efetuar estes passos, utilizando o [modelo de implementação do Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Os seguintes passos utilizam um grupo de recursos denominado *IaaSStory* para os servidores WEB e um grupo de recursos denominado *IaaSStory-back-end* para os servidores de base de dados.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar os servidores de base de dados, terá de criar o *IaaSStory* grupo de recursos com todos os recursos necessários para este cenário. Para criar estes recursos, conclua os passos que se seguem. Criar uma rede virtual, seguindo os passos a [criar uma rede virtual](virtual-networks-create-vnet-classic-cli.md) artigo.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implementar as VMs do back-end
As VMs do back-end dependem a criação dos recursos seguintes:

* **Conta de armazenamento para discos de dados**. Para um melhor desempenho, os discos de dados nos servidores de base de dados utilizará a tecnologia de unidade (SSD) de estado sólido, que requer uma conta de armazenamento premium. Certifique-se a localização do Azure, pode implementa para suportar o armazenamento premium.
* **NICs**. Cada VM terá dois NICs, um para acesso de base de dados e outro para gestão.
* **Conjunto de disponibilidade**. Todos os servidores de base de dados serão adicionados a uma único disponibilidade definido, para garantir a pelo menos uma das VMs está ativo e em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Passo 1 – iniciar o script
Pode transferir o script de deteção completo utilizado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Conclua os passos seguintes para alterar o script para funcionar no seu ambiente:

1. Alterar os valores das variáveis abaixo com base no seu grupo de recursos existente implementado acima no [pré-requisitos](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Altere os valores das variáveis abaixo com base nos valores que pretende utilizar para a implementação de back-end.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passo 2 – criar recursos necessários para as suas VMs
1. Crie um novo serviço em nuvem para todas as VMs do back-end. Tenha em atenção a utilização do `$backendCSName` variável para o nome do grupo de recursos, e `$location` para a região do Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Crie uma conta de armazenamento premium para os SO e discos de dados a serem utilizadas pela sua VMs.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Passo 3 – criar VMs com vários NICs
1. Iniciar um ciclo para criar várias VMs, com base no `numberOfVMs` variáveis.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Para cada VM, especifique o nome e endereço IP de cada um dos dois NICs.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Crie a VM. Tenha em atenção a utilização do `--nic-config` parâmetro, que contém uma lista de todos os NICs com nome, uma sub-rede e endereço IP.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Para cada VM, crie dois discos de dados.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Passo 4 - execute o script
Agora que transferiu e alterou o script com base nas suas necessidades, execute o script para criar as VMs de base de dados de back-end com vários NICs.

1. Guarde o script e execute-à partir do **Bash** terminal. Irá ver o resultado inicial, conforme mostrado abaixo.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Após alguns minutos, irá terminar a execução e irá ver o resto do resultado, conforme mostrado abaixo.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Passo 5 - configurar o encaminhamento dentro do sistema de operativo da VM

Azure DHCP atribui um gateway predefinido para a primeira interface de rede (principal) ligada à máquina virtual. Azure não atribuir um gateway predefinido para interfaces de rede (secundário) adicionais ligado a uma máquina virtual. Por conseguinte, não é possível comunicar com recursos fora da sub-rede de uma interface de rede secundária, por predefinição. Interfaces de rede secundárias podem, no entanto, comunicar com os recursos fora da sua sub-rede. Para configurar o encaminhamento para interfaces de rede secundárias, consulte o artigo [encaminhamento dentro de um sistema de operativo da máquina virtual com várias interfaces de rede](virtual-network-network-interface-vm.md#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces).
