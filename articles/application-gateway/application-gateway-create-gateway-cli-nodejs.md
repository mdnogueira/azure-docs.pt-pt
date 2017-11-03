---
title: "Criar um Gateway de aplicação do Azure - CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como criar um Gateway de aplicação utilizando a CLI do Azure 1.0 no Gestor de recursos"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 5106b0a480bb853ad75ad1aaca896f7a72fc4f71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Criar um gateway de aplicação utilizando a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure 1.0](application-gateway-create-gateway-cli.md)
> * [CLI 2.0 do Azure](application-gateway-create-gateway-cli.md)
> 
> 

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local. Aplicação gateway tem as seguintes funcionalidades de entrega de aplicações: carregar sondas de estado de funcionamento personalizado de balanceamento, afinidade de sessão com base em cookies e descarga de Secure Sockets Layer (SSL), HTTP e suporte para vários sites.

## <a name="prerequisite-install-the-azure-cli"></a>Pré-requisito: Instalar a CLI do Azure

Para efetuar os passos neste artigo, terá de [instalar a Interface de linha de comandos do Azure para Mac, Linux e Windows (CLI do Azure)](../xplat-cli-install.md) e terá [inicie sessão no Azure](../xplat-cli-connect.md). 

> [!NOTE]
> Se não tiver uma conta do Azure, é necessário um. Subscreva [uma avaliação gratuita aqui](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Neste cenário, irá aprender a criar um gateway de aplicação no portal do Azure.

Neste cenário irão:

* Crie um gateway de aplicação médias com duas instâncias.
* Crie uma rede virtual denominada ContosoVNET com um bloco CIDR reservado de 10.0.0.0/16.
* Crie uma sub-rede denominada subnet01 que utiliza 10.0.0.0/28 como bloco CIDR.

> [!NOTE]
> Pesquisas de configuração adicionais do gateway de aplicação, incluindo o estado de funcionamento personalizado, o conjunto de endereços de back-end e regras adicionais são configuradas depois do gateway de aplicação está configurado e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

Gateway de aplicação do Azure requer a sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que deixe suficiente espaço de endereço para tem várias sub-redes. Depois de implementar um gateway de aplicação a uma sub-rede, gateways de aplicação apenas adicionais são capazes de ser adicionado à sub-rede.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Abra o **linha de comandos do Microsoft Azure**e inicie sessão. 

```azurecli-interactive
azure login
```

Depois de escrever o exemplo anterior, é fornecido um código. Navegue para https://aka.ms/devicelogin num browser para continuar o processo de início de sessão.

![o início de sessão do cmd que mostra dispositivos][1]

No browser, introduza o código que recebeu. Será redirecionado para uma página de início de sessão.

![browser para introduzir o código][2]

Assim que foi introduzido o código tem sessão iniciada, feche o browser continue com o cenário.

![sessão iniciada com êxito][3]

## <a name="switch-to-resource-manager-mode"></a>Mudar para modo Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway de aplicação, um grupo de recursos é criado para conter o gateway de aplicação. O seguinte mostra o comando.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Assim que o grupo de recursos é criado, uma rede virtual é criada para o gateway de aplicação.  No exemplo seguinte, o espaço de endereços foi como 10.0.0.0/16 conforme definido nas notas de cenário anterior.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Criar uma sub-rede

Depois de criar a rede virtual, é adicionada uma sub-rede para o gateway de aplicação.  Se planeia utilizar o gateway de aplicação com uma aplicação web alojada na mesma rede virtual que o gateway de aplicação, lembre-se de que deixe tenha espaço suficiente para outra sub-rede.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Assim que a rede virtual e sub-rede são criados, as pré-requisitos para o gateway de aplicação estão concluídos. Além de um certificado. pfx exportado anteriormente e a palavra-passe para o certificado são necessários para o passo seguinte: endereços IP utilizados para o back-end são os endereços IP para o servidor de back-end. Estes valores podem ser IPs privados numa rede virtual, os ips públicos ou nomes de domínio totalmente qualificados para os servidores de back-end.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Para obter uma lista de parâmetros que podem ser fornecidos durante a criação, execute o seguinte comando: **criar gateway de aplicação de rede do azure – ajudar**.

Este exemplo cria um gateway de aplicação básico com as predefinições para o serviço de escuta, conjunto back-end, as definições http de back-end e regras. Pode modificar estas definições de acordo com a implementação, após o aprovisionamento é efetuada com êxito.
Se já tiver a sua aplicação web definida com o conjunto de back-end nos passos anteriores, uma vez criado, o balanceamento de carga é iniciada.

## <a name="next-steps"></a>Passos seguintes

Saiba como criar sondas de estado de funcionamento personalizado, visitando [criar uma sonda do Estado de funcionamento personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar a descarga de SSL e tomar a desencriptação de SSL dispendiosa desativar os servidores web, visitando [configurar a descarga de SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
