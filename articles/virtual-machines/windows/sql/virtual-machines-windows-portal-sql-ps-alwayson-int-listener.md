---
title: "Configurar sempre nos serviços de escuta do grupo de disponibilidade – Microsoft Azure | Microsoft Docs"
description: "Configure serviços de escuta de grupo de disponibilidade no modelo Azure Resource Manager, utilizando um balanceador de carga interno com um ou mais endereços IP."
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/22/2017
ms.author: mikeray
ms.openlocfilehash: 74fa1e4c9cfa608a9a385f3dd82a0599fbcc421c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar um ou mais Always On disponibilidade grupo escuta - Resource Manager
Este tópico mostra como:

* Crie um balanceador de carga interno para grupos de disponibilidade do SQL Server utilizando cmdlets do PowerShell.
* Adicione endereços IP adicionais a um balanceador de carga mais do que um grupo de disponibilidade. 

Um serviço de escuta do grupo de disponibilidade é um nome de rede virtual que os clientes ligam para acesso de base de dados. Máquinas virtuais do Azure, um balanceador de carga contém o endereço IP para o serviço de escuta. O load balancer encaminha o tráfego para a instância do SQL Server que está a escutar a porta da sonda. Normalmente, um grupo de disponibilidade utiliza um balanceador de carga interno. Um balanceador de carga interno do Azure pode alojar um ou vários endereços IP. Cada endereço IP utiliza uma porta específica de pesquisa. Este documento mostra como utilizar o PowerShell para criar um balanceador de carga, ou adicionar endereços IP a um balanceador de carga existente para grupos de disponibilidade do SQL Server. 

A capacidade de atribuir vários endereços IP para um balanceador de carga interno é nova no Azure e só está disponível no modelo do Resource Manager. Para concluir esta tarefa, tem de ter um grupo de disponibilidade do SQL Server implementado em máquinas virtuais do Azure no modelo do Resource Manager. Máquinas virtuais do SQL Server têm de pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o [modelo Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Gestor de recursos do Azure. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o Balanceador de carga interno para si. Se preferir, pode [configurar manualmente a um grupo de disponibilidade Always On](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico requer que os grupos de disponibilidade já estão configurados.  

Tópicos relacionados incluem:

* [Configurar grupos de Disponibilidade AlwaysOn na VM do Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurar uma ligação VNet a VNet através do Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurar a Firewall do Windows
Configure a Firewall do Windows para permitir o acesso do SQL Server. As regras de firewall permitem ligações de TCP para a utilização de portas por instância do SQL Server e a sonda do serviço de escuta. Para obter instruções detalhadas, consulte [configurar uma Firewall do Windows para acesso ao motor de base de dados](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crie uma regra de entrada para a porta do SQL Server e para a porta da sonda.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script de exemplo: Criar um balanceador de carga interno com o PowerShell
> [!NOTE]
> Se tiver criado o seu grupo de disponibilidade com o [modelo Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), o Balanceador de carga interno já foi criado. 
> 
> 

O script do PowerShell seguinte cria um balanceador de carga interno, configura a regras de balanceamento de carga e define um endereço IP para o Balanceador de carga. Para executar o script, abra o ISE do Windows PowerShell e cole o script no painel de Script. Utilize `Login-AzureRMAccount` para iniciar sessão no PowerShell. Se tiver várias subscrições do Azure, utilize `Select-AzureRmSubscription ` para configurar a subscrição. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a>Script de exemplo: adicionar um endereço IP para um balanceador de carga existente com o PowerShell
Para utilizar mais do que um grupo de disponibilidade, adicione um endereço IP adicional ao balanceador de carga. Cada endereço IP requer a sua própria regra, a porta de pesquisa e a porta de front-de balanceamento de carga.

A porta de front-end é a porta que as aplicações a utilizar para ligar à instância do SQL Server. Endereços IP para grupos de disponibilidade diferente, podem utilizar a mesma porta front-end.

> [!NOTE]
> Para grupos de disponibilidade do SQL Server, cada endereço IP necessita de uma porta específica de pesquisa. Por exemplo, se um endereço IP num Balanceador de carga utiliza a porta da sonda 59999, não existem outros endereços IP que Balanceador de carga podem utilizar a porta da sonda 59999.

* Para informações sobre limites de Balanceador de carga, consulte **IP privado front-end por Balanceador de carga** em [limites de rede - Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Para obter informações sobre os limites do grupo de disponibilidade, consulte [restrições (grupos de disponibilidade)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O script seguinte adiciona um novo endereço IP para um balanceador de carga existente. O ILB utiliza a porta do serviço de escuta para a porta de front-end de balanceamento de carga. Esta porta pode ser a porta do SQL Server está a escutar. Para instâncias predefinida do SQL Server, a porta é 1433. A regra para um grupo de disponibilidade de balanceamento de carga requer um IP flutuante (devolução direta do servidor) para a porta de back-end é o mesmo que a porta de front-end. Atualize as variáveis para o seu ambiente. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>Configurar o serviço de escuta

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Defina a porta do serviço de escuta no SQL Server Management Studio

1. Inicie o SQL Server Management Studio e ligue-se para a réplica primária.

1. Navegue para **AlwaysOn alta disponibilidade** | **grupos de disponibilidade** | **serviços de escuta do grupo de disponibilidade**. 

1. Deverá ver o nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha. O nome do serviço de escuta com o botão direito e clique em **propriedades**.

1. No **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade com $EndpointPort que utilizou anteriormente (era a predefinição de 1433), em seguida, clique em **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação ao serviço de escuta

Para testar a ligação:

1. RDP para um SQL Server que está a ser a mesma rede virtual, mas não possui a réplica. Isto pode ser o outro servidor de SQL no cluster.

1. Utilize **sqlcmd** utilitário para testar a ligação. Por exemplo, o script seguinte estabelece uma **sqlcmd** ligação para a réplica primária através do serviço de escuta com autenticação do Windows:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Se o serviço de escuta está a utilizar uma porta diferente da predefinição a porta (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd se liga a um serviço de escuta na porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A ligação de SQLCMD liga-se automaticamente para qualquer instância do SQL Server aloja a réplica primária. 

> [!NOTE]
> Certifique-se de que a porta especificada está aberta na firewall de ambos os servidores SQL. Ambos os servidores necessitam de uma regra de entrada para a porta TCP que utiliza. Consulte [adicionar ou Editar regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 
> 
> 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações
Tenha em atenção as seguintes diretrizes no serviço de escuta de grupo de disponibilidade no Azure utilizando o interno Balanceador de carga:

* Com um balanceador de carga interno, aceder apenas o serviço de escuta a partir da mesma rede virtual.


## <a name="for-more-information"></a>Para obter mais informações
Para obter mais informações, consulte [configurar Always On do grupo de disponibilidade na VM do Azure manualmente](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Utilize os seguintes cmdlets PowerShell para criar um balanceador de carga interno para máquinas virtuais do Azure.

* [Novo AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) cria um balanceador de carga. 
* [Novo AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) cria uma configuração de IP Front-end para um balanceador de carga. 
* [Novo AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) cria uma configuração de regra para um balanceador de carga. 
* [Novo AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) cria uma configuração de conjunto de endereços de back-end para um balanceador de carga. 
* [Novo AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) cria uma configuração de pesquisa para um balanceador de carga.
* [Remover AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) remove um balanceador de carga a partir de um grupo de recursos do Azure.
