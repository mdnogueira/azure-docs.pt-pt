---
title: "Configurar um serviço de escuta externo para grupos de disponibilidade Always | Microsoft Docs"
description: "Este tutorial orienta-o pelos passos da criação de um sempre na disponibilidade escuta do grupo no Azure que seja acessível externamente utilizando o endereço Virtual IP público do serviço de nuvem associado."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 8e506be42aea4fb3c48c29b771a78dcf694f4518
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurar um serviço de escuta externo para grupos de disponibilidade Always no Azure
> [!div class="op_single_selector"]
> * [Serviço de escuta interno](../classic/ps-sql-int-listener.md)
> * [Serviço de escuta externo](../classic/ps-sql-ext-listener.md)
> 
> 

Este tópico mostra como configurar um serviço de escuta para sempre no grupo de disponibilidade externamente acessível na internet. Isto é possibilitado ao associar o serviço em nuvem **IP Virtual público (VIP)** endereço com o serviço de escuta.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

O grupo de disponibilidade pode conter réplicas que estão no local só, Azure apenas, ou span no local e o Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões com várias redes virtuais (VNets). Os passos abaixo assumem que já tem [configurado um grupo de disponibilidade](../classic/portal-sql-alwayson-availability-groups.md) , mas não tiver configurado um serviço de escuta.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Diretrizes e limitações para as escutas de externas
Quando estiver a implementar utilizando o endereço VIP da pubic de serviço em nuvem, tenha em atenção as seguintes diretrizes sobre o serviço de escuta do grupo de disponibilidade no Azure:

