---
title: "O que é o Gestor de tráfego | Microsoft Docs"
description: "Este artigo ajuda-o a compreender o que é o Gestor de tráfego e se é a escolha de encaminhamento de tráfego de direita para a sua aplicação"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-traffic-manager"></a>Descrição Geral do Gestor de Tráfego

Gestor de tráfego do Microsoft Azure permite-lhe controlar a distribuição de tráfego do utilizador para pontos finais do serviço em datacenters diferentes. Pontos finais de serviço suportados pelo Gestor de tráfego incluem as VMs do Azure, as aplicações Web e serviços em nuvem. Também pode utilizar o Gestor de Tráfego com pontos finais externos, não pertencentes ao Azure.

Gestor de tráfego utiliza o sistema de nomes de domínio (DNS) para direcionar os pedidos de cliente para o ponto de final mais adequado, com base no método de encaminhamento de tráfego e o estado de funcionamento dos pontos finais. Gestor de tráfego fornece uma variedade de [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) e [ponto final de opções de monitorização](traffic-manager-monitoring.md) de acordo com as necessidades de aplicações diferentes e modelos de ativação pós-falha automática. Gestor de tráfego seja resistente a falhas, incluindo a falha de uma região do Azure completa.

## <a name="traffic-manager-benefits"></a>Vantagens do Gestor de tráfego

Gestor de tráfego pode ajudá-lo:

* **Melhorar a disponibilidade de aplicações críticas**

    Gestor de tráfego fornece elevada disponibilidade para as suas aplicações através da monitorização de pontos finais da sua e fornecer ativação pós-falha automática quando um ponto final fica inativo.

* **Melhorar a capacidade de resposta para aplicações de elevado desempenho**

    Azure permite-lhe executar os Web sites ou serviços em nuvem nos centros de dados localizados em todo o mundo. Gestor de tráfego melhora a capacidade de resposta da aplicação ao instruir o tráfego para o ponto final com a menor latência de rede para o cliente.

* **Executar a manutenção do serviço sem períodos de indisponibilidade**

    Pode efetuar operações de manutenção planeada sobre as aplicações sem períodos de indisponibilidade. Gestor de tráfego direciona o tráfego para pontos finais alternativos durante a manutenção está em curso.

* **Combinar as aplicações baseadas na nuvem e no local**

    O Traffic Manager suporta externo, pontos finais do Azure não ativar a ser utilizada com híbrida na nuvem e no local implementações, incluindo a "rajada para a nuvem," "migrar para a nuvem," e "ativação pós-falha para a nuvem" cenários.

* **Distribuir o tráfego para implementações grandes, complexas**

    Utilizar [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md), métodos de encaminhamento de tráfego podem ser combinados para criar regras sofisticadas e flexíveis para suporte as necessidades de implementações maiores, mais complexas.

## <a name="how-traffic-manager-works"></a>Como funciona o Gestor de Tráfego

Traffic Manager do Azure permite-lhe controlar a distribuição de tráfego entre pontos finais da sua aplicação. Um ponto final é qualquer serviço de acesso à Internet alojada dentro ou fora do Azure.

Gestor de tráfego proporciona duas vantagens chave:

1. Distribuição de tráfego de acordo com uma das várias [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)
2. [Monitorização contínua de estado de funcionamento do ponto final](traffic-manager-monitoring.md) e ativação pós-falha automática quando não conseguem pontos finais

Quando um cliente tenta estabelecer ligação a um serviço,-tem primeiro de resolver o nome DNS do serviço para um endereço IP. O cliente liga-se, em seguida, a esse endereço IP para aceder ao serviço.

**O ponto mais importante para compreender é que o Gestor de tráfego funciona ao nível do DNS.**  Gestor de tráfego utiliza o DNS para direcionar clientes para pontos finais de serviço específicos com base nas regras do método de encaminhamento de tráfego. Os clientes ligam ao ponto final selecionado **diretamente**. Gestor de tráfego não é um proxy ou de um gateway. Gestor de tráfego não consegue ver o tráfego de passar entre o cliente e o serviço.

### <a name="traffic-manager-example"></a>Exemplo de Gestor de tráfego

Contoso Corp ter programado um novo portal de parceiros. O URL para este portal é https://partners.contoso.com/login.aspx. A aplicação fique alojada em três regiões do Azure. Para melhorar a disponibilidade e maximizar o desempenho global, que utilizar o Gestor de tráfego para distribuir o tráfego de cliente para o ponto de final disponível mais próximo.

Para alcançar esta configuração, concluírem os seguintes passos:

1. Implemente três instâncias do seu serviço. Os nomes DNS estas implementações são 'contoso us.cloudapp .net', 'contoso eu.cloudapp .net' e 'contoso asia.cloudapp .net'.
2. Criar um perfil do Traffic Manager, com o nome 'contoso.trafficmanager.net' e configure-a para utilizar o método de encaminhamento de tráfego de 'Desempenho' em três pontos finais.
* Configure os respetivos nomes de domínio intuitivos, "partners.contoso.com", para que apontem para 'contoso.trafficmanager.net', através de um registo CNAME no DNS.

