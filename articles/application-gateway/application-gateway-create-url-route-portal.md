---
title: "Criar uma regra com base no caminho para um gateway de aplicação - portal do Azure | Microsoft Docs"
description: "Saiba como criar uma regra com base no caminho para um gateway de aplicação utilizando o portal do Azure."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Criar uma regra com base no caminho para um gateway de aplicação utilizando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [CLI 2.0 do Azure](application-gateway-create-url-route-cli.md)

Com o URL com base no caminho encaminhamento, associa as rotas com base no caminho de URL de pedidos HTTP. Verifica se existe uma rota para um conjunto de servidores de back-end configurado para o URL listado no gateway de aplicação e, em seguida, envia o tráfego de rede para o conjunto definido. Uma utilização comum para o URL com base no caminho de encaminhamento está a carga de pedidos de saldo para diferentes tipos de conteúdo em agrupamentos de diferentes servidores de back-end.

Gateways de aplicação têm dois tipos de regra: básico e as regras com base no caminho de URL. O tipo de regra básica fornece um serviço round robin para os conjuntos de back-end. As regras com base no caminho, para além de distribuição de round robin, também utilizam o padrão do caminho do URL do pedido quando escolher o conjunto de back-end adequado.

## <a name="scenario"></a>Cenário

O cenário seguinte cria uma regra de caminho num gateway de aplicação existente.
Este cenário pressupõe que já tiver seguido os passos em [criar um gateway de aplicação com o portal](application-gateway-create-gateway-portal.md).

![Rota de URL][scenario]

## <a name="createrule"></a>Criar a regra de caminho

Uma regra com base no caminho requer o seu próprio serviço de escuta. Antes de criar a regra, lembre-se de que verifique se tem um serviço de escuta disponível para utilizar.

### <a name="step-1"></a>Passo 1

Vá para o [portal do Azure](http://portal.azure.com) e selecione um gateway de aplicação existente. Clique em **regras**.

![Descrição geral do Gateway de Aplicação][1]

### <a name="step-2"></a>Passo 2

Clique em de **com base no caminho** botão para adicionar uma nova regra de acesso com base no caminho.

### <a name="step-3"></a>Passo 3

O **Adicionar regra de caminho com base em** painel tem duas secções. A primeira secção é onde o serviço de escuta, o nome da regra de, as predefinições de caminho que definiu. As predefinições de caminho são para as rotas não abrangidos por rota personalizada com base no caminho. A segunda secção do **Adicionar regra de caminho com base em** painel é onde é possível definir as regras com base no caminho próprios.

**Definições básicas**

* **Nome**: um nome amigável para a regra que está acessível no portal.
* **Serviço de escuta**: O serviço de escuta que é utilizado para a regra.
* **Predefinição conjunto back-end**: O back-end a utilizar para a regra predefinida.
* **Predefinições de HTTP**: as definições de HTTP a ser utilizado para a regra predefinida.

**Definições das regras com base no caminho**

* **Nome**: um nome amigável para a regra de caminho.
* **Caminhos**: O caminho a regra procura quando reencaminham o tráfego.
* **Conjunto back-end**: O back-end para ser utilizado para a regra.
* **Definição de HTTP**: definições de HTTP à ser utilizado para a regra.

> [!IMPORTANT]
> O **caminhos** definição se a lista de padrões de caminho para corresponder. Cada padrão tem de começar com uma barra e um asterisco só é permitido no fim. Exemplos válidos: /xyz, /xyz*e /xyz/*.  

![Adicionar um painel com base no caminho da regra com informações preenchidas][2]

Adicionar uma regra com base no caminho para um gateway de aplicação existente é um processo fácil através do portal do Azure. Depois de criar uma regra com base no caminho, pode editá-la para incluir regras adicionais. 

![Adicione regras adicionais com base no caminho][3]

Este passo configura uma rota com base no caminho. É importante compreender a pedidos não foi reescritos. Tal como pedidos são fornecidos, o gateway de aplicação inspeciona o pedido e, com base num padrão de URL, envia o pedido para o conjunto de back-end adequado.

## <a name="next-steps"></a>Passos seguintes

Para saber como configurar a descarga de SSL com o Gateway de aplicação do Azure, consulte [configurar um gateway de aplicação para a descarga de SSL com o portal do Azure](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
