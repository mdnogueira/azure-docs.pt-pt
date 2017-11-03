---
title: "Criar um gateway de aplicação utilizando regras de encaminhamento do URL - Azure CLI 2.0 | Microsoft Docs"
description: "Esta página fornece instruções sobre como criar e configurar um gateway de aplicação utilizando regras de encaminhamento de URL."
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
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Criar um gateway de aplicação utilizando a com base no caminho de encaminhamento com o Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [CLI 2.0 do Azure](application-gateway-create-url-route-cli.md)

Com o URL com base no caminho encaminhamento, associa as rotas com base no caminho de URL de pedidos HTTP. Verifica se existe uma rota para um conjunto de servidores de back-end configurado para o URL listado no gateway de aplicação e, em seguida, envia o tráfego de rede para o conjunto definido. Uma utilização comum para o URL com base no caminho de encaminhamento está a carga de pedidos de saldo para diferentes tipos de conteúdo em agrupamentos de diferentes servidores de back-end.

Gateway de aplicação do Azure utiliza dois tipos de regra: básico e as regras com base no caminho de URL. O tipo de regra básica fornece um serviço round robin para os conjuntos de back-end. Regras com base no caminho, para além de distribuição de round robin, utilizam também o padrão do caminho do URL do pedido para escolher o conjunto de back-end adequado.

## <a name="scenario"></a>Cenário

No exemplo seguinte, um gateway de aplicação serve tráfego para contoso.com com dois conjuntos de servidores de back-end: um conjunto predefinido de servidor e um conjunto de servidores de imagem.

Pedidos para http://contoso.com/image * são encaminhados para o agrupamento de servidores de imagem (**imagesBackendPool**). Se o padrão do caminho não corresponderem, o gateway de aplicação seleciona o conjunto predefinido de servidor (**appGatewayBackendPool**).

![Rota de URL](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o **linha de comandos do Microsoft Azure** e iniciar sessão:

```azurecli
az login -u "username"
```

> [!NOTE]
> Também pode utilizar `az login` sem que o comutador para início de sessão de dispositivo que necessita de introduzir um código no aka.ms/devicelogin.

Depois de introduzir o comando anterior, receberá um código. Aceda à https://aka.ms/devicelogin num browser para continuar o processo de início de sessão.

![o início de sessão do cmd que mostra dispositivos][1]

No browser, introduza o código que recebeu. Isto redireciona-o a uma página de início de sessão.

![browser para introduzir o código][2]

Introduza o código para iniciar sessão e, em seguida, feche o browser para continuar.

![sessão iniciada com êxito][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Adicionar uma regra com base no caminho de um gateway de aplicação existente

Os passos seguintes mostram como adicionar uma regra com base no caminho para um gateway de aplicação existente.
### <a name="create-a-new-back-end-pool"></a>Criar um novo conjunto de back-end

Configurar a definição de gateway de aplicação **imagesBackendPool** para o tráfego de rede com balanceamento de carga no conjunto de back-end. Neste exemplo, configure as definições de outro conjunto de back-end para o novo conjunto de back-end. Cada conjunto de back-end pode ter a sua própria definição. Regras de caminho utilizam definições de HTTP de back-end para encaminhar o tráfego para os membros do conjunto de back-end correto. Isto determina o protocolo e a porta que é utilizada quando o envio de tráfego para os membros do conjunto back-end. As definições de HTTP de back-end também determinam sessões com base em cookies.  Se estiver ativada, afinidade com base no cookie de sessão envia o tráfego para o mesmo back-end como anteriores pedidos para cada pacote.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Criar uma nova porta de front-end para um gateway de aplicação

O objeto de configuração de porta de front-end é utilizado por um serviço de escuta para definir a que o gateway de aplicação escuta tráfego pelo serviço de escuta de porta.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Criar um novo serviço de escuta

Este passo configura o serviço de escuta para o endereço IP público e a porta utilizados para receber o tráfego de rede de entrada. O exemplo seguinte executa a configuração de IP Front-end configurada anteriormente, a configuração de porta de front-end e um protocolo (http ou https, que são sensíveis a maiúsculas e minúsculas) e configura o serviço de escuta. Neste exemplo, o serviço de escuta escuta tráfego HTTP na porta 82 o endereço IP público criado anteriormente neste cenário.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Criar o mapa de caminho de URL

Este passo configura o caminho relativo de URL utilizado pelo gateway de aplicação para definir o mapeamento entre o caminho e o conjunto de back-end atribuído para processar o tráfego de entrada.

> [!IMPORTANT]
> Cada caminho tem de começar com "/", sendo o local só é permitido um asterisco no final. Exemplos válidos são /xyz, /xyz* ou/xyz / *. A cadeia que sejam fornecida ao matcher caminho não inclui qualquer texto após o primeiro "?" ou "#" e os carateres não são permitidas. 

O exemplo seguinte cria uma regra para as imagens / * caminho de encaminhamento de tráfego para o back-end **imagesBackendPool**. Esta regra assegura que o tráfego para cada conjunto de URLs é encaminhado para o back-end. Por exemplo, http://adatum.com/images/figure1.jpg vai para **imagesBackendPool**. Se o caminho não corresponde a qualquer uma das regras de caminho predefinido, a configuração de mapa de caminho de regra configura também um conjunto predefinido de endereços de back-end. Por exemplo, http://adatum.com/shoppingcart/test.html vai para **pool1** porque se encontra definida como o agrupamento predefinido para o tráfego sem correspondência.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Passos seguintes

Se pretender saber mais sobre a descarga de Secure Sockets Layer (SSL), consulte [configurar um gateway de aplicação para a descarga SSL de](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png
