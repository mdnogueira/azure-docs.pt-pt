---
title: "Configurar uma firewall de aplicação web: Gateway de aplicação do Azure | Microsoft Docs"
description: "Este artigo fornece orientação sobre como começar a utilizar uma firewall de aplicação web num gateway de aplicação nova ou existente."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.openlocfilehash: c9c740a3a1a28a1a9a4f2abf579fe2adb54e4f47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Configurar uma firewall de aplicação web num gateway de aplicação nova ou existente com a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI do Azure](application-gateway-web-application-firewall-cli.md)

Saiba como criar uma firewall de aplicação web (WAF)-ativado o gateway de aplicação. Além disso, saiba como adicionar uma WAF a um gateway de aplicação existente.

WAF no Gateway de aplicação do Azure protege as aplicações web de ataques baseados na web comuns, como a injeção de SQL, ataques de scripts entre sites e hijacks de sessão.

 Gateway de aplicação é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diferentes servidores, se estiverem na nuvem ou no local. Gateway de aplicação oferece muitas funcionalidades de controlador (ADC) de entrega de aplicações:

 * Balanceamento de carga HTTP 
 * Afinidade com base no cookie de sessão 
 * Proteger a descarga de Sockets Layer (SSL) 
 * Sondas de estado de funcionamento personalizado 
 * Suporte para a funcionalidade de múltiplos sites
 
 Para obter uma lista completa das funcionalidades suportadas, consulte [descrição geral do Gateway de aplicação](application-gateway-introduction.md).

Este artigo mostra como [adicionar uma firewall de aplicação web para um gateway de aplicação existente](#add-web-application-firewall-to-an-existing-application-gateway). Também mostra como [criar um gateway de aplicação que utiliza uma firewall de aplicação web](#create-an-application-gateway-with-web-application-firewall).

![imagem do cenário][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Pré-requisito: Instalar a CLI do Azure 2.0

Para efetuar os passos neste artigo, terá de [instalar a interface de linha de comandos (CLI do Azure) do Azure para Mac, Linux e Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Diferenças de configuração WAF

Se já leu [criar um gateway de aplicação com a CLI do Azure](application-gateway-create-gateway-cli.md), compreender as definições de SKU para configurar quando criar um gateway de aplicação. O WAF fornece definições adicionais para definir quando configurar o SKU num gateway de aplicação. Não foram efetuadas alterações adicionais que efetuar no gateway de aplicação em si.

| **Definição** | **Detalhes**
|---|---|
|**SKU** |Um gateway de aplicação normal sem uma WAF suporta **padrão\_pequeno**, **padrão\_média**, e **padrão\_grande**tamanhos. Com a introdução de uma WAF, existem dois SKUs adicionais, **WAF\_média** e **WAF\_grande**. Uma WAF não é suportada nos gateways de aplicação pequeno.|
|**Modo** | Esta definição é o modo do WAF. valores permitidos são **deteção** e **prevenção**. Quando o WAF está configurado **deteção** modo, todas as ameaças são armazenadas num ficheiro de registo. No **prevenção** modo, os eventos ainda estão registados, mas o atacante recebe 403 não autorizado resposta do gateway de aplicação.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar uma firewall de aplicação web para um gateway de aplicação existente

O comando seguinte altera um gateway de aplicação padrão existente para um gateway de aplicação com capacidade WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Este comando atualiza o gateway de aplicação com uma WAF. Para compreender como ver os registos para o gateway de aplicação, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md). Devido à natureza segurança de uma WAF, consulte os registos regularmente para compreender a postura de segurança das suas aplicações web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Criar um gateway de aplicação com uma firewall de aplicação web

O comando seguinte cria um gateway de aplicação com uma WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Gateways de aplicação criados com a configuração básica do WAF estão configurados com CR 3.0 para proteção.

## <a name="get-an-application-gateway-dns-name"></a>Obter um nome de DNS do gateway de aplicação

Depois de criado o gateway, o próximo passo é configurar o front-end para comunicação. Quando utilizar um IP público, o gateway de aplicação requer um nome DNS dinamicamente atribuído, o que não é amigável. Para garantir que os utilizadores podem atingir o gateway de aplicação, utilize um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado para um serviço em nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um registo CNAME, obter detalhes sobre o gateway de aplicação e o seu nome IP/DNS associado utilizando o elemento de PublicIPAddress ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME, que aponta de aplicações dois web para este nome DNS. Não recomendamos que utilize um registos, porque o VIP pode mudar quando reinicia o gateway de aplicação.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

Para saber como personalizar regras WAF, consulte [personalizar regras de firewall de aplicação web através de 2.0 do CLI do Azure](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
