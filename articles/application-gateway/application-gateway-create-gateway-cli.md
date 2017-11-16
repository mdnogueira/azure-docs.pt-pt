---
title: "Criar um gateway de aplicação - Azure CLI 2.0 | Microsoft Docs"
description: "Saiba como criar um gateway de aplicação com o 2.0 CLI do Azure no Resource Manager."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 9d3732d538f3ed9ecb87247f378a3736692025ca
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Criar um gateway de aplicação utilizando o 2.0 CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [PowerShell clássico do Azure](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure 1.0](application-gateway-create-gateway-cli.md)
> * [CLI 2.0 do Azure](application-gateway-create-gateway-cli.md)

Gateway de aplicação do Azure é uma aplicação virtual dedicada que fornece o controlador de entrega de aplicações (ADC) como um serviço, oferta vários grupos de capacidades de balanceamento de carga de camada 7 para a sua aplicação.

## <a name="cli-versions"></a>Versões do CLI

Pode criar um gateway de aplicação utilizando uma das seguintes versões de interface de linha de comandos (CLI):

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md): CLI do Azure para clássica e modelos de implementação Azure Resource Manager
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md): CLI de próxima geração para o modelo de implementação Resource Manager

## <a name="prerequisite-install-the-azure-cli-20"></a>Pré-requisito: Instalar a CLI do Azure 2.0

Para efetuar os passos neste artigo, terá de [instalar a CLI do Azure para macOS, Linux e Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Precisa de uma conta do Azure para criar um gateway de aplicação. Se não tiver uma, inscreva-se um [avaliação gratuita](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Neste cenário, irá aprender a criar um gateway de aplicação utilizando o portal do Azure.

Neste cenário irão:

* Crie um gateway de aplicação médias com duas instâncias.
* Crie uma rede virtual denominada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
* Criar uma sub-rede denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como bloco CIDR.

> [!NOTE]
> A configuração adicional do gateway de aplicação, incluindo sondas de estado de funcionamento personalizado, endereços de conjunto back-end e regras adicionais, ocorre depois do gateway de aplicação é criado e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

Um gateway de aplicação requer a sua própria sub-rede. Quando estiver a criar uma rede virtual, certifique-se de que deixe suficiente espaço de endereços de várias sub-redes. Depois de implementar um gateway de aplicação a uma sub-rede, pode adicionar apenas os gateways de aplicação adicionais a essa sub-rede.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o **linha de comandos do Microsoft Azure** e iniciar sessão:

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Também pode utilizar `az login` sem que o comutador para início de sessão de dispositivo que necessita de introduzir um código no aka.ms/devicelogin.

Depois de introduzir o comando anterior, irá receber um código. Aceda à https://aka.ms/devicelogin num browser para continuar o processo de início de sessão.

![o início de sessão do cmd que mostra dispositivos][1]

No browser, introduza o código que recebeu. Isto redireciona-o a uma página de início de sessão.

![browser para introduzir o código][2]

Introduza o código para iniciar sessão e, em seguida, feche o browser para continuar.

![sessão iniciada com êxito][3]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway de aplicação, crie um grupo de recursos para o contêm. Utilize o seguinte comando:

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Utilize os endereços IP back-end para os endereços IP do servidor back-end. Estes valores podem ser IPs privados numa rede virtual, os IPs públicos ou nomes de domínio totalmente qualificados para os servidores de back-end. O exemplo seguinte cria um gateway de aplicação com configurações adicionais para as definições, portas e as regras de HTTP:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

O exemplo anterior mostra várias propriedades que não são necessárias durante a criação de um gateway de aplicação. Exemplo de código seguinte cria um gateway de aplicação com as informações necessárias:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> Para obter uma lista de parâmetros a utilizar durante a criação, execute o seguinte comando: `az network application-gateway create --help`.

Este exemplo cria um gateway de aplicação básico com as predefinições para o serviço de escuta, conjunto back-end, as definições HTTP de back-end e regras. Pode modificar estas definições de acordo com a implementação, após o aprovisionamento é efetuada com êxito.

Se a aplicação web foi definida com o conjunto de back-end nos passos anteriores, o balanceamento de carga começa agora.

## <a name="get-the-application-gateway-dns-name"></a>Obter o nome DNS do gateway de aplicação
Depois de criar o gateway, em seguida, configure o front-end para comunicação. Quando estiver a utilizar um IP público, o gateway de aplicação requer um nome DNS dinamicamente atribuído, o que não é amigável. Para garantir que os utilizadores podem aceder o gateway de aplicação, utilize um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [DNS do Azure de utilização para fornecer definições de domínio personalizado para um serviço do Azure](../dns/dns-custom-domain.md).

Para configurar um alias, obter detalhes sobre o gateway de aplicação e o seu nome IP/DNS associado utilizando o elemento de PublicIPAddress ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME, que aponta de aplicações dois web para este nome DNS. Não utilize registos porque o VIP pode ser alterado de reiniciar o gateway de aplicação.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Para eliminar todos os recursos criados neste artigo, execute o seguinte comando:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Passos seguintes

Para saber como criar sondas de estado de funcionamento personalizados, aceda à [criar uma sonda personalizada para o Gateway de aplicação utilizando o portal](application-gateway-create-probe-portal.md).

Para saber como configurar a descarga de SSL e tomar a desencriptação de SSL dispendiosa desativar os servidores web, consulte [configurar um gateway de aplicação para a descarga SSL de utilizando o Azure Resource Manager](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
