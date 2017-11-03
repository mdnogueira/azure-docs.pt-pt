---
title: "Criar ou atualizar um gateway de aplicação com uma firewall de aplicações web | Microsoft Docs"
description: "Saiba como criar um gateway de aplicação com uma firewall de aplicação web através do portal"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>Criar um gateway de aplicação com uma firewall de aplicação web através do portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI do Azure](application-gateway-web-application-firewall-cli.md)

Saiba como criar uma firewall de aplicação web (WAF)-ativado o gateway de aplicação.

WAF no Gateway de aplicação do Azure protege as aplicações web de ataques baseados na web comuns, como a injeção de SQL, ataques de scripts entre sites e hijacks de sessão. Uma WAF protege contra muitas das OWASP superiores 10 comuns web vulnerabilidades.

## <a name="scenarios"></a>Cenários

Este artigo apresenta dois cenários. No primeiro cenário, saiba como [criar um gateway de aplicação com uma WAF](#create-an-application-gateway-with-web-application-firewall). O segundo cenário, a saber como [adicionar uma WAF para um gateway de aplicação existente](#add-web-application-firewall-to-an-existing-application-gateway).

![Exemplo de cenário][scenario]

> [!NOTE]
> Pode adicionar sondas de estado de funcionamento personalizado, endereços de conjunto back-end e regras adicionais para o gateway de aplicação. Estas aplicações estão configuradas depois do gateway de aplicação está configurado e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

 Um gateway de aplicação requer a sua própria sub-rede. Quando cria uma rede virtual, certifique-se de que deixe suficiente espaço de endereço para tem várias sub-redes. Depois de implementar um gateway de aplicação a uma sub-rede, gateways de aplicação apenas adicionais podem ser adicionados à sub-rede.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar uma firewall de aplicação web para um gateway de aplicação existente

Neste exemplo de atualizações de um gateway de aplicação existente para suportar uma WAF no **prevenção** modo.

1. No portal do Azure **Favoritos** painel, selecione **todos os recursos**. No **todos os recursos** painel, selecione o gateway de aplicação existente. Se a subscrição que selecionou já tem vários recursos na mesma, introduza o nome no **filtrar por nome** caixa para facilmente o acesso a zona DNS.

   ![Seleção de gateway de aplicação existente][1]

2. Selecione **firewall de aplicações Web**e atualizar as definições do gateway de aplicação. Quando a atualização estiver concluída, selecione **guardar**. 

3. Utilize as seguintes definições para atualizar um gateway de aplicação existente para suportar uma WAF:

   | **Definição** | **Valor** | **Detalhes**
   |---|---|---|
   |**Atualizar para o escalão de WAF**| Assinalado | Esta opção define a camada do gateway de aplicação para a camada de WAF.|
   |**Estado da firewall**| Ativado | Esta definição permite que a firewall no WAF.|
   |**Modo de firewall** | Prevenção | Esta definição é a forma como uma WAF lida com tráfego malicioso. **Deteção** modo apenas os registos de eventos. **Prevenção** modo os registos de eventos e para o tráfego malicioso.|
   |**Conjunto de regras**|3.0|Esta definição determina a [principal conjunto de regras](application-gateway-web-application-firewall-overview.md#core-rule-sets) que é utilizado para proteger os membros do conjunto back-end.|
   |**Configurar regras desativadas**|varia|Para evitar possíveis falsos positivos, pode utilizar esta definição para desativar certos [regras e grupos de regra](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Quando atualizar um gateway de aplicação existente para o SKU de WAF, o tamanho do SKU muda para **média**. Após a conclusão da configuração, pode reconfigurar a esta definição.

    ![Definições básicas][2-1]

    > [!NOTE]
    > Para ver registos WAF, ative os diagnósticos e selecione **ApplicationGatewayFirewallLog**. Escolha uma contagem de instâncias de **1** apenas para testes fins. Não se recomenda uma contagem de instâncias em **2** porque não está abrangida o SLA. Gateways pequenos não estão disponíveis quando utiliza uma WAF.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Criar um gateway de aplicação com uma firewall de aplicação web

Neste cenário irão:

* Crie um gateway de aplicação WAF médio com duas instâncias.
* Crie uma rede virtual denominada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
* Criar uma sub-rede denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como bloco CIDR.
* Configure um certificado para a descarga de SSL.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter um [um mês avaliação gratuita](https://azure.microsoft.com/free).

2. No **Favoritos** painel no portal, selecione **novo**.

3. No **novo** painel, selecione **redes**. No **redes** painel, selecione **Gateway de aplicação**, conforme mostrado na imagem seguinte:

    ![Criação do gateway de aplicação][1]

4. No **Noções básicas** painel que aparece, introduza os valores seguintes e, em seguida, selecione **OK**:

   | **Definição** | **Valor** | **Detalhes**
   |---|---|---|
   |**Nome**|AdatumAppGateway|O nome do gateway de aplicação.|
   |**Camada**|WAF|Os valores disponíveis são padrão e WAF. Para obter mais informações sobre uma WAF, consulte o artigo [firewall de aplicações Web](application-gateway-web-application-firewall-overview.md).|
   |**Tamanho do SKU**|Médio|As opções de escalão Standard **pequeno**, **média**, e **grande**. As opções de camada de WAF **média** e **grande** apenas.|
   |**Contagem de instâncias**|2|O número de instâncias de gateway de aplicação para elevada disponibilidade. Utilize as contagens de instâncias de 1 para fins de teste.|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição a utilizar para criar o gateway de aplicação.|
   |**Grupo de recursos**|**Criar um novo:** AdatumAppGatewayRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Localização**|EUA Oeste||

   ![Configuração de definições básicas][2-2]

5. No **definições** painel que é apresentado em **rede Virtual**, selecione **escolha uma rede virtual**. No **escolha de rede virtual** painel, selecione **criar nova**.

   ![Opção de rede virtual][2]

6. No **painel de rede virtual criar**, introduza os valores seguintes e, em seguida, selecione **OK**. O **sub-rede** campo no **definições** painel é preenchido com a sub-rede que escolheu.

   |**Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|O nome do gateway de aplicação.|
   |**Espaço de endereços**|10.0.0.0/16| Este valor é o espaço de endereços da rede virtual.|
   |**Nome da sub-rede**|AppGatewaySubnet|O nome da sub-rede para o gateway de aplicação.|
   |**Intervalo de endereços da sub-rede**|10.0.0.0/28 | Esta sub-rede permite mais sub-redes adicionais na rede virtual para os membros do conjunto de back-end.|

7. No **definições** painel em **configuração de IP de front-end**, selecione **pública** como o **tipo de endereço IP**.

8. No **definições** painel em **endereço IP público**, selecione **escolher um endereço IP público**. No **escolher endereço IP público** painel, selecione **criar nova**.

   ![Opção de endereço IP pública][3]

9. No **Criar endereço IP público** painel, aceite o valor predefinido e selecione **OK**. O **endereço IP público** campo é preenchido com o endereço IP público que escolheu.

10. No **definições** painel em **configuração do serviço de escuta**, selecione **HTTP** em **protocolo**. É necessário um certificado para utilizar **HTTPS**. É necessária a chave privada do certificado. Forneça uma exportação. pfx do certificado e introduza a palavra-passe para o ficheiro.

11. Configurar definições específicas para o **WAF**.

   |**Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Estado da firewall**| Ativado| Esta definição desativa a WAF ou desativar.|
   |**Modo de firewall** | Prevenção| Esta definição determina as ações que a WAF demora no tráfego malicioso. **Deteção** modo apenas os registos de tráfego. **Prevenção** modo regista e deixa de tráfego com uma resposta não autorizado 403.|


12. Reveja o **resumo** página e selecione **OK**. Agora o gateway de aplicação é colocado em fila cópias de segurança e criado.

13. Após a aplicação é criado o gateway, aceda ao mesmo no portal para continuar a configuração do gateway de aplicação.

    ![Vista de recurso do gateway de aplicação][10]

Estes passos criar um gateway de aplicação básico com as predefinições para o serviço de escuta, conjunto back-end, as definições HTTP de back-end e regras. Após a conclusão do aprovisionamento com êxito, pode modificar estas definições de acordo com a sua implementação.

> [!NOTE]
> Gateways de aplicação criados com a configuração básica do WAF estão configurados com CR 3.0 para proteção.

## <a name="next-steps"></a>Passos seguintes

Para configurar um alias de domínio personalizado para o [endereço IP público](../dns/dns-custom-domain.md#public-ip-address), pode utilizar o DNS do Azure ou de outro fornecedor DNS.

Para configurar o registo de diagnóstico para registar os eventos que são detetados ou impedidos com WAF, consulte o artigo [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md).

Para criar sondas de estado de funcionamento personalizado, consulte [criar uma sonda do Estado de funcionamento personalizado](application-gateway-create-probe-portal.md).

Para configurar a descarga de SSL e tomar a subscrição de SSL dispendiosa desativar os servidores web, consulte [descarga de SSL configurar](application-gateway-ssl-portal.md).

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
