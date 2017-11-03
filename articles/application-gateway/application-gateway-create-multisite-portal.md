---
title: "Alojar vários sites com o Gateway de aplicação do Azure | Microsoft Docs"
description: "Esta página fornece instruções para configurar um gateway de aplicação do Azure existente para alojar várias aplicações web no mesmo gateway com o portal do Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 28a7fcb3e08a9c4b6a27e9fbc8d3ebae309adc62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurar um gateway de aplicação existente para alojar várias aplicações web

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

Alojar vários do site permite-lhe implementar mais do que uma aplicação web no mesmo gateway de aplicação. Baseia-se na presença de cabeçalho de anfitrião no pedido HTTP recebido, para determinar qual escuta seria receber tráfego. O serviço de escuta, em seguida, direciona o tráfego para o conjunto back-end apropriado, conforme configurado na definição de regras do gateway. Nas aplicações da web SSL ativado, o gateway de aplicação baseia-se a extensão de indicação de nome de servidor (SNI) para escolher o serviço de escuta correto para o tráfego web. É uma utilização comum para vários sites de alojamento para a carga de pedidos de saldo para domínios web diferente para conjuntos de diferentes servidores de back-end. Da mesma forma vários subdomínios do mesmo domínio de raiz também podem ser alojados num mesmo gateway de aplicação.

## <a name="scenario"></a>Cenário

No exemplo seguinte, gateway de aplicação está a servir o tráfego para contoso.com e fabrikam.com com dois conjuntos de servidores de back-end: contoso agrupamento de servidores e agrupamento de servidores da fabrikam. Configuração semelhante poderia ser utilizado para subdomínios de anfitrião, como app.contoso.com e blog.contoso.com.

![cenário de múltiplos sites][multisite]

## <a name="before-you-begin"></a>Antes de começar

Este cenário adiciona suporte de vários site para um gateway de aplicação existente. Para concluir este cenário, um gateway de aplicação existente tem de estar disponível para configurar. Visite [criar um gateway de aplicação utilizando o portal](application-gateway-create-gateway-portal.md) para saber como criar um gateway de aplicação básico no portal.

Seguem-se os passos necessários para atualizar o gateway de aplicação:

1. Crie conjuntos de back-end a utilizar para cada site.
2. Criar um serviço de escuta para cada site suporta o gateway de aplicação.
3. Crie regras para mapear cada serviço de escuta com o back-end adequado.

## <a name="requirements"></a>Requisitos

* **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ter um IP/VIP público. Também pode ser utilizado o FQDN.
* **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** o serviço de escuta tem uma porta de front-end, um protocolo (Http ou Https; estes valores são sensíveis às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL). Para gateways de aplicação ativada multilocal, nome de anfitrião e os indicadores SNI também são adicionados.
* **Regra:** a regra vincula o serviço de escuta, o agrupamento de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. As regras são processadas pela ordem que estão listados e será direcionado tráfego através da primeira regra que corresponde ao independentemente especificidade. Por exemplo, se tiver uma regra com um serviço de escuta básico e uma regra com uma escuta de vários site ambas na mesma porta, a regra com o serviço de escuta de vários site têm de estar listada antes da regra com o serviço de escuta básico por ordem para a regra de vários site a funcionar conforme esperado. 
* **Certificados:** cada serviço de escuta requer um certificado exclusivo, neste exemplo 2 escuta é criada para vários sites. Dois certificados. pfx e as palavras-passe para os mesmos têm de ser criado.

## <a name="create-back-end-pools-for-each-site"></a>Criar conjuntos de back-end para cada site

Um back-end do agrupamento de cada site que é necessário o suporte de gateway de aplicação, neste caso 2 são ser criado, um para contoso11.com e outro para fabrikam11.com.

### <a name="step-1"></a>Passo 1

Navegue para um gateway de aplicação existente no portal do Azure (https://portal.azure.com). Selecione **conjuntos back-end** e clique em **adicionar**

![Adicionar conjuntos de back-end][7]

### <a name="step-2"></a>Passo 2

Preencha as informações para o conjunto de back-end **pool1**, adicionar os endereços ip ou FQDN para servidores de back-end e clique em **OK**

![definições de pool1 do conjunto de back-end][8]

### <a name="step-3"></a>Passo 3

No painel de conjuntos de back-end clique **adicionar** para adicionar um conjunto de back-end adicional **pool2**, adicionar os endereços ip ou FQDN para servidores de back-end e clique em **OK**

![definições de pool2 do conjunto de back-end][9]

## <a name="create-listeners-for-each-back-end"></a>Criar serviços de escuta para cada back-end

O Gateway de Aplicação conta com os cabeçalhos de anfitrião HTTP 1.1 para alojar mais do que um site no mesmo endereço IP público e porta. O serviço de escuta básico criado no portal de não contém esta propriedade.

### <a name="step-1"></a>Passo 1

Clique em **os serviços de escuta** no gateway de aplicação existente e clique em **multilocal** para adicionar o serviço de escuta primeiro.

![Painel de descrição geral de serviços de escuta][1]

### <a name="step-2"></a>Passo 2

Preencha as informações para o serviço de escuta. Neste exemplo SSL terminação está configurada, crie uma nova porta de front-end. Carregue o certificado. pfx a ser utilizada para a terminação de SSL. A única diferença neste painel em comparação comparada o painel do serviço de escuta básica padrão é o nome do anfitrião.

![Painel de propriedades do serviço de escuta][2]

### <a name="step-3"></a>Passo 3

Clique em **multilocal** e crie outro serviço de escuta conforme descrito no passo anterior para o site segundo. Certifique-se utilizar um certificado diferente para o serviço de escuta segundo. A única diferença neste painel em comparação comparada o painel do serviço de escuta básica padrão é o nome do anfitrião. Preencha as informações para o serviço de escuta e clique em **OK**.

![Painel de propriedades do serviço de escuta][3]

> [!NOTE]
> A criação de serviços de escuta no portal do Azure para o gateway de aplicação é uma tarefa de execução longa, poderá demorar algum tempo a criar os dois serviços de escuta neste cenário. Quando concluir a mostrar os serviços de escuta no portal, como mostrado na imagem seguinte:

![Descrição geral do serviço de escuta][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>Criar regras para mapear os serviços de escuta para conjuntos back-end

### <a name="step-1"></a>Passo 1

Navegue para um gateway de aplicação existente no portal do Azure (https://portal.azure.com). Selecione **regras** e escolha a regra predefinida existente **rule1** e clique em **editar**.

### <a name="step-2"></a>Passo 2

Preencha o painel de regras, como mostrado na imagem seguinte. Escolher o primeiro serviço de escuta e o primeiro conjunto e clicando em **guardar** quando concluir.

![Editar a regra existente][6]

### <a name="step-3"></a>Passo 3

Clique em **regra básica** para criar a segunda regra. Preencha o formulário com o segundo conjunto de back-end do serviço de escuta e o segundo e clique em **OK** para guardar.

![regra básica painel Adicionar][10]

Este cenário conclui a configurar um gateway de aplicação existente com o suporte de vários site através do portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Saiba como proteger os seus Web sites com [Gateway de aplicação - Firewall de aplicações Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png
