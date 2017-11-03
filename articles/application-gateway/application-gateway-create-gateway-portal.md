---
title: "Criar um Gateway de aplicação - Portal do Azure | Microsoft Docs"
description: "Saiba como criar um Gateway de aplicação utilizando o portal"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 17d09ce98c40717d1db0927f791a7c97ea7835e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-the-portal"></a>Criar um gateway de aplicação com o portal

[Gateway de aplicação](application-gateway-introduction.md) é uma aplicação virtual dedicada, fornecendo o controlador de entrega de aplicações (ADC) como um oferta de serviço, as capacidades para a sua aplicação de balanceamento de carga de camada 7 vários. Este artigo orienta-os passos para criar um Gateway de aplicação no portal do Azure e a adição de um servidor existente como um membro de back-end.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [http://portal.azure.com](http://portal.azure.com)

## <a name="create-application-gateway"></a>Criar gateway de aplicação

Para criar um gateway de aplicação, conclua os passos que se seguem. Gateway de aplicação requer a sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que deixe suficiente espaço de endereço para tem várias sub-redes. Depois do gateway de aplicação for implementado para uma sub-rede, outros gateways de aplicação podem ser adicionados ao mesmo.

1. No painel de Favoritos do portal, clique em **novo**
1. No painel **Novo**, clique em **Redes**. No **redes** painel, clique em **Gateway de aplicação**, conforme mostrado na imagem seguinte:

    ![Criar Gateway de aplicação][1]

1. No **Noções básicas** painel apresentado, introduza os seguintes valores, em seguida, clique em **OK**:

   | **Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|AdatumAppGateway|O nome do gateway de aplicação|
   |**Camada**|Standard|Os valores disponíveis são padrão e WAF. Visite [firewall de aplicações web](application-gateway-web-application-firewall-overview.md) para saber mais sobre WAF.|
   |**Tamanho do SKU**|Médio|Opções de quando escolher o escalão padrão são pequeno, médio e grande. Quando escolher o escalão de WAF, as opções médio e grande apenas estão.|
   |**Contagem de instâncias**|2|Número de instâncias de gateway de aplicação para elevada disponibilidade. Contagens de instâncias de 1 só devem ser utilizadas para fins de teste.|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar o gateway de aplicação.|
   |**Grupo de recursos**|**Criar um novo:** AdatumAppGatewayRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Localização**|EUA Oeste||

1. No **definições** painel que é apresentado em **rede Virtual**, clique em **escolha uma rede virtual**. O **escolha de rede virtual** abre o painel.  Clique em **criar nova** para abrir o **criar rede virtual** painel.

   ![Escolha uma rede virtual][2]

1. No **painel de rede virtual criar** introduza os seguintes valores, em seguida, clique em **OK**. O **criar rede virtual** e **escolha de rede virtual** fechar os painéis. Este passo preenche a **sub-rede** campo no **definições** painel com a sub-rede escolhida.

   | **Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|Nome do gateway de aplicação|
   |**Espaço de endereços**|10.0.0.0/16|Este é o espaço de endereços da rede virtual|
   |**Nome da sub-rede**|AppGatewaySubnet|Nome da sub-rede para o gateway de aplicação|
   |**Intervalo de endereços da sub-rede**|10.0.0.0/28|Esta sub-rede permite mais sub-redes adicionais na rede virtual para os membros do conjunto de back-end|

1. No **definições** painel em **configuração de IP de front-end**, escolha **pública** como o **tipo de endereço IP**

1. No **definições** painel em **endereço IP público** clique **escolher um endereço IP público**, a **escolher endereço IP público** abre painel, clique em **criar nova**.

   ![Escolha o ip público][3]

1. No **Criar endereço IP público** painel, aceite o valor predefinido e clique em **OK**. O painel fecha e preenche o **endereço IP público** com o endereço IP público escolhido.

1. No **definições** painel em **configuração do serviço de escuta**, clique em **HTTP** em **protocolo**. Introduza a porta a utilizar o **porta** campo.

2. Clique em **OK** no **definições** painel para continuar.

1. Reveja as definições no **resumo** painel e clique em **OK** para iniciar a criação do gateway de aplicação. Criar um gateway de aplicação é uma tarefa de longa e demora tempo a concluir.

## <a name="add-servers-to-backend-pools"></a>Adicionar servidores ao conjuntos back-end

Depois de criar o gateway de aplicação, os sistemas que alojam a aplicação ser com balanceamento de carga ainda tem de ser adicionado ao gateway de aplicação. Os endereços IP, FQDN ou NICs esses servidores são adicionados aos conjuntos de endereços de back-end.

### <a name="ip-address-or-fqdn"></a>Endereço IP ou FQDN

1. Com o gateway de aplicação criado, no portal do Azure **Favoritos** painel, clique em **todos os recursos**. Clique em de **AdatumAppGateway** gateway de aplicação no painel de todos os recursos. Se a subscrição que selecionou já tem vários recursos na mesma, pode introduzir **AdatumAppGateway** no **filtrar por nome...** para aceder facilmente ao gateway de aplicação.

1. O gateway de aplicação que criou é apresentado. Clique em **conjuntos back-end**e selecione o conjunto de back-end atual **appGatewayBackendPool**, a **appGatewayBackendPool** abre o painel.

   ![Conjuntos de back-end do Gateway de aplicação][4]

1. Clique em **adicionar destino** para adicionar endereços IP dos valores FQDN. Escolha **IP ou FQDN do endereço** como o **tipo** e introduza o endereço IP ou FQDN do campo. Repita este passo para os membros do conjunto de back-end adicionais. Quando terminar clique **guardar**.

### <a name="virtual-machine-and-nic"></a>Máquina virtual e NIC

Também pode adicionar NICs de Máquina Virtual como membros do conjunto de back-end. Apenas as máquinas virtuais na mesma rede virtual que o Gateway de aplicação estão disponíveis através da lista pendente.

1. Com o gateway de aplicação criado, no portal do Azure **Favoritos** painel, clique em **todos os recursos**. Clique em de **AdatumAppGateway** gateway de aplicação no painel de todos os recursos. Se a subscrição que selecionou já tem vários recursos na mesma, pode introduzir **AdatumAppGateway** no **filtrar por nome...** para aceder facilmente ao gateway de aplicação.

1. O gateway de aplicação que criou é apresentado. Clique em **conjuntos back-end**e selecione o conjunto de back-end atual **appGatewayBackendPool**, a **appGatewayBackendPool** abre o painel.

   ![Conjuntos de back-end do Gateway de aplicação][5]

1. Clique em **adicionar destino** para adicionar endereços IP dos valores FQDN. Escolha **Máquina Virtual** como o **tipo** e selecione a máquina virtual e a NIC para utilizar. Quando terminar clique **guardar**

   > [!NOTE]
   > Apenas as máquinas virtuais na mesma rede virtual que o gateway de aplicação estão disponíveis na caixa pendente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, elimine o grupo de recursos, o gateway de aplicação e todos os recursos relacionados. Para tal, selecione o grupo de recursos a partir do painel de gateway de aplicação e clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste cenário, implementou um gateway de aplicação e adicionar um servidor para o back-end. São os passos seguintes configurar o gateway de aplicação por definições de modificação e ajustar regras no gateway. Estes passos podem ser encontrados, visitando os artigos seguintes:

Saiba como criar sondas de estado de funcionamento personalizado, visitando [criar uma sonda do Estado de funcionamento personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar a descarga de SSL e tomar a desencriptação de SSL dispendiosa desativar os servidores web, visitando [configurar a descarga de SSL](application-gateway-ssl-portal.md)

Saiba como proteger as suas aplicações com [Firewall de aplicações Web](application-gateway-webapplicationfirewall-overview.md) uma funcionalidade de gateway de aplicação.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
