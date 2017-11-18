---
title: Criar um cluster do Service Fabric do Linux no Azure | Microsoft Docs
description: Saiba como implementar um cluster do Linux Service Fabric numa rede virtual do Azure existente ao utilizar a CLI do Azure.
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
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 47e023e7240cfae3553b220ebc44c95ec96d62a7
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Implementar um cluster do Service Fabric Linux numa rede virtual do Azure
Este tutorial faz parte de um de uma série. Ficará a saber como implementar um cluster do Linux Service Fabric numa rede virtual do Azure existente (VNET) e subplano net utilizando a CLI do Azure. Quando tiver terminado, tiver um cluster em execução na nuvem que pode implementar aplicações. Para criar um cluster do Windows com o PowerShell, consulte [criar um cluster do Windows seguro no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure utilizando a CLI do Azure
> * Criar um cluster do Service Fabric seguro no Azure utilizando a CLI do Azure
> * Proteger o cluster com um certificado x. 509
> * Ligar ao cluster ao utilizar a CLI de recursos de infraestrutura de serviço
> * Remover um cluster

Este tutorial série, a saber como:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Um cluster de entrada ou saída de escala](/service-fabric-tutorial-scale-cluster.md)
> * [Implementar a gestão de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar o [Service Fabric CLI](service-fabric-cli.md)
- Instalar o [CLI do Azure 2.0](/cli/azure/install-azure-cli)

Os procedimentos seguintes criar um cluster do Service Fabric cinco nós. Para calcular o custo tarifas através da execução de um cluster do Service Fabric em utilização do Azure a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="introduction"></a>Introdução
Este tutorial implementa um cluster de cinco nós de um tipo de nó único numa rede virtual no Azure.

Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Clusters podem dimensionar a milhares de máquinas. Um computador ou a VM que faz parte de um cluster é designado por um nó. Cada nó é atribuído um nome de nó (uma cadeia). Os nós têm características como propriedades de colocação.

Um tipo de nó define o tamanho, o número e propriedades de um conjunto de máquinas virtuais no cluster. Cada tipo de nó definido está configurado como um [conjunto de dimensionamento da máquina virtual](/azure/virtual-machine-scale-sets/), que utiliza para implementar e gerir uma coleção de máquinas virtuais como um conjunto de recursos de computação de um Azure. Cada tipo de nó, em seguida, pode ser escalado para cima ou para baixo de forma independente, têm conjuntos diferentes de portas abertas e pode ter as métricas de capacidade diferentes. Tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front end" ou "back-end".  O cluster pode ter mais do que um tipo de nó, mas o tipo de nó principal tem de ter, pelo menos, cinco VMs de clusters de produção (ou, pelo menos, três VMs para clusters de teste).  [Serviços de sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocadas em nós do tipo de nó principal.

## <a name="cluster-capacity-planning"></a>Planeamento da capacidade do cluster
Este tutorial implementa um cluster de cinco nós de um tipo de nó único.  Para qualquer implementação de cluster de produção, o planeamento de capacidade é um passo importante. Seguem-se alguns aspetos a considerar como parte do processo.

- O número de nó tipos seu cluster precisa 
- As propriedades de cada tipo de nó (por exemplo tamanho, principal, para a internet e número de VMs)
- As características de durabilidade e fiabilidade do cluster

Para obter mais informações, consulte [considerações de planeamento de capacidade de Cluster](service-fabric-cluster-capacity.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Início de sessão para o Azure e selecionar a sua subscrição
Este guia utiliza CLI do Azure. Quando iniciar uma sessão nova, inicie sessão na sua conta do Azure e selecionar a sua subscrição antes de executar os comandos do Azure.
 
Execute o script seguinte para iniciar sessão sua conta do Azure selecione a sua subscrição:

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Criar um novo grupo de recursos para a implementação e atribua-lhe um nome e uma localização.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>Implementar a topologia de rede
Em seguida, configure a topologia de rede à qual serão implementados API Management e o cluster do Service Fabric. O [network.json] [ network-arm] modelo do Resource Manager é configurado para criar uma rede virtual (VNET) e também um grupo de segurança sub-rede e de rede (NSG) para o Service Fabric e uma sub-rede e o NSG para gestão de API . Saiba mais sobre as VNETs, sub-redes e NSGs [aqui](../virtual-network/virtual-networks-overview.md).

O [network.parameters.json] [ network-parameters-arm] ficheiro de parâmetros contém os nomes das sub-redes e NSGs Service Fabric e gestão de API implementar.  API Management está implementada no [seguintes tutorial](service-fabric-tutorial-deploy-api-management.md). Para este guia, os valores de parâmetros não precisam de ser alteradas. Os modelos de serviço Gestor de recursos de infraestrutura utilizam estes valores.  Se os valores forem modificados aqui, deve modificá-las nos outros modelos do Resource Manager utilizados neste tutorial e [tutorial de gestão de API implementar](service-fabric-tutorial-deploy-api-management.md). 

Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:
- [Network.JSON][network-arm]
- [Network.Parameters.JSON][network-parameters-arm]

Utilize o seguinte script para implementar os ficheiros de parâmetros e modelo do Resource Manager para a configuração de rede:

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Implementar o cluster do Service Fabric
Assim que os recursos de rede tem concluído a implementação, o passo seguinte é implementar um cluster do Service Fabric para a VNET na sub-rede e NSG designado para o cluster do Service Fabric. A implementação de um cluster para uma VNET existente e a sub-rede (implementado anteriormente neste artigo) requer um modelo do Resource Manager.  Para obter mais informações, consulte [criar um cluster utilizando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Para esta série tutorial, o modelo está pré-configurada para utilizar os nomes da VNET, uma sub-rede e um NSG que configurou no passo anterior.  

Transfira o ficheiro de modelo e os parâmetros de Gestor de recursos seguinte:
- [linuxcluster.JSON][cluster-arm]
- [linuxcluster.Parameters.JSON][cluster-parameters-arm]

Utilize este modelo para criar um cluster seguro.  Um certificado de cluster é um certificado x. 509 utilizado para proteger a comunicação de nó de nó e autenticar pontos finais de gestão de cluster para um cliente de gestão.  O certificado de cluster também fornece um SSL para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS. O Cofre de chaves do Azure é utilizado para gerir os certificados para clusters de Service Fabric no Azure.  Quando um cluster é implementado no Azure, o fornecedor de recursos do Azure responsável pela criação de clusters de Service Fabric obtém certificados a partir do Cofre de chaves e instala-los em VMs do cluster. 

Pode utilizar um certificado de uma autoridade de certificação (AC), como o certificado de cluster ou, para fins de teste, criar um certificado autoassinado. Tem do certificado de cluster:

- contém uma chave privada.
- criada para a troca de chaves, que é exportável para um ficheiro Personal Information Exchange (. pfx).
- tem um nome de requerente que corresponde ao domínio que utilizar para aceder ao cluster do Service Fabric. Esta correspondência é necessário para fornecer o SSL para o cluster pontos finais de gestão HTTPS e Service Fabric Explorer. Não é possível obter um certificado SSL de uma autoridade de certificação (AC) para o. cloudapp.azure.com domínio. Tem de obter um nome de domínio personalizado para o cluster. Quando solicitar um certificado a partir de uma AC, o nome de requerente do certificado tem de corresponder ao nome de domínio personalizado que utilizar para o cluster.

Preencha estes parâmetros vazios no *linuxcluster.parameters.json* ficheiro para a sua implementação:

|Parâmetro|Valor|
|---|---|
|adminPassword|Palavra-passe #1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|

Deixe o **certificateThumbprint**, **certificateUrlValue**, e **sourceVaultValue** parâmetros em branco para criar um certificado autoassinado.  Se pretender utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha os valores de parâmetros.

O script seguinte utiliza o [Criar cluster de sf az](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) comando e o modelo para implementar um cluster de novo no Azure. O cmdlet também cria um novo cofre de chaves no Azure, adiciona um novo certificado autoassinado para o Cofre de chaves e transfere o ficheiro de certificado localmente. Pode especificar um certificado existente e/ou o Cofre de chaves utilizando outros parâmetros do [Criar cluster de sf az](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) comando.

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Ligar ao cluster seguro
Ligar ao cluster utilizando a CLI de recursos de infraestrutura de serviço `sfctl cluster select` comando utilizando a chave.  Tenha em atenção, utilize apenas o **– Certifique-se de não** opção para um certificado autoassinado.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Verifique se está ligado e o cluster está em bom estado utilizando a `sfctl cluster health` comando.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpar recursos
Os outros artigos nesta série tutorial utilizam o cluster que acabou de criar. Se não estiver imediatamente mover para o artigo seguinte, pode querer eliminar o cluster para evitar incorrer em custos. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos.

Inicie sessão no Azure e selecione o ID de subscrição com o qual pretende remover o cluster.  Pode encontrar o ID de subscrição ao iniciar sessão a [portal do Azure](http://portal.azure.com). Eliminar o grupo de recursos e todos os recursos de cluster utilizando o [eliminação do grupo de az](/cli/azure/group?view=azure-cli-latest#az_group_delete) comando.

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure utilizando a CLI do Azure
> * Criar um cluster do Service Fabric seguro no Azure utilizando a CLI do Azure
> * Proteger o cluster com um certificado x. 509
> * Ligar ao cluster ao utilizar a CLI de recursos de infraestrutura de serviço
> * Remover um cluster

Em seguida, avançar para o tutorial seguinte para saber como dimensionar o seu cluster.
> [!div class="nextstepaction"]
> [Um Cluster de escala](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
