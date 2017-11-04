---
title: "Considerações sobre a topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory | Microsoft Docs"
description: "Abrange as considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 11244e0044eef8441e3a37ab8aeff0da30dacdb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory

Este artigo explica as considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory (Azure AD) para a publicar e aceder remotamente as suas aplicações.

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando uma aplicação publicada através do Azure AD para o Proxy de aplicações, fluxos de tráfego de utilizadores para as aplicações através de ligações de três:

1. O utilizador liga a Proxy de aplicações do Azure AD público ponto final do serviço no Azure
2. O serviço de Proxy de aplicações se liga ao conector do Proxy de aplicações
3. O conector do Proxy de aplicação estabelece ligação com a aplicação de destino

![Diagrama que mostra o fluxo de tráfego do utilizador para a aplicação de destino](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Localização de inquilino e o serviço Proxy de aplicações

Quando se inscreve para um inquilino do Azure AD, a região do seu inquilino é determinada pelo país que especificar. Quando ativar o Proxy da aplicação, as instâncias do serviço de Proxy de aplicações para o seu inquilino são selecionadas ou criadas na mesma região que o inquilino do Azure AD ou a região mais próxima ao mesmo.

Por exemplo, se a região do seu inquilino do Azure AD é a União Europeia (EU), todos os conectores de Proxy de aplicações a utilizam instâncias de serviço nos centros de dados do Azure no EU. Quando o acesso de utilizadores a aplicações publicadas, o tráfego atravessa as instâncias do serviço de Proxy de aplicações nesta localização.

## <a name="considerations-for-reducing-latency"></a>Considerações para reduzir a latência

Todas as soluções de proxy introduzem latência na sua ligação de rede. Independentemente do que na solução de VPN ou de proxy escolher como solução de acesso remoto, sempre inclui um conjunto de servidores, permitindo a ligação ao dentro da sua rede empresarial.

As organizações incluem, geralmente, os pontos finais do servidor na sua rede de perímetro. Com o Proxy de aplicações do Azure AD, no entanto, o tráfego flui através do serviço de proxy na nuvem enquanto os conectores de residirem na sua rede empresarial. Não é necessária nenhuma rede de perímetro.

As secções seguintes contêm sugestões adicionais para o ajudar a reduzir a latência ainda mais. 

### <a name="connector-placement"></a>Posicionamento de conector

Proxy de aplicações escolhe a localização de instâncias para si, com base na sua localização de inquilino. No entanto, pode obter para decidir onde pretende instalar o conector, que lhe confere a capacidade para definir as características de latência de tráfego de rede.

Quando configurar o serviço de Proxy de aplicações, faça as perguntas seguintes:

* Onde está localizada a aplicação?
* Onde estão localizados a maioria dos utilizadores aceder à aplicação?
* Onde está localizada a instância de Proxy de aplicações?
* Já tem uma ligação de rede dedicada para centros de dados do Azure, configurar, como o Azure ExpressRoute ou uma VPN semelhante?

O conector tem de comunicar com o Azure e as suas aplicações (os passos 2 e 3 no diagrama de fluxo de tráfego), por isso, o posicionamento do afeta o conector a latência das duas essas ligações. Ao avaliar a colocação do conector, tenha em consideração os seguintes pontos:

* Se pretender utilizar a delegação restrita de Kerberos (KCD) para o início de sessão único, em seguida, o conector tem de visão de uma linha para um datacenter. Além disso, o servidor do conector tem de ser associado a um domínio.  
* Quando em dúvida, instale o conector próximo à aplicação.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Pode minimizar a latência do tráfego ponto-a-ponto através da otimização de cada ligação de rede. Cada ligação pode ser otimizada por:

* Reduzir a distância entre as duas extremidades do salto.
* Escolher a rede à direita para atravessar. Por exemplo, o que atravessa a rede privada, em vez da Internet pública pode ser mais rápida, devido a ligações dedicadas.

Se tiver uma ligação VPN ou ExpressRoute dedicada entre o Azure e a sua rede empresarial, poderá utilizá-lo.

## <a name="focus-your-optimization-strategy"></a>Concentre-se a sua estratégia de otimização

Não há pouco que pode fazer para controlar a ligação entre os seus utilizadores e o serviço de Proxy de aplicações. Os utilizadores podem aceder às suas aplicações de uma rede doméstica, num café ou noutro país. Em vez disso, pode otimizar as ligações a partir do serviço de Proxy de aplicações para os conectores de Proxy de aplicações para as aplicações. Considere a incorporar os seguintes padrões no seu ambiente.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Padrão de 1: Colocar o conector próximo da aplicação

Coloque o conector próximo a aplicação de destino na rede de cliente. Esta configuração minimiza o passo 3 no diagrama topografia, porque o conector e a aplicação fechar. 

Se o conector tem de visão de uma linha para o controlador de domínio, é vantajoso este padrão. A maioria dos nossos clientes utiliza este padrão, porque funciona bem para a maioria dos cenários. Este padrão também pode ser conjugado com o padrão de 2 para otimizar o tráfego entre o serviço e o conector.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Padrão de 2: Tirar partido do ExpressRoute com o peering público

Se tiver configurado com um peering público do ExpressRoute, pode utilizar a ligação do ExpressRoute mais rápida para o tráfego entre o Proxy de aplicações e o conector. O conector está ainda na sua rede, próximo a aplicação.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Padrão de 3: Tirar partido do ExpressRoute com o peering privado

Se tiver uma VPN dedicada ou ExpressRoute configurar com o peering privado entre o Azure e a sua rede empresarial, tem outra opção. Nesta configuração, a rede virtual no Azure, normalmente, é considerada como uma extensão da rede empresarial. Por isso, pode instalar o conector no datacenter do Azure e, ainda satisfazer os requisitos de latência baixa da ligação de conector para aplicações.

Latência não fiquem comprometida porque o tráfego é que fluem através de uma ligação dedicada. Também obter a latência de conector do serviço de Proxy de aplicações melhorada porque o conector está instalado num datacenter do Azure próximo da sua localização de inquilino do Azure AD.

![Diagrama que mostra o conector instalado dentro de um datacenter do Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Outras abordagens

Embora este artigo aborda colocação do conector, também pode alterar a colocação da aplicação para obter um melhor características de latência.

Cada vez mais, as organizações estiver a mover as respetivas redes em ambientes alojados. Isto permite-lhes colocar as suas aplicações num ambiente alojado que também faz parte da sua rede empresarial e continuarão a estar dentro do domínio. Neste caso, os padrões abordados nas secções anteriores podem ser aplicados para a nova localização da aplicação. Se estiver a considerar esta opção, consulte o artigo [serviços de domínio do Azure AD](../active-directory-domain-services/active-directory-ds-overview.md).

Além disso, considere a organizar os conectores com [grupos conector](active-directory-application-proxy-connectors.md) para aplicações de destino que estão em diferentes localizações e as redes. 

## <a name="common-use-cases"></a>Casos de utilização comuns

Nesta secção, iremos guiá-lo através de alguns cenários comuns. Partem do princípio de que o inquilino do Azure AD (e, por conseguinte, ponto final de serviço do proxy) encontra-se nos Estados Unidos (E.U.A.). As considerações abordadas estes utilize casos também se aplicam a outras regiões à volta de todo o mundo.

Nestes cenários, podemos chamar cada ligação de um "salto" e numbê-los para debate mais fácil:

- **Salto 1**: utilizador para o serviço de Proxy de aplicações
- **Salto 2**: serviço de Proxy de aplicações para o conector do Proxy de aplicações
- **Salto 3**: conector de Proxy de aplicações para a aplicação de destino 

### <a name="use-case-1"></a>Caso de utilização 1

**Cenário:** a aplicação estiver numa rede de uma organização nos EUA, com utilizadores na mesma região. Não ExpressRoute VPN existe ou entre o Centro de dados do Azure e a rede empresarial.

**Recomendação:** siga padrão 1, explicado na secção anterior. Para latência melhorada, considere utilizar o ExpressRoute, se necessário.

Este é um padrão simple. Otimizar o salto 3, colocando o conector quase a aplicação. Também é uma escolha natural, porque o conector, normalmente, é instalado com a linha de visão para a aplicação e o Centro de dados para efetuar operações de KCD.

![Diagrama que mostra que os utilizadores, proxy, conector e aplicações estão todos em EUA](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso de utilização 2

**Cenário:** a aplicação estiver numa rede de uma organização nos EUA, com utilizadores distribuídos global. Não ExpressRoute VPN existe ou entre o Centro de dados do Azure e a rede empresarial.

**Recomendação:** siga padrão 1, explicado na secção anterior. 

Novamente, o padrão comum é otimizar o salto 3, onde colocar o conector quase a aplicação. Não é normalmente dispendioso, se for todos na mesma região salto 3. No entanto, salto 1 pode ser mais dispendioso, dependendo de onde está o utilizador, uma vez que os utilizadores por todo o mundo tem de aceder a instância de Proxy de aplicações nos E.U.A. É importante salientar que qualquer solução de proxy tem caraterísticas semelhantes sobre utilizadores que está a ser distribuídos globalmente.

![Diagrama que mostra que os utilizadores encontram-se distribuídas globalmente, mas o proxy, conector e aplicações estão nos E.U.A.](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso utilize 3

**Cenário:** a aplicação estiver numa rede de uma organização nos E.U.A. ExpressRoute com o peering público existe entre o Azure e a rede empresarial.

**Recomendação:** siga padrões 1 e 2, explicado na secção anterior.

Em primeiro lugar, coloque o conector conforme fechar quanto possível para a aplicação. Em seguida, o sistema utiliza automaticamente o ExpressRoute para o salto 2. 

Se a ligação do ExpressRoute estiver a utilizar peering público, o tráfego entre o proxy e o conector flui através dessa ligação. Salto 2 otimizou latência.

![Diagrama que mostra o ExpressRoute entre o proxy e o conector](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso de utilização 4

**Cenário:** a aplicação estiver numa rede de uma organização nos E.U.A. Existe ExpressRoute com o peering privado entre o Azure e a rede empresarial.

**Recomendação:** siga padrão 3, explicado na secção anterior.

Coloque o conector no datacenter do Azure que está ligado à rede empresarial através de peering privado do ExpressRoute. 

O conector pode ser colocado no datacenter do Azure. Uma vez que o conector ainda tem uma visão de linha para a aplicação e o Centro de dados através da rede privada, salto 3 permanece otimizado. Além disso, salto 2 está otimizado adicional.

![Diagrama que mostra o conector num datacenter do Azure e ExpressRoute entre o conector e a aplicação](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso de utilização 5

**Cenário:** a aplicação estiver numa rede de uma organização no EU, com a instância de Proxy de aplicações e a maioria dos utilizadores nos E.U.A.

**Recomendação:** colocar o conector quase a aplicação. Porque os utilizadores de E.U.A. estão a aceder a uma instância de Proxy de aplicações que ocorre estar na mesma região, salto 1 não é demasiado caro. Salto 3 está otimizado. Considere utilizar o ExpressRoute para otimizar o salto 2. 

![Diagrama que mostra os utilizadores e o proxy nos EUA, com o conector e a aplicação no EU](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Também pode considerar a utilizar um outro variante nesta situação. Se a maioria dos utilizadores na organização nos EUA, então possibilidades são que expande a sua rede para EUA, bem como. Coloque o conector nos E.U.A. e utilize a linha de rede empresarial interna dedicada para a aplicação a EU. Este saltos de forma 2 e 3 são otimizados.

![Diagrama que mostra os utilizadores, proxy e conector nos EUA, a aplicação no EU](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar o Proxy da aplicação](active-directory-application-proxy-enable.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Resolver problemas com o Proxy da aplicação](active-directory-application-proxy-troubleshoot.md)