![Configuração de DNS do Gestor de tráfego][1]

> [!NOTE]
> Ao utilizar um domínio do com o Gestor de tráfego do Azure, tem de utilizar um CNAME para apontar o nome de domínio para o nome de domínio do Traffic Manager. Normas DNS não permitem-lhe criar um CNAME no 'vértice' (ou raiz) de um domínio. Deste modo, não é possível criar um CNAME para "contoso.com" (por vezes, designado por um 'naked' domínio). Só pode criar um CNAME para o domínio em 'contoso.com', por exemplo, "www.contoso.com". Para contornar esta limitação, recomendamos que utilize um redirecionamento HTTP simple para pedidos diretos para 'contoso.com' para um nome alternativo, tais como "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes ligam utilizando o Gestor de tráfego

Continuar do exemplo anterior, quando um cliente solicita o https://partners.contoso.com/login.aspx página, o cliente efetua os seguintes passos para resolver o nome DNS e estabelecer uma ligação:

![Estabelecimento de ligação utilizando o Gestor de tráfego][2]

1. O cliente envia uma consulta DNS para o serviço DNS recursivo configurado para resolver o nome "partners.contoso.com". Um serviço DNS recursiva, por vezes denominado um serviço de 'local DNS', não aloja domínios DNS diretamente. Em vez disso, o cliente off-loads o trabalho de contactar os serviços de DNS autoritativos através da Internet necessária para resolver um nome DNS.
2. Para resolver o nome DNS, o serviço DNS recursiva localiza os servidores de nomes para o domínio "contoso.com". -Lo, em seguida, contacta os servidores de nomes para pedir o registo DNS "partners.contoso.com". Os servidores DNS de contoso.com devolvem o registo CNAME que aponta para contoso.trafficmanager.net.
3. Em seguida, o serviço DNS recursiva localiza os servidores de nomes para o domínio 'trafficmanager.net', que são fornecidas pelo serviço do Gestor de tráfego do Azure. Em seguida, envia um pedido para o registo DNS 'contoso.trafficmanager.net' para esses servidores DNS.
4. Os servidores de nomes do Gestor de tráfego receberem o pedido. Escolherem um ponto final com base em:

    - O estado de cada ponto final configurado (desativados pontos finais não são devolvidos)
    - Verifica o estado de funcionamento atual de cada ponto final, conforme determinado pelo Estado de funcionamento do Gestor de tráfego. Para obter mais informações, consulte [monitorização de ponto final do Gestor de tráfego](traffic-manager-monitoring.md).
    - O método de encaminhamento de tráfego que escolheu. Para obter mais informações, consulte [métodos de encaminhamento do Traffic Manager](traffic-manager-routing-methods.md).

5. O ponto final que escolheu é devolvido como outro registo CNAME no DNS. Neste caso, informe-nos suponha que contoso-us.cloudapp.net é devolvido.
6. Em seguida, o serviço DNS recursiva localiza os servidores de nomes para o domínio 'cloudapp.net'. -Contacta os servidores de nomes para pedir '.net contoso us.cloudapp' registo DNS. É devolvido um registo DNS 'A' que contém o endereço IP do baseado em E.U.A. ponto final do serviço.
7. O serviço DNS recursiva consolida os resultados e devolve uma resposta DNS único para o cliente.
8. O cliente recebe os resultados DNS e estabelece ligação com o endereço IP indicado. O cliente se liga ao ponto final de serviço de aplicações diretamente, não através do Gestor de tráfego. Uma vez que é um ponto final de HTTPS, o cliente efetua o handshake SSL/TLS necessário e, em seguida, faz um pedido de HTTP GET o ' / login.aspx' página.

O serviço DNS recursiva coloca em cache as respostas DNS recebe. A resolução DNS no dispositivo cliente também coloca em cache o resultado. A colocação em cache permite subsequentes consultas DNS ser respondidas mais rapidamente, utilizando os dados da cache em vez de consulta de outros servidores de nome. A duração da cache é determinada pela propriedade (TTL) 'time-to-live' de cada registo DNS. Valores mais curtos resultam em rápida expiração de cache e, por conseguinte, mais ida e volta para os servidores de nomes do Gestor de tráfego. Os valores mais significam que pode demorar mais longo para o tráfego direto na direção oposta ao ponto final de um falha. Gestor de tráfego permite-lhe configurar o valor de TTL utilizado nas respostas de DNS do Gestor de tráfego para ser tão elevado como 2,147,483,647 segundos e como tão baixo como 0 segundos (o intervalo máximo em conformidade com [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), permitindo-lhe escolher o valor que melhor equilibrar as necessidades da sua aplicação.

## <a name="pricing"></a>Preços

Para obter informações sobre preços, consulte [preços do Gestor de tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>FAQ

Para perguntas mais frequentes sobre o Gestor de tráfego, consulte [perguntas frequentes do Gestor de tráfego](traffic-manager-FAQs.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Gestor de tráfego [ativação pós-falha automática e monitorização do ponto final](traffic-manager-monitoring.md).

Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

