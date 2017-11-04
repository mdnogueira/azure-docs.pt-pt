---
title: 'Criar e modificar um circuito ExpressRoute do Azure: CLI | Microsoft Docs'
description: Este artigo descreve como criar, aprovisionar, certifique-se, atualizar, eliminar e retirar o aprovisionamento um circuito ExpressRoute com a CLI.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: cd4e31336fd0e90b13f1c3984de89f24e65b052b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Criar e modificar um circuito ExpressRoute com o CLI


Este artigo descreve como criar um circuito ExpressRoute do Azure utilizando a Interface de linha de comandos (CLI). Este artigo também mostra como verificar o estado, update ou delete e desaprovisionar um circuito. Se pretender utilizar um método diferente para trabalhar com circuitos do ExpressRoute, pode selecionar o artigo na lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Antes de começar

* Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli).
* Reveja o [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inicie sessão na sua conta do Azure e selecionar a sua subscrição

Para iniciar a configuração, inicie sessão na sua conta do Azure. Utilize os seguintes exemplos para ajudar na ligação:

```azurecli
az login
```

Verifique as subscrições da conta.

```azurecli
az account list
```

Selecione a subscrição para o qual pretende criar um circuito ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Obter a lista de fornecedores suportados, as localizações e larguras de banda

Antes de criar um circuito do ExpressRoute, terá da lista de fornecedores de conectividade suportados, as localizações e as opções de largura de banda. Os CLI comando 'az rede express route lista--fornecedores de serviços' devolve estas informações, que irá utilizar em passos posteriores:

```azurecli
az network express-route list-service-providers
```

A resposta é semelhante ao seguinte exemplo:

```azurecli
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Verificar a resposta para ver se o seu fornecedor de conectividade é listado. Anote as informações seguintes, que irá necessitar quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora, está pronto para criar um circuito ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito do ExpressRoute

> [!IMPORTANT]
> O circuito ExpressRoute é faturado a partir do momento em que é emitida uma chave de serviço. Execute esta operação quando o fornecedor de conectividade estiver pronto para aprovisionar o circuito.
> 
> 

Se ainda não tiver um grupo de recursos, tem de criar um antes de criar o seu circuito do ExpressRoute. Pode criar um grupo de recursos, executando o seguinte comando:

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

O exemplo seguinte mostra como criar uma Mbps 200 circuito de ExpressRoute através de Equinix em Silicon Valley. Se estiver a utilizar um fornecedor diferente e definições diferentes, substitua essa informação quando efetua o pedido. 

Certifique-se de que especifica a camada correta de SKU e família de SKUS:

* Camada SKU determina se a um padrão de ExpressRoute ou de um suplemento ExpressRoute premium está ativado. Pode especificar padrão para obter o SKU standard ou o "Premium" para o suplemento premium.
* A família SKU determina o tipo de faturação. Pode especificar 'Metereddata' para um plano de dados limitados e 'Unlimiteddata' para um plano de dados ilimitados. Pode alterar o tipo de faturação 'Metereddata' para 'Unlimiteddata', mas não é possível alterar o tipo de 'Unlimiteddata' para 'Metereddata'.


O circuito ExpressRoute é faturado a partir do momento em que é emitida uma chave de serviço. O exemplo seguinte é um pedido para uma nova chave de serviço:

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A resposta contém a chave do serviço.

### <a name="4-list-all-expressroute-circuits"></a>4. Listar todos os circuitos do ExpressRoute

Para obter uma lista de todos os circuitos ExpressRoute que criou, execute o comando 'lista de express route az rede'. Pode obter estas informações em qualquer altura utilizando este comando. Para listar todos os circuitos, efetue a chamada sem parâmetros.

```azurecli
az network express-route list
```

A chave do serviço está listada no *ServiceKey* campo da resposta.

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Pode obter descrições detalhadas do todos os parâmetros, executando o comando utilizando a '-h' parâmetro.

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Enviar a chave do serviço para o seu fornecedor de conectividade para aprovisionamento

'ServiceProviderProvisioningState' fornece informações sobre o estado atual do aprovisionamento do lado do fornecedor de serviços. O estado fornece o estado do lado do Microsoft. Para obter mais informações, consulte o [artigo de fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando cria um novo circuito do ExpressRoute, o circuito é no seguinte estado:

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

O circuito muda para o seguinte estado quando o fornecedor de conectividade está no processo de ativação-lo por si:

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Para que possa ser capazes de utilizar um circuito do ExpressRoute, tem de ser no seguinte estado:

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verificar periodicamente o estado e o estado da chave do circuito

A verificar o estado e o estado da chave do circuito permite-lhe saber quando o fornecedor tem ativado o seu circuito. Depois do circuito tiver sido configurado, 'ServiceProviderProvisioningState' aparece como 'Aprovisionado', conforme mostrado no exemplo seguinte:

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

A resposta é semelhante ao seguinte exemplo:

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Criar a configuração de encaminhamento

Para obter instruções passo a passo, consulte o [configuração de encaminhamento de circuito de ExpressRoute](howto-routing-cli.md) artigo para criar e modificar peerings do circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de 2 conectividade de camada. Se estiver a utilizar um fornecedor de serviço que oferece gerido layer 3 serviços (normalmente, uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Ligar uma rede virtual a um circuito ExpressRoute

Em seguida, ligar uma rede virtual para o circuito do ExpressRoute. Utilize o [ligar redes virtuais para circuitos do ExpressRoute](howto-linkvnet-cli.md) artigo.

## <a name="modify"></a>Modificar um circuito do ExpressRoute

Pode modificar algumas propriedades de um circuito ExpressRoute sem afetar a conectividade. Pode efetuar as seguintes alterações sem período de indisponibilidade:

* Pode ativar ou desativar um suplemento ExpressRoute premium para o seu circuito do ExpressRoute.
* Pode aumentar a largura de banda do circuito do ExpressRoute fornecido está capacidade disponível na porta. No entanto, a desatualização de largura de banda de um circuito não é suportada. 
* Pode alterar o plano de medição de dados limitados aos dados ilimitados. No entanto, a alterar o plano de medição de dados ilimitados para limitados dados não é suportada.
* Pode ativar e desativar *permitir operações clássicas*.

Para obter mais informações sobre limites e limitações, consulte o [FAQ do ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o suplemento ExpressRoute premium

Pode ativar o suplemento ExpressRoute premium para o seu circuito existente usando o seguinte comando:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

O circuito tem agora as funcionalidades de suplemento ExpressRoute premium ativadas. Iremos começar de faturação para a capacidade de suplemento premium, assim que o comando foi executada com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o suplemento ExpressRoute premium

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar recursos que são maiores que o que é permitido para o circuito standard.
> 
> 

Antes de desativar o suplemento ExpressRoute premium, compreenda os seguintes critérios:

* Antes de mudar de premium para standard, tem de se certificar de que tem menos de 10 redes virtuais ligadas ao circuito. Se tiver mais de 10, o pedido de atualização falha e iremos faturar-lhe às taxas de premium.
* Tem de desassociar todas as redes virtuais noutras regiões geopolíticas. Se não desassociar todas as redes virtuais, o pedido de atualização falha e iremos faturar-lhe às taxas de premium.
* A tabela de rota tem de ser inferior a 4000 rotas para peering privado. Se o tamanho da tabela de rota for superior a 4000 rotas, ignora a sessão de BGP. A sessão não será possível reenabled até que o número de prefixos anunciados é abaixo 4,000.

Pode desativar o suplemento ExpressRoute premium para o circuito existente utilizando o exemplo seguinte:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute

Para as opções de largura de banda suportadas para o fornecedor, verifique o [FAQ do ExpressRoute](expressroute-faqs.md). Pode escolher qualquer tamanho maior do que o tamanho do seu circuito existente.

> [!IMPORTANT]
> Se existir capacidade inadequada na porta existente, poderá ter de recriar o circuito ExpressRoute. Não é possível atualizar o circuito se não houver nenhuma capacidade adicional nessa localização.
>
> Não é possível reduzir a largura de banda de um circuito de ExpressRoute sem interrupção. Desatualização de largura de banda requer a retirar o aprovisionamento do circuito ExpressRoute e, em seguida, reaprovisionar um circuito de ExpressRoute novo.
>

Depois de decidir o tamanho que necessário, utilize o seguinte comando para redimensionar o seu circuito:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

O circuito é um tamanho cópias de segurança do lado do Microsoft. Em seguida, tem de contactar o seu fornecedor de conectividade para atualizar as configurações no seu lado para fazer corresponder esta alteração. Depois de efetuar esta notificação, podemos começar a faturação, para a opção de largura de banda atualizado.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de limitado para ilimitado

Pode alterar o SKU de um circuito ExpressRoute, utilizando o exemplo seguinte:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso a ambientes de Resource Manager e clássico

Reveja as instruções em [circuitos do ExpressRoute mover do clássico para o modelo de implementação Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Desaprovisionamento e eliminar um circuito do ExpressRoute

Para retirar o aprovisionamento e eliminar um circuito do ExpressRoute, certifique-se de que compreende os seguintes critérios:

* Tem de desassociar todas as redes virtuais do circuito ExpressRoute. Se esta operação falhar, verifique se as redes virtuais ligadas ao circuito.
* Se o fornecedor de serviço de circuito de ExpressRoute estado de aprovisionamento é **aprovisionamento** ou **aprovisionado**, tem de trabalhar com o fornecedor de serviços para retirar o aprovisionamento do circuito no seu lado. Vamos continuar a reserva de recursos e faturar-lhe até que o fornecedor do serviço concluída desaprovisionamento o circuito e notifica-nos.
* É possível eliminar o circuito se o fornecedor de serviços tem desaprovisionada o circuito. Quando um circuito está desaprovisionado, o fornecedor do serviço de estado de aprovisionamento está definido como **não aprovisionado**. Este deixa de faturação para o circuito.

Pode eliminar o circuito do ExpressRoute, executando o seguinte comando:

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, certifique-se de que efetua as seguintes tarefas:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](howto-routing-cli.md)
* [Ligar a rede virtual para o circuito do ExpressRoute](howto-linkvnet-cli.md)