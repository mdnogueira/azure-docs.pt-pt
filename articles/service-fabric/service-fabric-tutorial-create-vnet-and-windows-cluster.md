---
title: Criar um cluster do Windows Service Fabric no Azure | Microsoft Docs
description: Saiba como implementar um cluster do Windows Service Fabric numa rede virtual do Azure existente com o PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: ryanwi
ms.openlocfilehash: 1ac5ca34e412aeb8b24e657abfe8eca04943799d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Implementar um cluster do Service Fabric Windows numa rede virtual do Azure
Este tutorial faz parte de um de uma série. Ficará a saber como implementar um cluster do Windows Service Fabric numa rede virtual do Azure existente (VNET) e subplano net através do PowerShell. Quando tiver terminado, tiver um cluster em execução na nuvem que pode implementar aplicações.  Para criar um cluster de Linux utilizando a CLI do Azure, consulte [criar um cluster com Linux seguro no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure utilizando o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado x. 509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Este tutorial série, a saber como:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Implementar a gestão de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar o [módulo do PowerShell e o SDK de Service Fabric](service-fabric-get-started.md)
- Instalar o [versão 4.1 ou superior do módulo do Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Os procedimentos seguintes criar um cluster do Service Fabric cinco nós. Para calcular o custo tarifas através da execução de um cluster do Service Fabric em utilização do Azure a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Início de sessão para o Azure e selecionar a sua subscrição
Este guia utiliza o Azure PowerShell. Quando inicia uma nova sessão do PowerShell, inicie sessão na sua conta do Azure e selecionar a sua subscrição antes de executar os comandos do Azure.
 
Execute o script seguinte para iniciar sessão sua conta do Azure selecione a sua subscrição:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Criar um novo grupo de recursos para a implementação e atribua-lhe um nome e uma localização.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>Implementar a topologia de rede
Em seguida, configure a topologia de rede à qual serão implementados API Management e o cluster do Service Fabric. O [network.json] [ network-arm] modelo do Resource Manager é configurado para criar uma rede virtual (VNET) e também um grupo de segurança sub-rede e de rede (NSG) para o Service Fabric e uma sub-rede e o NSG para gestão de API . Saiba mais sobre as VNETs, sub-redes e NSGs [aqui](../virtual-network/virtual-networks-overview.md).

O [network.parameters.json] [ network-parameters-arm] ficheiro de parâmetros contém os nomes das sub-redes e NSGs Service Fabric e gestão de API implementar.  API Management está implementada no [seguintes tutorial](service-fabric-tutorial-deploy-api-management.md). Para este guia, os valores de parâmetros não precisam de ser alteradas. Os modelos de serviço Gestor de recursos de infraestrutura utilizam estes valores.  Se os valores forem modificados aqui, deve modificá-las nos outros modelos do Resource Manager utilizados neste tutorial e [tutorial de gestão de API implementar](service-fabric-tutorial-deploy-api-management.md). 

Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:
- [Network.JSON][network-arm]
- [Network.Parameters.JSON][network-parameters-arm]

Utilize o seguinte comando do PowerShell para implementar os ficheiros de parâmetros e modelo do Resource Manager para a configuração de rede:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Implementar o cluster do Service Fabric
Assim que os recursos de rede tem concluído a implementação, o passo seguinte é implementar um cluster do Service Fabric para a VNET na sub-rede e NSG designado para o cluster do Service Fabric. A implementação de um cluster para uma VNET existente e a sub-rede (implementado anteriormente neste artigo) requer um modelo do Resource Manager.  Para esta série tutorial, o modelo está pré-configurada para utilizar os nomes da VNET, uma sub-rede e um NSG que configurou no passo anterior.  

Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:
- [cluster.JSON][cluster-arm]
- [cluster.Parameters.JSON][cluster-parameters-arm]

Utilize este modelo para criar um cluster seguro.  Um certificado de cluster é um certificado x. 509 utilizado para proteger a comunicação de nó de nó e autenticar pontos finais de gestão de cluster para um cliente de gestão.  O certificado de cluster também fornece um SSL para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS. O Cofre de chaves do Azure é utilizado para gerir os certificados para clusters de Service Fabric no Azure.  Quando um cluster é implementado no Azure, o fornecedor de recursos do Azure responsável pela criação de clusters de Service Fabric obtém certificados a partir do Cofre de chaves e instala-los em VMs do cluster. 

Pode utilizar um certificado de uma autoridade de certificação (AC), como o certificado de cluster ou, para fins de teste, criar um certificado autoassinado. Tem do certificado de cluster:

- contém uma chave privada.
- criada para a troca de chaves, que é exportável para um ficheiro Personal Information Exchange (. pfx).
- tem um nome de requerente que corresponde ao domínio que utilizar para aceder ao cluster do Service Fabric. Esta correspondência é necessário para fornecer o SSL para o cluster pontos finais de gestão HTTPS e Service Fabric Explorer. Não é possível obter um certificado SSL de uma autoridade de certificação (AC) para o. cloudapp.azure.com domínio. Tem de obter um nome de domínio personalizado para o cluster. Quando solicitar um certificado a partir de uma AC, o nome de requerente do certificado tem de corresponder ao nome de domínio personalizado que utilizar para o cluster.

Preencha vazia *localização*, *clusterName*, *adminUserName*, e *adminPassword* parâmetros a  *cluster.Parameters.JSON* ficheiro para a sua implementação.  Deixe o *certificateThumbprint*, *certificateUrlValue*, e *sourceVaultValue* parâmetros em branco para criar um certificado autoassinado.  Se pretender utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha os valores de parâmetros.

O script seguinte utiliza o [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet e o modelo para implementar um cluster de novo no Azure. O cmdlet também cria um novo cofre de chaves no Azure, adiciona um novo certificado autoassinado para o Cofre de chaves e transfere o ficheiro de certificado localmente. Pode especificar um certificado existente e/ou o Cofre de chaves utilizando outros parâmetros do [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet.

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname
```

## <a name="connect-to-the-secure-cluster"></a>Ligar ao cluster seguro
Ligar ao cluster utilizando o módulo do PowerShell de Service Fabric instalado com o SDK de Service Fabric.  Em primeiro lugar, instale o certificado para o arquivo pessoal (os meus) do utilizador atual no seu computador.  Execute o seguinte comando do PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Está agora pronto para ligar ao seu cluster seguro.

O **Service Fabric** módulo do PowerShell fornece vários cmdlets para gerir clusters, aplicações e serviços do Service Fabric.  Utilize o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet para ligar ao cluster seguro. Os detalhes de ponto final de ligação e o thumbprint do certificado encontram-se na saída do passo anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique se está ligado e o cluster está em bom estado utilizando a [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpar recursos
Os outros artigos nesta série tutorial utilizam o cluster que acabou de criar. Se não estiver imediatamente mover para o artigo seguinte, pode querer eliminar o cluster e o Cofre de chaves para evitar incorrer em custos. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos.

Eliminar o grupo de recursos e todos os recursos de cluster utilizando o [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Também elimine o grupo de recursos que contém o Cofre de chaves.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure utilizando o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Criar um cluster do Service Fabric seguro no Azure através do PowerShell
> * Proteger o cluster com um certificado x. 509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Em seguida, avançar para o tutorial seguinte para saber como implementar a gestão de API com o Service Fabric.
> [!div class="nextstepaction"]
> [Implementar a gestão de API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
