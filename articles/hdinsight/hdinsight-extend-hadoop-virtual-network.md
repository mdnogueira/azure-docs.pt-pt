---
title: Expandir HDInsight com a rede Virtual - Azure | Microsoft Docs
description: Saiba como utilizar a rede Virtual do Azure para ligar o HDInsight para outros recursos em nuvem ou recursos no datacenter
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: larryfr
ms.openlocfilehash: 5835d4ac83b54648b4f7885de577f90008018baa
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Expandir o Azure HDInsight utilizando uma rede Virtual do Azure

Saiba como utilizar o HDInsight com um [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Utilizar uma rede Virtual do Azure permite que os seguintes cenários:

* A ligar ao HDInsight diretamente a partir de uma rede no local.

* A ligar HDInsight aos dados armazena numa rede Virtual do Azure.

* Aceder diretamente aos serviços do Hadoop que não estão disponíveis publicamente através da internet. Por exemplo, Kafka APIs ou a API de Java de HBase.

> [!WARNING]
> As informações neste documento requerem uma compreensão das redes de TCP/IP. Se não estiver familiarizado com redes TCP/IP, deve parceiro com alguém que antes de efetuar alterações a redes de produção.

> [!IMPORTANT]
> Se estiver à procura de documentação de orientação passo a passo sobre a ligação HDInsight para o seu local de rede através de uma rede Virtual do Azure, consulte o [HDInsight ligar à sua rede no local](connect-on-premises-network.md) documento.

## <a name="planning"></a>Planeamento

Seguem-se as questões que tem de responder quando planear instalar HDInsight numa rede virtual:

* Precisa de instalar o HDInsight numa rede virtual existente? Ou pode criar uma nova rede?

    Se estiver a utilizar uma rede virtual existente, poderá ter de modificar a configuração de rede antes de poder instalar HDInsight. Para obter mais informações, consulte o [adicionar HDInsight para uma rede virtual existente](#existingvnet) secção.

* Pretende ligar a rede virtual com o HDInsight para outra rede virtual ou a sua rede no local?

    Trabalhar facilmente com recursos através de redes, poderá ter de criar um DNS personalizado e configurar o reencaminhamento de DNS. Para obter mais informações, consulte o [ligar várias redes](#multinet) secção.

* Que pretende restringir/redirecionar tráfego de entrada ou saído no HDInsight?

    HDInsight tem de ter irrestrito a comunicação com endereços IP específicos no Centro de dados do Azure. Também existem várias portas tem de ser permitidas através de firewalls para comunicação de cliente. Para obter mais informações, consulte o [controlar o tráfego de rede](#networktraffic) secção.

## <a id="existingvnet"></a>Adicionar o HDInsight para uma rede virtual existente

Utilize os passos nesta secção para saber como adicionar um novo HDInsight para uma rede Virtual do Azure existente.

> [!NOTE]
> Não é possível adicionar um cluster do HDInsight existente numa rede virtual.

1. Está a utilizar um clássico ou modelo de implementação do Resource Manager para a rede virtual?

    HDInsight 3.4 e maior necessita de uma rede virtual do Gestor de recursos. Versões anteriores do HDInsight necessária uma rede virtual clássica.

    Se a sua rede existente é uma rede virtual clássica, tem de criar uma rede virtual do Gestor de recursos e, em seguida, ligar os dois. [Ligar as VNets clássicas a novas VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Depois de associados, HDInsight instalado na rede de Gestor de recursos pode interagir com recursos na rede clássico.

2. Utilizar imposição do túnel? Imposição do túnel é uma definição de sub-rede que força o tráfego de Internet de saída para um dispositivo inspeção e registo. HDInsight não suporta a imposição do túnel. Remova a imposição do túnel antes de instalar o HDInsight para uma sub-rede ou crie uma nova subrede para o HDInsight.

3. Utilizar grupos de segurança de rede, as rotas definidas pelo utilizador ou dispositivos de rede Virtual para restringir o tráfego ou a sair da rede virtual?

    Como um serviço gerido, o HDInsight requer acesso sem restrições para vários endereços IP no Centro de dados do Azure. Para permitir a comunicação com estes endereços IP, atualize quaisquer grupos de segurança de rede existentes ou rotas definidas pelo utilizador.

    HDInsight aloja vários serviços, utilizam uma variedade de portas. Não bloqueia o tráfego para estas portas. Para obter uma lista de portas para permitir através de firewalls de aplicação virtual, consulte o [segurança](#security) secção.

    Para localizar a configuração de segurança existente, utilize os seguintes comandos do Azure PowerShell ou a CLI do Azure:

    * Grupos de segurança de rede

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Para obter mais informações, consulte o [resolver problemas relacionados com grupos de segurança de rede](../virtual-network/virtual-network-nsg-troubleshoot-portal.md) documento.

        > [!IMPORTANT]
        > Regras do grupo de segurança de rede são aplicadas por ordem, com base na prioridade da regra. A primeira regra que corresponda ao padrão tráfego é aplicada e não existem outras pessoas são aplicadas para que o tráfego. Regras de ordem de mais permissivo para menos permissiva. Para obter mais informações, consulte o [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) documento.

    * Rotas definidas pelo utilizador

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Para obter mais informações, consulte o [resolver rotas](../virtual-network/virtual-network-routes-troubleshoot-portal.md) documento.

4. Criar um cluster do HDInsight e selecione a rede Virtual do Azure durante a configuração. Utilize os passos nos seguintes documentos para compreender o processo de criação do cluster:

    * [Create HDInsight using the Azure portal (Criar o HDInsight com o portal do Azure)](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Create HDInsight using Azure PowerShell (Criar o HDInsight com o Azure PowerShell)](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Criar HDInsight utilizando a CLI do Azure 1.0](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Criar HDInsight através de um modelo Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > A adição de HDInsight a uma rede virtual é um passo de configuração opcional. Lembre-se de que selecione a rede virtual, quando configurar o cluster.

## <a id="multinet"></a>Ligar a várias redes

O desafio maior com uma configuração de rede multi é resolução do nome entre as redes.

O Azure oferece resolução de nomes para os serviços do Azure que estão instalados numa rede virtual. Esta resolução de nome incorporada permite HDInsight ligar os seguintes recursos, utilizando um nome de domínio completamente qualificado (FQDN):

* Qualquer recurso que está disponível na internet. Por exemplo, microsoft.com, google.com.

* Quaisquer recursos que se encontra na mesma rede Virtual do Azure, utilizando o __nomes DNS internos__ do recurso. Por exemplo, quando utilizar a resolução do nome predefinido, seguem-se nomes DNS internos exemplo atribuídos a nós de trabalho do HDInsight:

    * wn0 hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2 hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Ambos estes nós podem comunicar diretamente com entre si e a outros nós do HDInsight, utilizando nomes DNS internos.

Resolução de nomes predefinido __não__ permitir HDInsight para resolver os nomes dos recursos nas redes que estão associados à rede virtual. Por exemplo, é comum para associar a sua rede no local para a rede virtual. Com apenas a resolução do nome predefinido, HDInsight não é possível aceder aos recursos na rede no local de por nome. O oposto também for VERDADEIRO, a recursos na sua rede no local não é possível aceder a recursos na rede virtual por nome.

> [!WARNING]
> Tem de criar o servidor DNS personalizado e configurar a rede virtual para utilizá-lo antes de criar o cluster do HDInsight.

Para permitir a resolução de nome entre a rede virtual e os recursos nas redes associados, tem de efetuar as seguintes ações:

1. Crie um servidor DNS personalizado na rede Virtual do Azure onde planeia instalar HDInsight.

2. Configure a rede virtual para utilizar o servidor DNS personalizado.

3. Localize que o Azure atribuído o sufixo DNS para a rede virtual. Este valor é semelhante à `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Para informações sobre como localizar o sufixo DNS, consulte o [exemplo: DNS personalizado](#example-dns) secção.

4. Configure o reencaminhamento entre os servidores DNS. A configuração depende do tipo de rede remota.

    * Se a rede remota é uma rede no local, configure o DNS da seguinte forma:
        
        * __DNS personalizado__ (numa rede virtual):

            * Pedidos de reencaminhamento para o sufixo DNS da rede virtual para o resolvedor recursivo do Azure (168.63.129.16). Azure processa pedidos de recursos na rede virtual

            * Reencaminhe todos os outros pedidos para o servidor DNS no local. O DNS no local processa todos os outros pedidos de resolução de nomes, mesmo pedidos de recursos de internet, tais como Microsoft.com.

        * __No local DNS__: reencaminhar pedidos para o sufixo DNS de rede virtual para o servidor DNS personalizado. O servidor DNS personalizado, em seguida, encaminha para o resolvedor recursivo do Azure.

        Este solicitações de configuração de rotas para totalmente qualificado nomes de domínio que contém o sufixo DNS da rede virtual para o servidor DNS personalizado. Todos os outros pedidos (mesmo para endereços de internet públicas) são processados pelo servidor DNS no local.

    * Se a rede remota for outra rede Virtual do Azure, configure o DNS da seguinte forma:

        * __DNS personalizado__ (em cada rede virtual):

            * Pedidos para o sufixo DNS de redes virtuais são reencaminhados para os servidores DNS personalizados. O DNS em cada rede virtual é responsável por resolver recursos dentro da sua rede.

            * Reencaminhe todos os outros pedidos para o resolvedor recursivo do Azure. O resolvedor recursivo é responsável por resolver local e de recursos de internet.

        O servidor DNS para cada rede reencaminha os pedidos para outro, com base no sufixo DNS. Outros pedidos são resolvidos utilizando o resolvedor recursivo do Azure.

    Para obter um exemplo de cada configuração, consulte o [exemplo: DNS personalizado](#example-dns) secção.

Para obter mais informações, consulte o [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) documento.

## <a name="directly-connect-to-hadoop-services"></a>Ligar diretamente a serviços do Hadoop

A maioria das documentação no HDInsight parte do princípio de que tem acesso ao cluster através da internet. Por exemplo, se pode ligar ao cluster em https://CLUSTERNAME.azurehdinsight.net. Este endereço utiliza o gateway público, que não está disponível se tiver utilizado NSGs ou UDRs para restringir o acesso a partir da internet.

Para ligar a Ambari e outras páginas web através da rede virtual, utilize os seguintes passos:

1. Para detetar os nomes de interno de domínio completamente qualificado (FQDN) de nós de cluster do HDInsight, utilize um dos seguintes métodos:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na lista de nós devolvido, localizar o FQDN para os nós principais e utilize os FQDNs para ligar a Ambari e outros serviços web. Por exemplo, utilizar `http://<headnode-fqdn>:8080` aceder Ambari.

    > [!IMPORTANT]
    > Alguns serviços alojados em nós principais só estão ativos num nó numa altura. Se tentar aceder a um serviço num nó principal e devolve um erro 404, mude para outro nó principal.

2. Para determinar o nó e a porta que tem um serviço disponível, consulte o [portas utilizadas pelos serviços do Hadoop no HDInsight](./hdinsight-hadoop-port-settings-for-services.md) documento.

## <a id="networktraffic"></a>Controlar o tráfego de rede

Tráfego de rede num redes virtuais do Azure pode ser controlado utilizando os seguintes métodos:

* **Grupos de segurança de rede** (NSG) permitem-lhe filtrar o tráfego de entrada e saído para a rede. Para obter mais informações, consulte o [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) documento.

    > [!WARNING]
    > HDInsight não suporta a restringir o tráfego de saída.

* **Rotas definidas pelo utilizador** (UDR) definir a forma como o tráfego flui entre os recursos na rede. Para obter mais informações, consulte o [rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md) documento.

* **Aplicações virtuais de rede** replicar a funcionalidade dos dispositivos, tais como routers e firewalls. Para obter mais informações, consulte o [aparelhos de rede](https://azure.microsoft.com/solutions/network-appliances) documento.

Como um serviço gerido, o HDInsight requer acesso sem restrições aos serviços de estado de funcionamento e a gestão do Azure na nuvem do Azure. Ao utilizar os NSGs e UDRs, certifique-se de que HDInsight estes serviços podem ainda comunicar com o HDInsight.

HDInsight expõe serviços em várias portas. Quando utilizar uma firewall de aplicação virtual, tem de permitir tráfego nas portas utilizadas para estes serviços. Para obter mais informações, consulte a secção [as portas necessárias].

### <a id="hdinsight-ip"></a>HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador

Se planeia utilizar **grupos de segurança de rede** ou **rotas definidas pelo utilizador** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifica a região do Azure que pretende utilizar para o HDInsight.

2. Identifica os endereços IP necessários pelo HDInsight. Para obter mais informações, consulte o [endereços IP necessários pelo HDInsight](#hdinsight-ip) secção.

3. Criar ou modificar os grupos de segurança de rede ou as rotas definidas pelo utilizador para a sub-rede que planeia instalar o HDInsight em.

    * __Grupos de segurança de rede__: permitir __entrada__ tráfego na porta __443__ do IP endereços.
    * __Rotas definidas pelo utilizador__: criar uma rota para cada endereço IP e definir o __próximo salto tipo__ para __Internet__.

Para obter mais informações sobre grupos de segurança de rede ou as rotas definidas pelo utilizador, consulte a seguinte documentação:

* [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md)

* [Rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Túnel forçado

Imposição do túnel é uma configuração de encaminhamento definido pelo utilizador em que todo o tráfego de sub-rede é forçado a uma rede específico ou a localização, tal como a sua rede no local. HDInsight __não__ suporte imposição do túnel.

## <a id="hdinsight-ip"></a>Endereços IP necessários

> [!IMPORTANT]
> Os serviços de estado de funcionamento e a gestão do Azure tem de conseguir comunicar com o HDInsight. Se utilizar grupos de segurança de rede ou as rotas definidas pelo utilizador, de permitir tráfego de endereços IP para estes serviços alcançar o HDInsight.
>
> Se não utilizar rotas definidas pelo utilizador ou grupos de segurança de rede para controlar o tráfego, pode ignorar esta secção.

Se utilizar grupos de segurança de rede ou as rotas definidas pelo utilizador, tem de permitir tráfego de serviços do Azure e gestão de estado de funcionamento para alcançar o HDInsight. Utilize os seguintes passos para localizar os endereços IP que tem de ser permitidos:

1. Tem de permitir sempre o tráfego dos seguintes endereços IP:

    | Endereço IP | Porta permitida | Direção |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Entrada |
    | 23.99.5.239 | 443 | Entrada |
    | 168.61.48.131 | 443 | Entrada |
    | 138.91.141.162 | 443 | Entrada |

2. Se o cluster do HDInsight é uma das regiões do seguintes, em seguida, tem de permitir tráfego de endereços IP listados para a região:

    > [!IMPORTANT]
    > Se a região do Azure que está a utilizar não estiver listada, em seguida, apenas utilize os quatro endereços IP do passo 1.

    | País | Região | Endereços IP permitidos | Porta permitida | Direção |
    | ---- | ---- | ---- | ---- | ----- |
    | Ásia | Ásia Oriental | 23.102.235.122</br>52.175.38.134 | 443 | Entrada |
    | &nbsp; | Sudeste Asiático | 13.76.245.160</br>13.76.136.249 | 443 | Entrada |
    | Austrália | Leste da Austrália | 104.210.84.115</br>13.75.152.195 | 443 | Entrada |
    | &nbsp; | Sudeste da Austrália | 13.77.2.56</br>13.77.2.94 | 443 | Entrada |
    | Brasil | Sul do Brasil | 191.235.84.104</br>191.235.87.113 | 443 | Entrada |
    | Canadá | Leste do Canadá | 52.229.127.96</br>52.229.123.172 | 443 | Entrada |
    | &nbsp; | Canadá Central | 52.228.37.66</br>52.228.45.222 | 443 | Entrada |
    | China | China Norte | 42.159.96.170</br>139.217.2.219 | 443 | Entrada |
    | &nbsp; | Leste da China | 42.159.198.178</br>42.159.234.157 | 443 | Entrada |
    | Europa | Europa do Norte | 52.164.210.96</br>13.74.153.132 | 443 | Entrada |
    | &nbsp; | Europa Ocidental| 52.166.243.90</br>52.174.36.244 | 443 | Entrada |
    | Alemanha | Alemanha Central | 51.4.146.68</br>51.4.146.80 | 443 | Entrada |
    | &nbsp; | Alemanha Nordeste | 51.5.150.132</br>51.5.144.101 | 443 | Entrada |
    | Índia | Índia Central | 52.172.153.209</br>52.172.152.49 | 443 | Entrada |
    | Japão | Leste do Japão | 13.78.125.90</br>13.78.89.60 | 443 | Entrada |
    | &nbsp; | Oeste do Japão | 40.74.125.69</br>138.91.29.150 | 443 | Entrada |
    | Coreia | Coreia Central | 52.231.39.142</br>52.231.36.209 | 433 | Entrada |
    | &nbsp; | Coreia do Sul | 52.231.203.16</br>52.231.205.214 | 443 | Entrada
    | Reino Unido | Reino Unido Oeste | 51.141.13.110</br>51.141.7.20 | 443 | Entrada |
    | &nbsp; | Reino Unido Sul | 51.140.47.39</br>51.140.52.16 | 443 | Entrada |
    | Estados Unidos | EUA Central | 13.67.223.215</br>40.86.83.253 | 443 | Entrada |
    | &nbsp; | EUA Centro-Norte | 157.56.8.38</br>157.55.213.99 | 443 | Entrada |
    | &nbsp; | EUA Centro-Oeste | 52.161.23.15</br>52.161.10.167 | 443 | Entrada |
    | &nbsp; | EUA Oeste 2 | 52.175.211.210</br>52.175.222.222 | 443 | Entrada |

    Para obter informações sobre os endereços IP a utilizar para o Azure Government, consulte o [Azure Government Intelligence + análise](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) documento.

3. Se utilizar um servidor DNS personalizado com a sua rede virtual, também tem de permitir acesso a partir de __168.63.129.16__. Este endereço é o resolvedor recursivo do Azure. Para obter mais informações, consulte o [a resolução de nomes para VMs e função instâncias](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) documento.

Para obter mais informações, consulte o [controlar o tráfego de rede](#networktraffic) secção.

## <a id="hdinsight-ports"></a>Portas necessárias

Se planeia utilizar uma rede **firewall de aplicação virtual** para proteger a rede virtual, tem de permitir tráfego de saída nas portas seguintes:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Para obter uma lista de portas para serviços específicos, consulte o [portas utilizadas pelos serviços do Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md) documento.

Para obter mais informações sobre regras de firewall para aplicações virtuais, consulte o [cenário de aplicação virtual](../virtual-network/virtual-network-scenario-udr-gw-nva.md) documento.

## <a id="hdinsight-nsg"></a>Exemplo: grupos de segurança de rede com o HDInsight

Os exemplos nesta secção demonstram como criar regras que permitem o HDInsight comunicar com os serviços de gestão do Azure do grupo de segurança de rede. Antes de utilizar os exemplos, ajuste os endereços IP para corresponder aos perfis para a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

### <a name="azure-resource-management-template"></a>Modelo de gestão de recursos do Azure

O modelo de gestão de recursos seguinte cria uma rede virtual que restringe o tráfego de entrada, mas permite que o tráfego de endereços IP necessários pelo HDInsight. Este modelo também cria um cluster do HDInsight na rede virtual.

* [Implementar uma rede Virtual do Azure seguro e um cluster de Hadoop do HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Altere os endereços IP utilizados neste exemplo, para fazer corresponder a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o seguinte script do PowerShell para criar uma rede virtual que restringe o tráfego de entrada e permite que o tráfego de endereços IP para a região Europa do Norte.

> [!IMPORTANT]
> Altere os endereços IP utilizados neste exemplo, para fazer corresponder a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "blockeverything" `
        -Description "Block everything else" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "*" `
        -SourceAddressPrefix "Internet" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Deny `
        -Priority 500 `
        -Direction Inbound
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

> [!IMPORTANT]
> Este exemplo demonstra como adicionar regras para permitir tráfego de entrada os endereços IP necessários. Não contém uma regra para restringir o acesso de entrada de outras origens.
>
> O exemplo seguinte demonstra como ativar o acesso SSH através da Internet:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>CLI do Azure

Utilize os seguintes passos para criar uma rede virtual que restringe o tráfego de entrada, mas permite que o tráfego de endereços IP necessários pelo HDInsight.

1. Utilize o seguinte comando para criar um novo grupo de segurança de rede com o nome `hdisecure`. Substitua **RESOURCEGROUPNAME** com o grupo de recursos que contém a rede Virtual do Azure. Substitua **localização** com a localização (região) que o grupo foi criado no.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Quando o grupo tiver sido criado, receberá informações sobre o novo grupo.

2. Utilize o seguinte para adicionar regras para o novo grupo de segurança de rede que permitem a comunicação de entrada na porta 443 do serviço de estado de funcionamento e a gestão do Azure HDInsight. Substitua **RESOURCEGROUPNAME** com o nome do grupo de recursos que contém a rede Virtual do Azure.

    > [!IMPORTANT]
    > Altere os endereços IP utilizados neste exemplo, para fazer corresponder a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n block --protocol "*" --source-port-range "*" --destination-port-range "*" --source-address-prefix "Internet" --destination-address-prefix "VirtualNetwork" --access "Deny" --priority 500 --direction "Inbound"
    ```

3. Para obter o identificador exclusivo para este grupo de segurança de rede, utilize o seguinte comando:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Este comando devolve um valor semelhante para o seguinte texto:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilize aspas à volta do id no comando, se não a obter os resultados esperados.

4. Utilize o seguinte comando para aplicar o grupo de segurança de rede a uma sub-rede. Substitua o __GUID__ e __RESOURCEGROUPNAME__ valores com os devolvido do passo anterior. Substitua __VNETNAME__ e __SUBNETNAME__ com o nome da rede virtual e o nome de sub-rede que pretende criar.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Uma vez concluída este comando, pode instalar o HDInsight para a rede Virtual.

> [!IMPORTANT]
> Estes passos apenas abrirem acesso para o serviço de estado de funcionamento e a gestão do HDInsight em nuvem do Azure. Qualquer outro acesso ao cluster do HDInsight de fora da rede Virtual está bloqueado. Para ativar o acesso a partir de fora da rede virtual, tem de adicionar regras de grupo de segurança de rede adicionais.
>
> O exemplo seguinte demonstra como ativar o acesso SSH através da Internet:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a>Exemplo: Configuração de DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Resolução de nome entre uma rede virtual e uma rede ligada no local

Este exemplo faz com que os seguintes pressupostos:

* Tiver uma rede Virtual do Azure que está ligada a uma rede no local utilizando um gateway de VPN.

* O servidor DNS personalizado na rede virtual está a executar o Linux ou Unix como o sistema operativo.

* [Vincular](https://www.isc.org/downloads/bind/) está instalado no servidor DNS personalizado.

No servidor DNS personalizado na rede virtual:

1. Utilize o Azure PowerShell ou a CLI do Azure para localizar o sufixo DNS da rede virtual:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. No servidor DNS personalizado para a rede virtual, utilize o seguinte texto como o conteúdo a `/etc/bind/named.conf.local` ficheiro:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Substitua o `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor com o sufixo DNS da sua rede virtual.

    Esta configuração encaminha todos os pedidos DNS para o sufixo DNS da rede virtual para o resolvedor recursivo do Azure.

2. No servidor DNS personalizado para a rede virtual, utilize o seguinte texto como o conteúdo a `/etc/bind/named.conf.options` ficheiro:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Substitua o `10.0.0.0/16` valor com o intervalo de endereços IP da sua rede virtual. Esta entrada permite que os endereços de pedidos de resolução de nome dentro deste intervalo.

    * Adicione o intervalo de endereços IP da rede no local para o `acl goodclients { ... }` secção.  entrada permite pedidos de resolução de nomes de recursos na rede no local.
    
    * Substitua o valor `192.168.0.1` com o endereço IP do servidor DNS no local. Esta entrada encaminha todos os outros pedidos DNS para o servidor DNS no local.

3. Para utilizar a configuração, reinicie o enlace. Por exemplo, `sudo service bind9 restart`.

4. Adicione um reencaminhador condicional para o servidor DNS no local. Configure o reencaminhador condicional para enviar pedidos para o sufixo DNS do passo 1 para o servidor DNS personalizado.

    > [!NOTE]
    > Consulte a documentação para o software DNS para informações específicas sobre como adicionar um reencaminhador condicional.

Depois de concluir estes passos, pode ligar aos recursos em qualquer rede utilizando nomes de domínio completamente qualificado (FQDN). Agora, pode instalar o HDInsight para a rede virtual.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Resolução de nomes entre duas redes virtuais ligadas

Este exemplo faz com que os seguintes pressupostos:

* Tem duas redes virtuais do Azure estão ligadas através de um gateway de VPN ou peering.

* O servidor DNS personalizado em ambas as redes está a executar o Linux ou Unix como o sistema operativo.

* [Vincular](https://www.isc.org/downloads/bind/) está instalado nos servidores DNS personalizados.

1. Utilize o Azure PowerShell ou a CLI do Azure para localizar o sufixo DNS de ambas as redes virtuais:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Utilize o seguinte texto como o conteúdo a `/etc/bind/named.config.local` ficheiro no servidor DNS personalizado. Efetue esta alteração no servidor DNS personalizado em ambas as redes virtuais.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Substitua o `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor com o sufixo DNS do __outros__ rede virtual. Esta entrada encaminha os pedidos para o sufixo DNS da rede remoto para o DNS personalizado nessa rede.

3. Nos servidores DNS personalizados em ambas as redes virtuais, utilize o seguinte texto como o conteúdo a `/etc/bind/named.conf.options` ficheiro:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Substitua o `10.0.0.0/16` e `10.1.0.0/16` valores com o IP endereço de intervalos de redes virtuais. Esta entrada permite que os recursos em cada rede possa fazer pedidos dos servidores DNS.

    Quaisquer pedidos que não são para os sufixos DNS das redes virtuais (por exemplo, microsoft.com) é processado pelo resolvedor recursivo do Azure.

4. Para utilizar a configuração, reinicie o enlace. Por exemplo, `sudo service bind9 restart` em ambos os servidores DNS.

Depois de concluir estes passos, pode ligar aos recursos na rede virtual, utilizando nomes de domínio completamente qualificado (FQDN). Agora, pode instalar o HDInsight para a rede virtual.

## <a name="next-steps"></a>Passos seguintes

* Para obter um exemplo de ponto a ponto de configuração do HDInsight para ligar a uma rede no local, consulte [HDInsight ligar a uma rede no local](./connect-on-premises-network.md).
* Para configurar clusters de Hbase em redes virtuais do Azure, consulte [clusters criar HBase no HDInsight na Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Para configurar a georreplicação HBase, consulte [configurar a replicação de cluster HBase em redes virtuais do Azure](hbase/apache-hbase-replication.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte o [descrição geral da rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).