* O serviço de escuta do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* A aplicação de cliente têm de residir num serviço cloud diferente daquela que contém o grupo de disponibilidade VMs. Azure não suporta a devolução direta do servidor com o cliente e servidor no mesmo serviço em nuvem.
* Por predefinição, os passos neste artigo mostram como configurar um serviço de escuta para utilizar o endereço de IP Virtual (VIP) do serviço de nuvem. No entanto, é possível reservar e criar vários endereços de VIP do serviço em nuvem. Isto permite-lhe utilizar os passos neste artigo para criar vários serviços de escuta que estejam associados um VIP diferentes. Para obter informações sobre como criar vários endereços VIP, consulte [vários VIPs por serviço em nuvem](../../../load-balancer/load-balancer-multivip.md).
* Se estiver a criar um serviço de escuta para um ambiente híbrido, a rede no local tem de ter conectividade à Internet pública, para além da VPN de site para site com a rede virtual do Azure. Quando a sub-rede do Azure, o serviço de escuta do grupo de disponibilidade é acessível apenas pelo endereço IP público do serviço de nuvem correspondentes.
* Não é suportado para criar um serviço de escuta externo no mesmo serviço em nuvem em que tem também um serviço de escuta interno utilizando o Balanceador de carga interno (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do serviço de escuta
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo foca-se sobre a criação de um serviço de escuta que utiliza **balanceamento de carga externo**. Se pretender que um serviço de escuta é privado à sua rede virtual, consulte a versão deste artigo que fornece os passos para configurar um [serviço de escuta com o ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar os pontos finais VM com balanceamento de carga com direta do servidor retorno
Balanceamento de carga externo utiliza virtual o endereço Virtual IP público do serviço de nuvem que aloja as suas VMs. Por isso, não terá de criar ou configurar o Balanceador de carga neste caso.

Tem de criar um ponto final com balanceamento de carga para cada VM que aloja uma réplica do Azure. Se tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço em nuvem na mesma VNet. As réplicas do grupo de disponibilidade de criação que abrangem várias regiões do Azure necessita de configurar várias VNets. Para obter mais informações sobre como configurar a conectividade de VNet cruzada, consulte [configurar VNet a VNet conectividade](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal do Azure, navegue para cada VM que aloja uma réplica e ver os detalhes.
2. Clique em de **pontos finais** separador para cada uma das VMs.
3. Certifique-se de que o **nome** e **Porta pública** do serviço de escuta ponto final que pretende utilizar não está já em utilização. No exemplo abaixo, o nome é "MyEndpoint" e a porta é "1433".
4. No seu cliente local, transfira e instale [o módulo do PowerShell mais recente](https://azure.microsoft.com/downloads/).
5. Iniciar **o Azure PowerShell**. Uma nova sessão PowerShell é aberta com os módulos de administração do Azure carregados.
6. Executar **Get-AzurePublishSettingsFile**. Este cmdlet direciona-o para um browser para transferir um ficheiro de definições de publicação para um diretório local. Poderão ser-lhe solicitadas as credenciais de início de sessão para a sua subscrição do Azure.
7. Execute o **importação AzurePublishSettingsFile** comando com o caminho do ficheiro de definições de publicação transferido:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Depois do ficheiro de definições de publicação é importado, pode gerir a sua subscrição do Azure na sessão do PowerShell.
    
1. Copie o script do PowerShell abaixo para um editor de texto e defina os valores das variáveis de acordo com o seu ambiente (as predefinições foram fornecidas para alguns parâmetros). Tenha em atenção que se o grupo de disponibilidade abranger regiões do Azure, tem de executar o script de uma vez cada centro de dados para o serviço em nuvem e os nós que residem em que Centro de dados.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Assim que tiver configurado as variáveis, copie o script do editor de texto para a sua sessão do Azure PowerShell para executá-la. Se a linha de comandos ainda mostra >>, escreva ENTER novamente para se certificar de que o script entra em execução.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Certifique-se de que KB2854082 está instalado, se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abrir as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o serviço de escuta do grupo de disponibilidade

Crie o serviço de escuta do grupo de disponibilidade em dois passos. Em primeiro lugar, crie o recurso de cluster de ponto de acesso de cliente e configurar as dependências. Segundo, configure os recursos de cluster com o PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso de cliente e configurar as dependências de cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Balanceamento de carga externa, tem de obter o endereço IP virtual público do serviço em nuvem que contém as réplicas. Inicie sessão no portal do Azure. Navegue para o serviço em nuvem que contém o grupo de disponibilidade VM. Abra o **Dashboard** vista.
2. Tenha em atenção o endereço apresentado em **endereço IP Virtual público (VIP)**. Se a sua solução abrange VNets, repita este passo para cada serviço em nuvem que contenha uma VM que aloja uma réplica.
3. Das VMs, copie o script do PowerShell abaixo para um editor de texto e defina as variáveis para valores que apontou anteriormente.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Uma vez que definiu as variáveis, abra uma janela elevada do Windows PowerShell, em seguida, copie o script do editor de texto e cole a sessão do PowerShell do Azure para executá-la. Se a linha de comandos ainda mostra >>, escreva ENTER novamente para se certificar de que o script entra em execução.
5. Repita esta em cada VM. Este script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros, como a porta da sonda. Quando o recurso de endereço IP seja colocado online, pode, em seguida, responder a consulta na porta sonda do ponto final com balanceamento de carga criado anteriormente no tutorial.

## <a name="bring-the-listener-online"></a>Coloque online o serviço de escuta
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de seguimento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar a escuta do grupo de disponibilidade (na mesma VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testar o serviço de escuta do grupo de disponibilidade (através da internet)
Para poder aceder ao serviço de escuta de fora da rede virtual, tem de utilizar o balanceamento de carga de externo/público (descrito neste tópico) em vez do ILB, que é accesible apenas dentro da mesma VNet. Na cadeia de ligação, especifique o nome do serviço em nuvem. Por exemplo, se tiver um serviço em nuvem com o nome *mycloudservice*, a instrução sqlcmd seria o seguinte:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ao contrário do exemplo anterior, a autenticação do SQL Server tem de ser utilizada, porque a função invocadora não é possível utilizar a autenticação do windows através da internet. Para obter mais informações, consulte [sempre no grupo de disponibilidade na VM do Azure: cenários de conectividade do cliente](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Quando utilizar a autenticação do SQL Server, certifique-se de que cria o início de sessão mesmo em ambas as réplicas. Para obter mais informações sobre resolução de problemas de inícios de sessão com grupos de disponibilidade, consulte [como mapear os inícios de sessão ou utilizar contidos utilizador de base de dados do SQL Server para ligar a outras réplicas e a mapear para bases de dados de disponibilidade](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se as réplicas Always On estiverem em sub-redes diferentes, os clientes têm de especificar **MultisubnetFailover = True** na cadeia de ligação. Isto resulta em tentativas de ligação paralelas réplicas em sub-redes diferentes. Tenha em atenção que este cenário inclui a uma implementação de sempre no grupo de disponibilidade por várias regiões.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

