---
title: Configurar um cluster do Azure Service Fabric | Microsoft Docs
description: "Guia de introdução - criar um cluster do Service Fabric do Windows ou Linux no Azure."
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
ms.date: 08/24/2017
ms.author: ryanwi
ms.openlocfilehash: de7fa7e6445e6eaf08bdcc8ae812611f20a98c34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Crie o primeiro cluster Service Fabric no Azure
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Este guia de introdução ajuda-o a criar um cluster de cinco nós, em execução no Windows ou Linux, através do [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) ou do [portal do Azure](http://portal.azure.com) em apenas alguns minutos.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Inicie sessão no portal do Azure em [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Criar o cluster

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** a partir do painel **Novo** e, em seguida, selecione **Cluster do Service Fabric** a partir do painel **Computação**.
3. Preencha o formulário **Informações básicas** do Service Fabric. Para o **Sistema operativo**, selecione a versão do Windows ou do Linux que pretende que os nós do cluster executem. O nome de utilizador e palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. Para o **Grupo de recursos** crie um novo. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são criados e geridos coletivamente. Quando terminar, clique em **OK**.

    ![Saída do programa de configuração do cluster][cluster-setup-basics]

4. Preencha o formulário **Configuração de cluster**.  Para a **contagem do tipo de Nó**, introduza "1".

5. Selecione **Tipo de Nó 1 (Primário)** e preencha o formulário **Configuração do tipo de nó**.  Introduza um nome de tipo de nó e defina o [Escalão de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) para "Bronze."  Selecione um tamanho de VM.

    Os tipos de nós definem o tamanho da VM, o número de VMs, os pontos finais personalizados e outras definições para as VMs desse tipo. Cada tipo de nó definido é configurado como um conjunto de dimensionamento de máquinas virtuais separado, que é utilizado para implementar e gerir as máquinas virtuais como um conjunto. Cada tipo de nó pode ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente.  O primeiro, ou primário, tipo de nó é onde os serviços do sistema do Service Fabric estão alojados e devem ter cinco ou mais VMs.

    Para qualquer implementação de produção, o [planeamento da capacidade](service-fabric-cluster-capacity.md) é um passo importante.  Para este guia de introdução, no entanto, não está a executar aplicações, pelo que deve selecionar um tamanho da VM *DS1_v2 Standard* .  Selecione "Prata" para o [escalão de fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) e uma capacidade do conjunto de dimensionamento de máquinas virtuais inicial de cinco.  

    Os pontos finais personalizados abrem portas no balanceador de carga do Azure para que se possa ligar a aplicações em execução no cluster.  Introduza "80, 8172" para abrir as portas 80 e 8172.

    Não assinale a caixa **Configurar definições avançadas**, que é utilizada para personalizar os pontos finais de gestão TCP/HTTP, os intervalos de portas de aplicação, as [restrições de posicionamento](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) e as [propriedades de capacidade](service-fabric-cluster-resource-manager-metrics.md).    

    Selecione **OK**.

6. No formulário **Configuração de cluster**, defina os **Diagnósticos** como **Ativados**.  Para este guia de introdução, não tem de introduzir quaisquer propriedades de [definição de recursos de infraestrutura](service-fabric-cluster-fabric-settings.md).  Na **versão dos Recursos de infraestrutura**, selecione o modo de atualização **Automático**, para que a Microsoft atualize automaticamente a versão do código dos recursos de infraestrutura em execução no cluster.  Defina o modo para **Manual**, se pretender [escolher uma versão suportada](service-fabric-cluster-upgrade.md) para se atualizar. 

    ![Configuração do tipo de nó][node-type-config]

    Selecione **OK**.

7. Preencha o formulário **Segurança**.  Para este guia de introdução, selecione **Inseguro**.  É altamente recomendado que crie um cluster seguro para cargas de trabalho de produção, uma vez que qualquer pessoa pode ligar a um cluster inseguro anonimamente e realizar operações de gestão.  

    Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, consulte [Service Fabric cluster security scenarios (Cenários de segurança do cluster do Service Fabric)](service-fabric-cluster-security.md).  Para ativar a autenticação de utilizador com o Azure Active Directory ou configurar certificados para a segurança da aplicação, [crie um cluster a partir de um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Selecione **OK**.

8. Reveja o resumo.  Se gostaria de transferir um modelo do Resource Manager criado a partir das definições que introduziu, selecione **Transferir modelo e parâmetros**.  Selecione **Criar** para criar o cluster.

    Pode ver o progresso da criação nas notificações. (Clique no ícone de "Sino", junto a barra de estado no canto superior direito do ecrã.) Se clicou em **Afixar ao Startboard** ao criar o cluster, verá **Implementar o Cluster do Service Fabric** afixado ao painel de **Início**.

### <a name="connect-to-the-cluster-using-powershell"></a>Ligar ao cluster com o PowerShell
Certifique-se de que o cluster está em execução ao ligar ao PowerShell.  O módulo do ServiceFabric PowerShell é instalado com o [SDK do Service Fabric](service-fabric-get-started.md).  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma ligação ao cluster.   

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
Um cluster do Service Fabric é constituído por outros recursos do Azure, além do próprio recurso do cluster. Por isso, para eliminar completamente um cluster do Service Fabric também tem de eliminar todos os recursos de que é constituído. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos. Para outras formas de eliminar um cluster ou para eliminar alguns (embora não todos) recursos num grupo de recursos, consulte [Delete a cluster (Eliminar um cluster)](service-fabric-cluster-delete.md)

Eliminar um grupo de recursos no portal do Azure:
1. Navegue para o cluster do Service Fabric que pretende eliminar.
2. Clique no nome **Grupo de Recursos** na página de conceitos básicos do cluster.
3. Na página **Conceitos Básicos do Grupo de Recursos**, clique em **Eliminar grupo de recursos** e siga as instruções nessa página para concluir a eliminação do grupo de recursos.
    ![Eliminar o grupo de recursos][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-windows-cluster"></a>Utilizar o Azure Powershell para implementar um cluster Windows seguro
1. Transfira o [módulo Azure Powershell versão 4.0 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para o computador.

2. Abra uma janela do Windows PowerShell e execute o comando seguinte. 
    
    ```powershell

    Get-Command -Module AzureRM.ServiceFabric 
    ```

    Deverá ver um resultado semelhante ao seguinte.

    ![ps-list][ps-list]

3. Inicie sessão no Azure e selecione a subscrição para a qual pretende criar o cluster.

    ```powershell

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId "Subcription ID" 
    ```

4. Execute o seguinte comando para criar agora um cluster seguro. Não se esqueça de personalizar os parâmetros. 

    ```powershell
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
    $RDPuser="vmadmin"
    $RGname="mycluster" # this is also the name of your cluster
    $clusterloc="SouthCentralUS"
    $subname="$RGname.$clusterloc.cloudapp.azure.com"
    $certfolder="c:\mycertificates\"
    $clustersize=1 # can take values 1, 3-99

    New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder
    ```

    O comando pode demorar de 10 minutos a 30 minutos a concluir e, no final do mesmo, deverá obter um resultado semelhante ao seguinte. A saída contém informações sobre o certificado, o KeyVault para onde foi carregado e a pasta local onde o certificado é copiado. 

    ![ps-out][ps-out]

5. Copie a saída completa e guarde num ficheiro de texto porque temos de consultá-la. Tome nota das seguintes informações da saída. 

    - **CertificateSavedLocalPath** : c:\mycertificates\mycluster20170504141137.pfx
    - **CertificateThumbprint** : C4C1E541AD512B8065280292A8BA6079C3F26F10
    - **ManagementEndpoint** : https://mycluster.southcentralus.cloudapp.azure.com:19080
    - **ClientConnectionEndpointPort** : 19000

### <a name="install-the-certificate-on-your-local-machine"></a>Instalar o certificado no computador local
  
Para ligar ao cluster, terá de instalar o certificado no arquivo pessoal (O meu) do utilizador atual. 

Execute o PowerShell seguinte

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

Está agora pronto para ligar ao seu cluster seguro.

### <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro 

Execute o seguinte comando do PowerShell para ligar a um cluster seguro. Os detalhes do certificado têm de corresponder a um certificado que foi utilizado para configurar o cluster. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


O exemplo seguinte mostra os parâmetros concluídos: 

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

Remove-AzureRmResourceGroup -Name $RGname -Force

```
## <a name="use-azure-cli-to-deploy-a-secure-linux-cluster"></a>Utilizar a CLI do Azure para implementar um cluster Linux seguro

1. Instale o [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) no seu computador.
2. Inicie sessão no Azure e selecione a subscrição na qual pretende criar o cluster.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Execute o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) para criar um cluster seguro.

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
    # the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Ligar ao cluster
Execute o seguinte comando da CLI para ligar ao cluster com o certificado.  Quando utilizar um certificado de cliente para autenticação, os detalhes do certificado têm de coincidir com um certificado implementado nos nós do cluster.  Utilize a opção `--no-verify` para um certificado autoassinado.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Execute o comando seguinte para verificar se está ligado e se o cluster está em bom estado de funcionamento.

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Ligar diretamente aos nós 

Para ligar aos nós num cluster do Linux, pode utilizar o SSH ao especificar um número de porta de 3389 em diante. Por exemplo, para o cluster de cinco nós criado anteriormente, os comandos seriam os seguintes:
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
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
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG
