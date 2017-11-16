---
title: Ligar ao Kafka utilizando redes virtuais - Azure HDInsight | Microsoft Docs
description: "Saiba como ligar diretamente a Kafka no HDInsight através de uma rede Virtual do Azure. Saiba como ligar a Kafka de clientes de desenvolvimento utilizando um gateway de VPN ou de clientes na sua rede no local através da utilização de um dispositivo de gateway VPN."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 2b55de4de6bb94be78649112161211346090b23a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="connect-to-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Ligar ao Kafka no HDInsight através de uma rede Virtual do Azure

Saiba como ligar diretamente a Kafka no HDInsight através de uma rede Virtual do Azure. Este documento fornece informações sobre a ligação ao Kafka utilizando as seguintes configurações:

* A partir dos recursos numa rede no local. Esta ligação é estabelecida utilizando um dispositivo VPN (software ou hardware) na sua rede local.
* A partir de um ambiente de desenvolvimento utilizando um cliente de software VPN.

## <a name="architecture-and-planning"></a>Planeamento e arquitetura

HDInsight não permite a ligação direta para Kafka através da internet pública. Em vez disso, os clientes de Kafka (produtores e consumidores) tem de utilizar um dos seguintes métodos de ligação:

* Execute cliente na mesma rede virtual como Kafka no HDInsight. Esta configuração é utilizada no [começar a utilizar o Apache Kafka no HDInsight](apache-kafka-get-started.md) documento. O cliente é executada diretamente em nós de cluster do HDInsight ou outra máquina virtual na mesma rede.

