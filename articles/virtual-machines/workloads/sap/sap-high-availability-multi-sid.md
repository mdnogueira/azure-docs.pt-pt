---
title: "Criar uma configuração de várias SID SAP no Azure | Microsoft Docs"
description: "Guia para configuração de várias SID do SAP NetWeaver elevada disponibilidade no Windows máquinas virtuais"
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b48df78df9f53ac7bf0804f55a8d36a2fe2f86b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Criar uma configuração de várias SID do SAP NetWeaver

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

Setembro de 2016, a Microsoft lançou uma funcionalidade onde pode gerir vários endereços IP virtuais utilizando um balanceador de carga interno do Azure. Esta funcionalidade já existe no balanceador de carga externo do Azure.

Se tiver uma implementação de SAP, pode utilizar um balanceador de carga interno para criar uma configuração de cluster do Windows para SAP ASCS/SCS, conforme documentado no [guia para elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide].

Este artigo incida no como mover de uma instalação de ASCS/SCS única para uma configuração de várias SID SAP ao instalar instâncias adicionais de SAP ASCS/SCS em cluster para um cluster de Clustering de ativação pós-falha de servidor ao Windows ' (WSFC) existente. Quando este processo é concluído, será configurou um cluster de múltiplos SID SAP.


## <a name="prerequisites"></a>Pré-requisitos
Já configurou um cluster WSFC que é utilizado para uma instância do SAP ASCS/SCS, tal como explicado o [guia para elevada disponibilidade SAP NetWeaver em VMs do Windows] [ sap-ha-guide] e como é mostrado neste diagrama.

