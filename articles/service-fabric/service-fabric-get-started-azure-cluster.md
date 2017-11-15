---
title: Configurar um cluster do Azure Service Fabric | Microsoft Docs
description: "Este início rápido ajuda-o a criar um cluster do Service Fabric do Windows ou do Linux no Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Crie o primeiro cluster Service Fabric no Azure
Os [clusters do Azure Service Fabric](service-fabric-deploy-anywhere.md) são um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os seus microsserviços são implementados e geridos. Este guia de introdução ajuda-o a criar um cluster de cinco nós, em execução no Windows ou no Linux, através do [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) ou do [portal do Azure](http://portal.azure.com) em apenas alguns minutos. Também pode utilizar a CLI do Azure para esta tarefa.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Inicie sessão no portal do Azure, em [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Criar o cluster

1. No canto superior esquerdo do portal do Azure, selecione **Novo**.
2. Procure **Service Fabric** e selecione **Cluster do Service Fabric** nos resultados devolvidos. Em seguida, selecione **Criar**.
3. Preencha o formulário **Informações básicas** do Service Fabric. Para o **Sistema operativo**, selecione a versão do Windows ou do Linux que pretende que os nós do cluster executem. O nome de utilizador e a palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. Para o **Grupo de recursos** crie um novo. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são criados e geridos coletivamente. Quando tiver terminado, selecione **OK**.

    ![Captura de ecrã do resultado da configuração do cluster][cluster-setup-basics]

4. Preencha o formulário **Configuração de cluster**. Para **Contagem do tipo de nó**, introduza **1**.

5. Selecione **Tipo de nó 1 (Primário)** e preencha o formulário **Configuração do tipo de nó**. Introduza um nome de tipo de nó e defina o [Escalão de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) como **Bronze**. Depois, selecione um tamanho de VM.

    Os tipos de nós definem o tamanho da VM, o número de VMs, os pontos finais personalizados e outras definições para as VMs desse tipo. Cada tipo de nó definido é configurado como um conjunto de dimensionamento de máquinas virtuais separado, que é utilizado para implementar e gerir as máquinas virtuais como um conjunto. Cada tipo de nó pode ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. O primeiro, ou primário, tipo de nó é onde os serviços do sistema do Service Fabric estão alojados. Este tipo de nó tem de ter cinco ou mais VMs.

    Para qualquer implementação de produção, o [planeamento da capacidade](service-fabric-cluster-capacity.md) é um passo importante. Neste início rápido, no entanto, não está a executar aplicações, pelo que deve selecionar o tamanho de VM *DS1_v2 Standard* . Selecione **Prata** no [escalão de fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) e especifique uma capacidade do conjunto de dimensionamento de máquinas virtuais inicial de cinco.  

    Os pontos finais personalizados abrem portas no Balanceador de Carga do Azure, para que se possa ligar a aplicações em execução no cluster.  Introduza **80, 8172** para abrir as portas 80 e 8172.

    Não selecione a caixa **Configurar definições avançadas**, que é utilizada para personalizar os pontos finais de gestão TCP/HTTP, os intervalos de portas de aplicação, as [restrições de posicionamento](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) e as [propriedades de capacidade](service-fabric-cluster-resource-manager-metrics.md).    
    
    ![Captura de ecrã da Configuração do tipo de nó][node-type-config]

    Selecione **OK**.

6. No formulário **Configuração de cluster**, defina os **Diagnósticos** como **Ativados**. Para este guia de introdução, não tem de introduzir quaisquer propriedades de [definição de recursos de infraestrutura](service-fabric-cluster-fabric-settings.md).  Na **versão dos Recursos de infraestrutura**, selecione o modo de atualização **Automático**, para que a Microsoft atualize automaticamente a versão do código dos recursos de infraestrutura em execução no cluster.  Defina o modo para **Manual**, se pretender [escolher uma versão suportada](service-fabric-cluster-upgrade.md) para se atualizar.     

    Selecione **OK**.

7. Preencha o formulário **Segurança**. Neste início rápido, selecione **Inseguro**. Tenha em conta que, de um modo geral, deve criar um cluster seguro para cargas de trabalho de produção. Qualquer pessoa se pode ligar anonimamente a um cluster inseguro e realizar operações de gestão.  

   O Service Fabric utiliza certificados para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações, veja [Service Fabric cluster security scenarios](service-fabric-cluster-security.md) (Cenários de segurança de clusters do Service Fabric). Para ativar a autenticação de utilizador mediante a utilização do Azure Active Directory ou para configurar certificados para a segurança das aplicações, veja[Create a cluster from a Resource Manager template](service-fabric-cluster-creation-via-arm.md) (Criar um cluster a partir de um modelo do Resource Manager).

    Selecione **OK**.

8. Reveja o resumo. Se quiser transferir um modelo do Azure Resource Manager criado a partir das definições que introduziu, selecione **Transferir modelo e parâmetros**. Selecione **Criar** para criar o cluster.

    Pode ver o progresso da criação nas notificações. (Selecione o ícone de "Sino", junto à barra de estado no canto superior direito do ecrã.) Se tiver selecionado **Afixar ao Startboard** ao criar o cluster, verá **Implementar o Cluster do Service Fabric** afixado ao painel **Início**.

### <a name="connect-to-the-cluster-by-using-powershell"></a>Ligar ao cluster com o PowerShell
Ligue através do PowerShell para verificar se o cluster está em execução. O módulo do Service Fabric do PowerShell é instalado com o [SDK do Service Fabric](service-fabric-get-started.md). O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma ligação ao cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
Consulte [Connect to a secure cluster (Ligar a um cluster seguro)](service-fabric-connect-to-secure-cluster.md) para outros exemplos de ligação a um cluster. Depois de ligar ao cluster, utilize o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para apresentar uma lista de nós no cluster e informações de estado para cada nó. O **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster do Service Fabric é constituído por outros recursos do Azure, além do próprio recurso do cluster. Para eliminar completamente um cluster do Service Fabric também tem de eliminar todos os recursos de que é constituído. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos. Para outras formas de eliminar um cluster ou para eliminar alguns (embora não todos) recursos num grupo de recursos, veja [Delete a cluster (Eliminar um cluster)](service-fabric-cluster-delete.md)

Eliminar um grupo de recursos no portal do Azure:
1. Navegue para o cluster do Service Fabric que pretende eliminar.
2. Na página Essentials do cluster, selecione o nome **Grupo de Recursos**.
3. Na página **Essentials do Grupo de Recursos**, selecione **Eliminar grupo de recursos**. Depois, siga as instruções nessa página para concluir a eliminação do grupo de recursos.
    ![Captura de ecrã da página Essentials do Grupo de Recursos, com Eliminar grupo de recursos realçado][cluster-delete]


## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Outra forma de criar o cluster é utilizar o PowerShell. Eis como:

1. Transfira o [módulo do Azure PowerShell versão 4.0 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para o seu computador.

2. Execute o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster) para criar um cluster do Service Fabric com cinco nós, protegido por um certificado x.509. O comando cria um certificado autoassinado e carrega-o para um novo cofre de chaves. O certificado é também copiado para um diretório local. Defina o parâmetro *-OS* para escolher a versão do Windows ou Linux que é executada nos nós do cluster. Personalize os parâmetros conforme necessário. 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    O comando pode demorar entre 10 a 30 minutos a concluir. A saída contém informações sobre o certificado, o cofre de chaves para onde foi carregado e a pasta local para onde é copiado.     

3. Copie a saída completa e guarde num ficheiro de texto (vai consultá-lo mais tarde). Tome nota das informações seguintes da saída: 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>Instalar o certificado no computador local
  
Para ligar ao cluster, instale o certificado no arquivo Pessoal (Meu) do utilizador atual. 

Execute o seguinte:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

Está agora pronto para ligar ao seu cluster seguro.

### <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro 

Execute o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para ligar a um cluster seguro. Os detalhes do certificado têm de corresponder a um certificado que foi utilizado para configurar o cluster. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

O exemplo seguinte mostra os parâmetros de exemplo: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Execute o comando seguinte para verificar se está ligado e se o cluster está em bom estado de funcionamento.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster é constituído por outros recursos do Azure, além do próprio recurso do cluster. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos. 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Outra forma de criar o cluster é utilizar a CLI. Eis como:

1. Instale a [CLI 2.0 do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) no seu computador.
2. Inicie sessão no Azure e selecione a subscrição na qual pretende criar o cluster.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Execute o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) para criar um cluster do Service Fabric com cinco nós, protegido por um certificado x.509. O comando cria um certificado autoassinado e carrega-o para um novo cofre de chaves. O certificado é também copiado para um diretório local. Defina o parâmetro *-os* para escolher a versão do Windows ou Linux que é executada nos nós do cluster. Personalize os parâmetros conforme necessário.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Ligar ao cluster
Execute o seguinte comando da CLI para ligar ao cluster com o certificado.  Se utilizar um certificado de cliente para autenticação, confirme que os detalhes do certificado coincidem com um certificado implementado nos nós do cluster. Utilize a opção `--no-verify` para um certificado autoassinado.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Execute o comando seguinte para verificar se está ligado e se o cluster está em bom estado de funcionamento.

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Ligar diretamente aos nós 

Para ligar aos nós num cluster do Linux, pode utilizar o comando do secure shell (SSH). Especifique um número de porta de 3389 ou superior. Por exemplo, para o cluster de cinco nós criado anteriormente, os comandos seriam os seguintes:
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster é constituído por outros recursos do Azure, além do próprio recurso do cluster. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos. 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes
Agora que configurou um cluster de desenvolvimento, experimente o seguinte:
* [Visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Remover um cluster](service-fabric-cluster-delete.md) 
* [Implementar aplicações com o Powershell](service-fabric-deploy-remove-applications.md)
* [Implementar aplicações com a CLI](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
