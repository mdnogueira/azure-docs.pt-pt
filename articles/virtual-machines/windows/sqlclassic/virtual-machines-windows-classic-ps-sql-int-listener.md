---
title: "Configurar um serviço de escuta do ILB para grupos de disponibilidade Always On no Azure | Microsoft Docs"
description: "Este tutorial utiliza recursos criados com o modelo de implementação clássica e cria uma escuta do grupo Always On disponibilidade no Azure que utiliza um balanceador de carga interno."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: fea70b389b1f1d6af963e3f14fdc48e8d857dd53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurar um serviço de escuta do ILB para grupos de disponibilidade Always On no Azure
> [!div class="op_single_selector"]
> * [Serviço de escuta interno](../classic/ps-sql-int-listener.md)
> * [Serviço de escuta externo](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange a utilização do modelo de implementação clássica. Recomendamos que as implementações mais novas utilizem o modelo do Resource Manager.

Para configurar um serviço de escuta para um grupo de disponibilidade Always On no modelo do Resource Manager, consulte [configurar um balanceador de carga para um grupo de disponibilidade Always On no Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

O grupo de disponibilidade pode conter réplicas que são apenas no local ou para o Azure apenas, ou que abrangem no local e o Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões que utilizem várias redes virtuais. Os procedimentos deste artigo partem do princípio de que já tem [configurado um grupo de disponibilidade](../classic/portal-sql-alwayson-availability-groups.md) , mas ainda não tiver configurado um serviço de escuta.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para os serviços de escuta internas
A utilização de um balanceador de carga interno (ILB) com um serviço de escuta do grupo de disponibilidade no Azure está sujeita às seguintes diretrizes:

* O serviço de escuta do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* Escuta do grupo de disponibilidade interna apenas um é suportada para cada serviço em nuvem, porque o serviço de escuta está configurado para o ILB e existe apenas um ILB para cada serviço em nuvem. No entanto, é possível criar vários serviços de escuta externos. Para obter mais informações, consulte [configurar um serviço de escuta externo para grupos de disponibilidade Always On no Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do serviço de escuta
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo foca-se sobre a criação de um serviço de escuta que utiliza um ILB. Se precisar de um serviço de escuta público ou externo, consulte a versão deste artigo que descreve a definição de segurança de um [escuta externa](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar os pontos finais VM com balanceamento de carga com direta do servidor retorno
Primeiro de criar um ILB, executando o script posteriormente nesta secção.

Crie um ponto final com balanceamento de carga para cada VM que aloja uma réplica do Azure. Se tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço em nuvem na mesma rede virtual do Azure. Criação de réplicas do grupo que abrangem várias regiões do Azure de disponibilidade necessita de configurar várias redes virtuais. Para obter mais informações sobre como configurar cruzada conectividade de rede virtual, consulte [configurar a rede virtual para conectividade de rede virtual](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal do Azure, aceda a cada VM que aloja uma réplica para ver os detalhes.

2. Clique em de **pontos finais** separador para cada VM.

3. Certifique-se de que o **nome** e **Porta pública** do serviço de escuta ponto final que pretende utilizar não já estão em utilização. O exemplo nesta secção, o nome é *MyEndpoint*, e a porta é *1433*.

4. No seu cliente local, transfira e instale a versão mais recente [módulo do PowerShell](https://azure.microsoft.com/downloads/).

5. Inicie o PowerShell do Azure.  
    Abre uma nova sessão PowerShell com os módulos de administração do Azure carregados.

6. Execute `Get-AzurePublishSettingsFile`. Este cmdlet direciona-o para um browser para transferir um ficheiro de definições de publicação para um diretório local. Pode ser pedido para as credenciais de início de sessão para a sua subscrição do Azure.

7. Execute o seguinte `Import-AzurePublishSettingsFile` comando com o caminho do ficheiro de definições de publicação transferido:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Após a importação do ficheiro de definições de publicação, pode gerir a sua subscrição do Azure na sessão do PowerShell.

8. Para *ILB*, atribuir um endereço IP estático. Examine a configuração de rede virtual atual, executando o seguinte comando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Tenha em atenção o *sub-rede* de nome para a sub-rede que contém as VMs que alojam as réplicas. Este nome é utilizado no parâmetro $SubnetName no script.

10. Tenha em atenção o *VirtualNetworkSite* nome e a iniciar *AddressPrefix* para a sub-rede que contém as VMs que alojam as réplicas. Procurar um endereço IP disponível através da transmissão de ambos os valores para o `Test-AzureStaticVNetIP` de comandos e ao examinar o *AvailableAddresses*. Por exemplo, se a rede virtual é denominada *MyVNet* e tem um intervalo de endereços de sub-rede que inicia no *172.16.0.128*, o seguinte comando seria lista endereços disponíveis:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Selecione um dos endereços disponíveis e utilizá-lo no parâmetro $ILBStaticIP do script no próximo passo.

12. Copie o seguinte script do PowerShell para um editor de texto e defina os valores das variáveis de acordo com o seu ambiente. As predefinições foram fornecidas para alguns parâmetros.  

    As implementações existentes que utilizam grupos de afinidade não é possível adicionar um ILB. Para obter mais informações sobre os requisitos do ILB, consulte [descrição geral do Balanceador de carga interno](../../../load-balancer/load-balancer-internal-overview.md).

    Além disso, se o grupo de disponibilidade abranger regiões do Azure, tem de executar o script de uma vez cada centro de dados para o serviço em nuvem e os nós que residem em que Centro de dados.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Depois de ter de definir as variáveis, copie o script do editor de texto à sua sessão do PowerShell para executá-la. Se a linha de comandos ainda mostra  **>>** , prima Enter novamente para se certificar de que o script entra em execução.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Certifique-se de que KB2854082 está instalado, se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abrir as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o serviço de escuta do grupo de disponibilidade

Crie o serviço de escuta do grupo de disponibilidade em dois passos. Em primeiro lugar, crie o recurso de cluster de ponto de acesso de cliente e configurar as dependências. Segundo, configure os recursos de cluster no PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso de cliente e configurar as dependências de cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Para o ILB, tem de utilizar o endereço IP do ILB criado anteriormente. Para obter este endereço IP no PowerShell, utilize o seguinte script:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Das VMs, copie o script do PowerShell para o seu sistema operativo para um editor de texto e, em seguida, definir as variáveis para valores que apontou anteriormente.

    Para o Windows Server 2012 ou posterior, utilize o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Para o Windows Server 2008 R2, utilize o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Após definir as variáveis, abra uma janela elevada do Windows PowerShell, cole o script do editor de texto para a sua sessão do PowerShell para executá-la. Se a linha de comandos ainda mostra  **>>** , prima Enter novamente para se certificar de que o script entra em execução.

4. Repita os passos anteriores para cada VM.  
    Este script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros, tais como a porta da sonda. Quando o recurso de endereço IP seja colocado online, pode responder a consulta na porta sonda do ponto de final com balanceamento de carga que criou anteriormente.

## <a name="bring-the-listener-online"></a>Coloque online o serviço de escuta
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de seguimento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>O serviço de escuta do grupo de disponibilidade (dentro da mesma rede virtual) de teste
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