![Instância do SAP ASCS/SCS de elevada disponibilidade][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Arquitetura de destino

O objetivo é instalar vários ASCS de ABAP SAP ou SAP Java SCS agrupados instâncias no mesmo cluster WSFC, como ilustradas aqui:

![Várias instâncias de SAP ASCS/SCS em cluster no Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Não há um limite ao número de IPs front-end privada para cada Balanceador de carga interno do Azure.
>
>O número máximo de instâncias de SAP ASCS/SCS num cluster WSFC é igual ao número máximo de IPs front-end privada para cada Balanceador de carga interno do Azure.
>

Para obter mais informações sobre limites de Balanceador de carga, consulte "IP de front-end privada por Balanceador de carga" em [limites de rede: o Azure Resource Manager][networking-limits-azure-resource-manager].

O horizontal completa com dois sistemas SAP de elevada disponibilidade seria ter este aspeto:

![Configuração de elevada disponibilidade várias-SID SAP com o sistema SAP dois SIDs][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> O programa de configuração tem de cumprir as seguintes condições:
> - As instâncias de SAP ASCS/SCS têm de partilhar o mesmo cluster WSFC.
> - Cada SID DBMS deve ter o seu próprio cluster de WSFC dedicado.
> - Servidores de aplicações SAP que pertençam a um sistema SAP SID tem de ter os seus próprios VMs dedicadas.


## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura
Para preparar a sua infraestrutura, pode instalar uma instância adicional do SAP ASCS/SCS com os seguintes parâmetros:

| Nome do parâmetro | Valor |
| --- | --- |
| SAP ASCS/SCS SID |PR1-lb-ascs |
| Balanceador de carga interno SAP DBMS | PR5 |
| Nome de anfitrião virtual SAP | pr5-sap-cl |
| Endereço IP de anfitrião virtual SAP ASCS/SCS (endereço IP do Balanceador de carga do Azure adicionais) | 10.0.0.50 |
| Número de instância do SAP ASCS/SCS | 50 |
| Porta de sonda do ILB para a instância do SAP ASCS/SCS adicionais | 62350 |

> [!NOTE]
> Para as instâncias de cluster do SAP ASCS/SCS, cada endereço IP necessita de uma porta de pesquisa exclusivo. Por exemplo, se um endereço IP num Balanceador de carga interno do Azure utiliza a porta da sonda 62300, nenhum outro endereço IP que Balanceador de carga pode utilizar a porta da sonda 62300.
>
>Para efeitos de nosso, porque a porta da sonda 62300 já está reservada, estamos a utilizar a porta de sonda 62350.

Pode instalar instâncias adicionais de SAP ASCS/SCS do cluster de WSFC existente com dois nós:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Endereço IP estático |
| --- | --- | --- |
| nó de cluster 1 para a instância ASCS/SCS |PR1-ascs-0 |10.0.0.10 |
| nó de cluster 2nd para a instância ASCS/SCS |PR1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Criar um nome de anfitrião virtual para a instância em cluster do SAP ASCS/SCS no servidor DNS

Pode criar uma entrada DNS para o nome de anfitrião virtual da instância ASCS/SCS utilizando os seguintes parâmetros:

| Novo nome de anfitrião virtual SAP ASCS/SCS | Endereços IP associados |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

O novo nome de anfitrião e endereço IP são apresentados no Gestor de DNS, conforme mostrado na captura de ecrã seguinte:

![Lista de Gestor de DNS, realce a entrada DNS definida para a biblioteca de novo do SAP ASCS/SCS cluster virtual nome e endereço de TCP/IP][sap-ha-guide-figure-6004]

O procedimento para criar uma entrada DNS também está descrito em detalhe no principal [guia para elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> O novo endereço IP que são atribuídas para o nome de anfitrião virtual da instância ASCS/SCS adicional tem de ser o mesmo que o novo endereço IP que atribuiu ao balanceador de carga do Azure de SAP.
>
>No nosso cenário, o endereço IP é 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Adicione um endereço IP a um balanceador de carga interno do Azure existente, utilizando o PowerShell

Para criar mais do que uma instância do SAP ASCS/SCS no mesmo cluster WSFC, utilize o PowerShell para adicionar um endereço IP a um balanceador de carga interno do Azure existente. Cada endereço IP requer as suas próprias regras de balanceamento de carga, porta da sonda, conjunto IP Front-end e conjunto de back-end.

O script seguinte adiciona um novo endereço IP para um balanceador de carga existente. Atualize as variáveis de PowerShell para o seu ambiente. O script irá criar regras de balanceamento de carga todas as necessárias para todas as portas de SAP ASCS/SCS.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Succesfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Depois do script tem de ser executado, os resultados são apresentados no portal do Azure, conforme mostrado na captura de ecrã seguinte:

![Novo conjunto IP Front-end no portal do Azure][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Adicionar discos ao máquinas do cluster e configurar a partilha de disco em cluster SIOS

Tem de adicionar um nova partilha de disco em cluster para cada instância de SAP ASCS/SCS adicionais. Para o Windows Server 2012 R2, o disco de partilha de cluster WSFC atualmente em utilização é a solução de software SIOS DataKeeper.

Faça o seguinte:
1. Adicionar um disco adicional ou discos o mesmo tamanho (o que precisa de stripe) para cada um de nós do cluster e formate-los.
2. Configure a replicação de armazenamento com SIOS DataKeeper.

Este procedimento assume que já instalou SIOS DataKeeper nas máquinas do cluster WSFC. Se instalou, agora tem de configurar a replicação entre as máquinas. O processo é descrito detalhadamente no principal [guia para elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-8.12.3.3].  

![DataKeeper síncrono de espelhamento para o novo SAP ASCS/SCS partilhar o disco][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Implementar as VMs para o DBMS cluster e servidores de aplicação do SAP

Para concluir a preparação de infraestrutura para o sistema SAP segundo, efetue o seguinte:

1. Implemente VMs dedicadas SAP para servidores de aplicações e colocá-los no seu próprio grupo de disponibilidade dedicado.
2. Implemente VMs dedicadas para DBMS cluster e colocá-los no seu próprio grupo de disponibilidade dedicado.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalar o sistema do SAP NetWeaver de SID2 segundo

O processo de conclusão da instalação de um sistema de SAP SID2 segundo é descrito no principal [guia para elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-9].

O procedimento de alto nível é o seguinte:

1. [Instalar o SAP primeiro nó de cluster][sap-ha-guide-9.1.2].  
 Neste passo, estiver a instalar SAP com uma instância ASCS/SCS de elevada disponibilidade no **o nó de cluster de WSFC existente 1**.

2. [Modificar o perfil SAP da instância ASCS/SCS][sap-ha-guide-9.1.3].

3. [Configurar uma porta de sonda][sap-ha-guide-9.1.4].  
 Neste passo, está a configurar um recurso de cluster do SAP porta da sonda SAP-SID2-IP utilizando o PowerShell. Execute esta configuração num de nós do cluster de SAP ASCS/SCS.

4. [Instalar a instância de base de dados] [sap-ha-guia-9.2].  
 Neste passo, estiver a instalar DBMS num cluster de WSFC dedicado.

5. [Instalar o segundo nó de cluster] [sap-ha-guia-9.3].  
 Neste passo, que está a instalar SAP com uma instância ASCS/SCS de elevada disponibilidade num nó de cluster WSFC 2 existente.

6. Abrir portas de Firewall do Windows para a instância de SAP ASCS/SCS e ProbePort.  
 Em ambos os nós de cluster que são utilizados para instâncias de SAP ASCS/SCS, que está a abrir todas as portas de Firewall do Windows que são utilizadas pelo SAP ASCS/SCS. Estas portas são listadas no [guia para elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-8.8].  
 Também abra a porta de sonda de Balanceador de carga interno do Azure, que é 62350 no nosso cenário.

7. [Alterar o tipo de início da instância de serviço do Windows do SAP ERS][sap-ha-guide-9.4].

8. [Instalar o servidor de principal da aplicação do SAP] [ sap-ha-guide-9.5] no novo dedicado VM.

9. [Instalar o servidor de aplicação adicional SAP] [ sap-ha-guide-9.6] no novo dedicado VM.

10. [Testar a ativação pós-falha de instância do SAP ASCS/SCS e replicação SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Passos seguintes

- [Limites de rede: o Azure Resource Manager][networking-limits-azure-resource-manager]
- [Balanceador de carga de vários VIPs do Azure][load-balancer-multivip-overview]
- [Guia para elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide]
