---
title: "Configurar a descarga de SSL - Gateway de aplicação do Azure - CLI do Azure 2.0 | Microsoft Docs"
description: "Este artigo fornece instruções para criar um gateway de aplicação com SSL offload utilizando o Azure CLI 2.0"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: a2c4062db821e39e1af4fa1d54da0121d3993db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>Configurar um gateway de aplicação para a descarga de SSL com o Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [PowerShell clássico do Azure](application-gateway-ssl.md)
> * [CLI 2.0 do Azure](application-gateway-ssl-cli.md)

Pode configurar o Azure Application Gateway para terminar a sessão SSL (Secure Sockets Layer) no gateway para evitar tarefas dispendiosas de desencriptação de SSL que ocorrem no farm Web. Descarga de SSL também simplifica a gestão de certificados no servidor front-end.

## <a name="prerequisite-install-the-azure-cli-20"></a>Pré-requisito: Instalar a CLI do Azure 2.0

Para efetuar os passos neste artigo, terá de [instalar interface de linha de comandos do Azure para Mac, Linux e Windows (CLI do Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="required-components"></a>Componentes necessários

* **Agrupamento de servidores de back-end**: A lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um endereço IP público ou endereço IP virtual (VIP).
* **Definições de agrupamento de servidores de back-end**: cada conjunto tem definições como a porta, protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end**: esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta**: O serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https; estas definições são maiúsculas e minúsculas) e o nome do certificado SSL (se configurar o SSL offload).
* **Regra**: A regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end para direcionar o tráfego para quando chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deverá passar para *Https* (sensível às maiúsculas e minúsculas). Adicionar o **SslCertificate** elemento **HttpListener** com o valor da variável configurado para o certificado SSL. A porta de front-end deve ser atualizada para **443**.

**Para ativar a afinidade com base em cookies**: pode configurar um gateway de aplicação para se certificar de que um pedido de uma sessão de cliente é sempre direcionado para a mesma VM no web farm. Para tal, insira um cookie de sessão que permite ao gateway direcionar adequadamente o tráfego. Para ativar a afinidade com base em cookies, defina **CookieBasedAffinity** como *Ativado* no elemento **BackendHttpSettings**.

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>Configurar a descarga de SSL num gateway de aplicação existente

Introduza os comandos seguintes para configurar a descarga de SSL para um gateway de aplicação existente:

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>Criar um gateway de aplicação com descarga de SSL

O exemplo seguinte cria um gateway de aplicação com descarga de SSL. O certificado e a palavra-passe do certificado tem de ser atualizados para uma chave privada válida.

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>Obter um nome de DNS do gateway de aplicação

Depois de criado o gateway, o próximo passo é configurar o front-end para comunicação.  Gateway de aplicação requer um nome DNS dinamicamente atribuído ao utilizar um IP público, que não é amigável. Para garantir que os utilizadores finais podem aceder o gateway de aplicação, pode utilizar um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado para no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um alias, obter detalhes sobre o gateway de aplicação e a respetiva utilização de nome IP/DNS associado a **PublicIPAddress** elemento ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME, que aponta de aplicações dois web para este nome DNS. Não recomendamos a utilização de registos de um de como pode alterar o VIP no reiniciar do gateway de aplicação.


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

## <a name="next-steps"></a>Passos seguintes

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, consulte [criar um gateway de aplicação com um balanceador de carga interno](application-gateway-ilb.md).

Para obter mais informações sobre as opções de balanceamento em geral de carga, consulte:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
