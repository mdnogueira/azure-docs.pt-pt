---
title: "Considerações de desempenho para o Gestor de tráfego do Azure | Microsoft Docs"
description: "Compreender o desempenho no Gestor de tráfego e como testar o desempenho do seu Web site ao utilizar o Gestor de tráfego"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: f686685138625a53971f1fc5fc754fd22c9d67b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

Esta página explica as considerações de desempenho utilizando o Gestor de tráfego. Considere o seguinte cenário:

Tem de instâncias do seu site nas regiões WestUS e EastAsia. Uma das instâncias está a falhar a verificação de estado de funcionamento para a sonda do Gestor de tráfego. Tráfego de aplicação é direcionado para a região de bom estado de funcionamento. Esta ativação pós-falha é esperada, mas o desempenho pode ser um problema com base na latência de tráfego agora estiverem em deslocação região distantes.

## <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gestor de Tráfego

O impacto de desempenho apenas que o Gestor de tráfego pode ter no seu Web site é a pesquisa DNS inicial. Um pedido DNS para o nome do perfil do Gestor de tráfego é processado pelo servidor de raiz do Microsoft DNS que aloja o horário de trafficmanager.net. Gestor de tráfego preenche e atualiza regularmente, servidores de raiz DNS da Microsoft com base na política do Gestor de tráfego e os resultados de pesquisa. Por isso, mesmo durante a pesquisa de DNS inicial, não existem consultas DNS são enviadas para o Gestor de tráfego.

Gestor de tráfego é constituído por vários componentes: nome de DNS, servidores, um serviço de API, a camada de armazenamento e um ponto final de serviço de monitorização. Se um componente de serviço do Gestor de tráfego falhar, não há nenhum efeito sobre o nome DNS associado ao perfil do Traffic Manager. Os registos nos servidores Microsoft DNS permanecem inalterados. No entanto, a monitorização de ponto final e atualizar o DNS não ocorrer. Por conseguinte, o Gestor de tráfego não é capaz de atualizar o DNS para apontar para o site de ativação pós-falha quando o site primário fica inativo.

Resolução de nomes DNS é rápida e os resultados são colocadas em cache. A velocidade da pesquisa DNS inicial depende os servidores DNS, que o cliente utiliza para resolução de nomes. Normalmente, um cliente pode realizar uma pesquisa DNS no ~ 50 ms. Os resultados da pesquisa são colocadas em cache durante o DNS Time-to-live (TTL). O TTL para o Gestor de tráfego predefinido é de 300 segundos.

Não, o tráfego de fluir através do Gestor de tráfego. Uma vez concluída a pesquisa de DNS, o cliente tem um endereço IP para uma instância do seu web site. O cliente liga-se diretamente a esse endereço e não a passar através do Gestor de tráfego. A política do Gestor de tráfego que escolher não tem nenhum influência sobre o desempenho de DNS. No entanto, um desempenho encaminhamento-método pode afetar negativamente a experiência de aplicação. Por exemplo, se a sua política redireciona o tráfego da América do Norte para uma instância alojado na Ásia, a latência de rede para essas sessões pode ser um problema de desempenho.

## <a name="measuring-traffic-manager-performance"></a>Medir o desempenho do Gestor de tráfego

Existem vários sites, que pode utilizar para compreender o desempenho e o comportamento de um perfil do Traffic Manager. Muitas destes sites gratuitas, mas poderão ter limitações. Alguns sites oferecem avançada de monitorização e relatórios para uma taxa.

As ferramentas nestes sites medidas DNS latências e apresentar os resolver endereços IP para localizações de cliente em todo o mundo. A maioria destas ferramentas não colocar em cache os resultados DNS. Por conseguinte, as ferramentas de mostram a pesquisa DNS completa sempre que é executado um teste. Quando testa a partir do seu próprio cliente, só experiência de desempenho de pesquisa DNS completo uma vez durante a duração do TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ferramentas de exemplo para medir o desempenho de DNS

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS oferece muitas ferramentas de desempenho. A ferramenta de comparação de DNS pode apresentar-lhe tempo que demora para resolver o nome de DNS e como que compara com outros fornecedores de serviços DNS.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Uma das ferramentas mais simples é WebSitePulse. Introduza o URL para ver o tempo de resolução DNS, o primeiro Byte, o último Byte e outras estatísticas de desempenho. Pode escolher entre três localizações de teste diferentes. Neste exemplo, pode ver que a primeira execução mostra que a pesquisa de DNS demora 0.204 seg.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Porque os resultados são colocadas em cache, o segundo teste para o mesmo ponto final do Gestor de tráfego a pesquisa de DNS demora 0.002 seg.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor de aplicação Synthetic de AC](https://asm.ca.com/en/checkit.php)

    Anteriormente conhecida como a ferramenta de Web site de verificação Watchmouse, este site mostrar o tempo de resolução DNS de várias regiões geográficas em simultâneo. Introduza o URL para ver o tempo de resolução DNS, o tempo de ligação e velocidade de várias localizações geográficas. Utilize este teste para ver o serviço alojado é devolvido para as diferentes localizações em todo o mundo.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Esta ferramenta fornece estatísticas de desempenho para cada elemento de uma página web. O separador de análise de página apresenta a percentagem de tempo gasto na pesquisa DNS.

* [O que é o meu DNS?](http://www.whatsmydns.net/)

    Este site não uma pesquisa de DNS 20 diferentes localizações e apresenta os resultados num mapa.

* [Aprofundar Web Interface](http://www.digwebinterface.com)

    Este site mostra que informações de DNS, incluindo CNAMEs e registos mais detalhadas. Certifique-se que verifique 'Colorize saída' e 'Estatísticas' em Opções e selecionar 'Tudo' em Nameservers.

## <a name="next-steps"></a>Passos Seguintes

[Sobre os métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md)

[Testar as definições do Gestor de tráfego](traffic-manager-testing-settings.md)

[Operações do Gestor de Tráfego (Referência da API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gestor de tráfego do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