* Ligar uma rede privada, tais como a sua rede no local, a rede virtual. Esta configuração permite que os clientes na sua rede no local para trabalhar diretamente com Kafka. Para ativar esta configuração, execute as seguintes tarefas:

    1. Crie uma rede virtual
    2. Crie um gateway de VPN que utiliza uma configuração site a site. A configuração utilizada neste documento estabelece ligação a um dispositivo de gateway VPN na sua rede no local.
    3. Crie um servidor DNS na rede virtual.
    4. Configure o reencaminhamento entre o servidor DNS em cada rede.
    5. Instale Kafka no HDInsight para a rede virtual.

    Para obter mais informações, consulte o [ligar ao Kafka partir de uma rede no local](#on-premises) secção. 

* Ligar máquinas individuais para a rede virtual com um gateway de VPN e o cliente VPN. Para ativar esta configuração, execute as seguintes tarefas:

    1. Crie uma rede virtual
    2. Crie um gateway VPN que utiliza uma configuração ponto a site. Esta configuração fornece um cliente VPN que pode ser instalado nos clientes do Windows.
    3. Instale Kafka no HDInsight para a rede virtual.
    4. Configure Kafka de publicidade de IP. Esta configuração permite que o cliente ligar através de endereçamento IP em vez de nomes de domínio.
    5. Transferir e utilizar o cliente VPN no sistema de desenvolvimento.

    Para obter mais informações, consulte o [ligar ao Kafka com um cliente VPN](#vpnclient) secção.

    > [!WARNING]
    > Esta configuração é recomendada apenas para fins de desenvolvimento devido às seguintes limitações:
    >
    > * Cada cliente tem de ligar utilizando um cliente de software VPN. O Azure oferece apenas um cliente baseado em Windows.
    > * O cliente VPN não transmite pedidos de resolução de nome para a rede virtual, pelo que deverá utilizar para comunicar com Kafka de endereçamento IP. Comunicação do IP requer configuração adicional no Kafka cluster.

Para obter mais informações sobre como utilizar o HDInsight numa rede virtual, consulte [expandir HDInsight ao utilizar redes virtuais do Azure](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a>Ligar ao Kafka a partir de uma rede no local

Para criar um cluster de Kafka que comunica com a sua rede no local, siga os passos a [HDInsight ligar à sua rede no local](./../connect-on-premises-network.md) documento.

> [!IMPORTANT]
> Ao criar o cluster do HDInsight, selecione o __Kafka__ tipo de cluster.

Estes passos, crie a seguinte configuração:

* Rede Virtual do Azure
* Gateway de VPN de site a site
* Conta de armazenamento do Azure (utilizada pelo HDInsight)
* Kafka no HDInsight

Para verificar se um cliente Kafka pode ligar ao cluster no local, utilize os passos a [exemplo: cliente Python](#python-client) secção.

## <a id="vpnclient"></a>Ligue Kafka com um cliente VPN

Utilize os passos nesta secção para criar a seguinte configuração:

* Rede Virtual do Azure
* Gateway de VPN ponto a site
* Conta de armazenamento do Azure (utilizado pelo HDInsight)
* Kafka no HDInsight

1. Siga os passos a [trabalhar com certificados autoassinados para ligações ponto a site](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) documento. Este documento cria os certificados necessários para o gateway.

2. Abra uma linha de comandos do PowerShell e utilize o seguinte código para iniciar sessão sua subscrição do Azure:

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Utilize o seguinte código para criar variáveis que contêm informações de configuração:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Utilize o seguinte código para criar o grupo de recursos do Azure e a rede virtual:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > Pode demorar alguns minutos para que este processo esteja concluído.

5. Utilize o seguinte código para criar o contentor de conta de armazenamento do Azure e BLOBs:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Utilize o seguinte código para criar o cluster do HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > Este processo demora cerca de 15 minutos a concluir.

8. Utilize o cmdlet seguinte para obter o URL para o cliente de VPN do Windows para a rede virtual:

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Para transferir o cliente de VPN do Windows, utilize o URI devolvido no seu browser.

### <a name="configure-kafka-for-ip-advertising"></a>Configurar Kafka de publicidade de IP

Por predefinição, o Zookeeper devolve o nome de domínio de mediadores de Kafka aos clientes. Esta configuração não funciona com o cliente de software VPN, como não poderá utilizar a resolução de nomes para entidades na rede virtual. Para esta configuração, utilize os seguintes passos para configurar Kafka anunciará endereços IP em vez de nomes de domínio:

1. Um web browser, aceda ao https://CLUSTERNAME.azurehdinsight.net. Substitua __CLUSTERNAME__ com o nome de Kafka num cluster do HDInsight.

    Quando lhe for pedido, utilize HTTPS nome de utilizador e palavra-passe para o cluster. É apresentado a IU da Web do Ambari para o cluster.

2. Para ver informações no Kafka, selecione __Kafka__ na lista à esquerda.

    ![Lista de serviço com Kafka realçado](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Para ver a configuração de Kafka, selecione __folhas__ do meio superior.

    ![Ligações de folhas de Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Para localizar o __kafka env__ configuração, introduza `kafka-env` no __filtro__ campo no canto superior direito.

    ![Configuração de Kafka, para kafka env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Para configurar Kafka anunciará endereços IP, adicione o seguinte texto para o fim do __modelo de env kafka__ campo:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface de escuta Kafka, introduza `listeners` no __filtro__ campo no canto superior direito.

7. Para configurar Kafka escutar em todas as interfaces de rede, altere o valor no __os serviços de escuta__ campo para `PLAINTEXT://0.0.0.0:9092`.

8. Para guardar as alterações de configuração, utilize o __guardar__ botão. Introduza uma mensagem de texto que descreve as alterações. Selecione __OK__ depois das alterações foram guardadas.

    ![Configuração botão Guardar](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Para evitar erros ao reiniciar Kafka, utilize o __serviço ações__ botão e selecione __ativar no modo de manutenção__. Selecione OK para concluir esta operação.

    ![Ações de serviço, com ative manutenção realçada](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Para reiniciar Kafka, utilize o __reiniciar__ botão e selecione __afetadas todas reinicie__. Confirme o reinício e, em seguida, utilize o __OK__ botão após a conclusão da operação.

    ![Reinicie o botão ao reiniciar todos os afetados realçado](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Para desativar o modo de manutenção, utilize o __serviço ações__ botão e selecione __Ativar desativar modo de manutenção__. Selecione **OK** para concluir esta operação.

### <a name="connect-to-the-vpn-gateway"></a>Ligar ao gateway VPN

Para ligar ao gateway VPN de um __cliente Windows__, utilize o __ligar ao Azure__ secção o [configurar uma ligação ponto a Site](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#clientcertificate) documento.

## <a id="python-client"></a>Exemplo: Cliente de Python

Para validar a conetividade Kafka, utilize os seguintes passos para criar e executar um produtor de Python e de consumidor:

1. Utilize um dos seguintes métodos para obter os endereços IP de nós no Kafka cluster e nome de domínio completamente qualificado (FQDN):

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

    Este script assume que `$resourceGroupName` é o nome do grupo de recursos do Azure que contém a rede virtual.

    Guarde as informações devolvidas para utilização nos passos.

2. Utilize o seguinte para instalar o [kafka python](http://kafka-python.readthedocs.io/) cliente:

        pip install kafka-python

3. Para enviar dados para Kafka, utilize o seguinte código Python:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  # NOTE: you don't need the full list of worker nodes, just one or two.
  producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Substitua o `'kafka_broker'` entradas com os endereços devolvidos do passo 1 nesta secção:

    * Se estiver a utilizar um __cliente VPN de Software__, substitua o `kafka_broker` entradas com o endereço IP do seu nós de trabalho.

    * Se tiver __ativada a resolução do nome através de um servidor DNS personalizado__, substitua o `kafka_broker` entradas com o FQDN de nós de trabalho.

    > [!NOTE]
    > Este código envia a cadeia `test message` para o tópico `testtopic`. A configuração predefinida Kafka no HDInsight é ao criar o tópico, se não existir.

4. Para obter as mensagens de Kafka, utilize o seguinte código Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Substitua o `'kafka_broker'` entradas com os endereços devolvidos do passo 1 nesta secção:

    * Se estiver a utilizar um __cliente VPN de Software__, substitua o `kafka_broker` entradas com o endereço IP do seu nós de trabalho.

    * Se tiver __ativada a resolução do nome através de um servidor DNS personalizado__, substitua o `kafka_broker` entradas com o FQDN de nós de trabalho.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar o HDInsight com uma rede virtual, consulte o [expandir utilizando uma Azure Virtual Network do Azure HDInsight](../hdinsight-extend-hadoop-virtual-network.md) documento.

Para obter mais informações sobre como criar uma rede Virtual do Azure com o gateway de VPN de ponto a Site, consulte os seguintes documentos:

* [Configurar uma ligação ponto a Site utilizando o portal do Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configurar uma ligação ponto a Site com o Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Para obter mais informações sobre como trabalhar com o Kafka no HDInsight, consulte os documentos seguintes:

* [Introdução ao Kafka no HDInsight](apache-kafka-get-started.md)
* [Utilize espelhamento com Kafka no HDInsight](apache-kafka-mirroring.md)
