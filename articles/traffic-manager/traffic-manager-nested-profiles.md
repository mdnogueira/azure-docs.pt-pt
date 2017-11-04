---
title: Aninhada perfis do Traffic Manager | Microsoft Docs
description: "Este artigo explica a funcionalidade de 'Perfis aninhados' do Gestor de tráfego do Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 1ac4ec2775ca9f690f5adf4f939908f8cee3f715
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="nested-traffic-manager-profiles"></a>Aninhada perfis do Traffic Manager

Gestor de tráfego incluem uma variedade de métodos de encaminhamento de tráfego de mensagens em fila que lhe permitem controlar a forma como Gestor de tráfego escolhe o ponto final deve receber tráfego de cada utilizador final. Para obter mais informações, consulte [métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).

Cada perfil do Traffic Manager Especifica um único método de encaminhamento de tráfego. No entanto, existem cenários que necessitam que o encaminhamento fornecido por um único perfil do Traffic Manager o encaminhamento de tráfego mais sofisticadas. Podem aninhar perfis do Traffic Manager combinar as vantagens de mais do que um método de encaminhamento de tráfego. Perfis aninhados permitem-lhe substituir o comportamento predefinido do Gestor de tráfego para suporte maior e mais complexas implementações de aplicações.

Os exemplos seguintes mostram como utilizar aninhada perfis do Traffic Manager em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: Combinar 'Desempenho' e 'Weighted' o encaminhamento de tráfego

Suponha que implementou uma aplicação em regiões do Azure seguintes: EUA oeste, Europa Ocidental e Ásia Oriental. Utilize o método de encaminhamento de tráfego de 'Desempenho' do Gestor de tráfego para distribuir o tráfego para a região mais próxima ao utilizador.

![Perfil do Gestor de tráfego único][4]

Agora, suponha que pretende testar uma atualização ao seu serviço antes de a disponibilizar mais amplamente. Pretende utilizar o método de encaminhamento de tráfego de mensagens em fila 'ponderado' direcionar numa pequena percentagem de tráfego para a implementação de teste. Configurar a implementação de teste em conjunto com a implementação de produção existente na Europa Ocidental.

Não é possível combinar ambos 'Weighted' e ' desempenho-o encaminhamento de tráfego num único perfil. Para suportar este cenário, crie um perfil de Gestor de tráfego através de dois pontos finais da Europa Ocidental e o método de encaminhamento de tráfego de 'Weighted'. Em seguida, adicione este perfil de 'child' como um ponto final para o perfil de 'parent'. O perfil de principal ainda utiliza o método de encaminhamento de tráfego de desempenho e contém as outras implementações global, como pontos finais.

O diagrama seguinte ilustra este exemplo:

![Aninhada perfis do Traffic Manager][2]

Nesta configuração, tráfego direcionado através do perfil de principal distribui o tráfego em regiões normalmente. Na Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos finais de produção e teste, de acordo com as ponderações atribuídos.

Quando o perfil de principal utiliza o método de encaminhamento de tráfego de 'Desempenho', cada ponto final tem de ser atribuída uma localização. A localização é atribuída quando configurar o ponto final. Escolha a região do Azure mais próxima da sua implementação. Regiões do Azure são os valores da localização suportados pela tabela de latência de Internet. Para obter mais informações, consulte [Gestor de tráfego 'Desempenho' método de encaminhamento de tráfego](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: Monitorização de pontos finais em perfis aninhados

Gestor de tráfego monitoriza ativamente o estado de funcionamento de cada ponto final de serviço. Se um ponto final está danificado, o Gestor de tráfego direciona os utilizadores para os pontos finais alternativos para preservar a disponibilidade do seu serviço. Este comportamento de ativação pós-falha de monitorização e de ponto final aplica-se a todos os métodos de encaminhamento de tráfego. Para obter mais informações, consulte [monitorização de ponto final do Gestor de tráfego](traffic-manager-monitoring.md). Monitorização de pontos finais funciona de forma diferente de perfis aninhados. Com perfis aninhados, o perfil de principal não executar verificações do Estado de funcionamento no subordinado diretamente. Em vez disso, o estado de funcionamento dos pontos finais do perfil subordinado é utilizado para calcular o estado de funcionamento geral do perfil subordinado. Estas informações de estado de funcionamento são propagadas ascendentemente na hierarquia de perfis aninhados. O perfil de principal utiliza este estado de funcionamento agregado para determinar se deve direcionar o tráfego para o perfil subordinado. Consulte o [FAQ](traffic-manager-FAQs.md#traffic-manager-nested-profiles) para obter detalhes completos sobre como monitorizar o estado de funcionamento de perfis aninhados.

Regressar à anterior exemplo, suponha que a implementação de produção na Europa Ocidental falhar. Por predefinição, o perfil 'child' direciona todo o tráfego para a implementação de teste. Se a implementação de teste também falhar, o perfil de principal determina que o perfil do subordinado não deverá receber tráfego, uma vez que todos os pontos finais de subordinados estão em mau estado de funcionamento. Em seguida, o perfil de principal distribui o tráfego para as outras regiões.

![Aninhada perfil ativação pós-falha (comportamento predefinido)][3]

Poderá estar satisfeito com esta disposição. Ou poderá ser questão que todo o tráfego de Europa Ocidental agora vai para a implementação de teste em vez de tráfego um subconjunto limitado. Independentemente do Estado de funcionamento da implementação de teste, pretende efetuar a ativação pós-falha as outras regiões quando ocorre uma falha de implementação de produção na Europa Ocidental. Para ativar esta ativação pós-falha, pode especificar o parâmetro 'MinChildEndpoints' quando configurar o perfil subordinado como um ponto final no perfil do principal. O parâmetro determina o número mínimo de pontos finais disponíveis no perfil subordinado. O valor predefinido é '1'. Para este cenário, definir o valor de MinChildEndpoints para 2. Abaixo deste limiar, o perfil de principal considera o perfil subordinado todo fique indisponível e direciona o tráfego para os pontos finais.

A figura seguinte ilustra esta configuração:

![Aninhada de ativação pós-falha de perfil com 'MinChildEndpoints' = 2][4]

> [!NOTE]
> O método de encaminhamento de tráfego de 'Priority' distribui todo o tráfego para um único ponto final. Por conseguinte, há pouca objetivo com uma definição MinChildEndpoints diferente de '1' para um perfil subordinado.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: Definida regiões de ativação pós-falha no 'Desempenho' Encaminhamento de tráfego

O comportamento predefinido para o método de encaminhamento de tráfego de 'Desempenho' foi concebido para evitar carregar excessiva seguinte mais próximo ponto final e causar uma série de falhas em cascata. Quando um ponto final falha, todo o tráfego seria direcionado para esse ponto final é distribuída uniformemente para os pontos finais todas as regiões.

![Encaminhamento com predefinido de ativação pós-falha de tráfego de 'Desempenho'][5]

No entanto, suponha que prefere a ativação pós-falha de tráfego de Europa Ocidental EUA oeste e direcionar o tráfego a outras regiões apenas quando os dois pontos finais não estão disponíveis. Pode criar esta solução utilizando um perfil subordinado com o método de encaminhamento de tráfego de 'Priority'.

![Encaminhamento com taxas de ativação pós-falha de tráfego de 'Desempenho'][6]

Uma vez que o ponto final Europa Ocidental tem prioridade mais alta do que o ponto final de EUA oeste, todo o tráfego é enviado para o ponto final Europa Ocidental quando ambos os pontos finais estão online. Se falhar a Europa Ocidental, o tráfego é direcionado para EUA oeste. Com o perfil aninhado, o tráfego é direcionado para a Ásia Oriental apenas quando Europa Ocidental e EUA oeste falhar.

Pode repetir este padrão para todas as regiões. Substitua todos os três pontos finais no perfil de principal três perfis de subordinados, cada fornecendo uma sequência de ativação pós-falha prioritários.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: Controlar o encaminhamento de tráfego 'Desempenho' entre vários pontos finais na mesma região

Suponha que o desempenho' ' é utilizado o método de encaminhamento de tráfego num perfil que tem mais do que um ponto final numa região específica. Por predefinição, o tráfego direcionado para essa região é distribuído uniformemente em todos os pontos finais disponíveis nessa região.

![A distribuição de tráfego na região (comportamento predefinido) de encaminhamento de tráfego de 'Desempenho'][7]

Em vez de adicionar vários pontos finais na Europa Ocidental, esses pontos finais estão incluídos num perfil subordinada em separado. O perfil subordinado é adicionado ao principal como o ponto final só na Europa Ocidental. As definições no perfil subordinado podem controlar a distribuição de tráfego com Europa Ocidental ao ativar o encaminhamento de tráfego com base na prioridade ou ponderado nessa região.

![Tráfego de 'Desempenho' Encaminhamento com a distribuição de tráfego na região personalizado][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: As definições de monitorização por ponto final

Suponhamos que está a utilizar o Gestor de tráfego para migrar facilmente o tráfego de um legado no local do web site para uma nova versão baseado na nuvem alojado no Azure. Para o site legado, que pretende utilizar a página inicial de URI para monitorizar o estado de funcionamento do site. Mas para a nova versão baseado na nuvem, estiver a implementar uma página de monitorização personalizada (caminho ' / monitor.aspx') que inclui verificações adicionais.

![Ponto final do Gestor de tráfego de monitorização (comportamento predefinido)][9]

As definições de monitorização num perfil do Traffic Manager aplicam-se a todos os pontos finais dentro de um único perfil. Com perfis aninhados, utilize um perfil subordinado diferentes por site configurar diferentes definições de monitorização.

![Ponto final do Gestor de tráfego de monitorização com as definições de ponto-final][10]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [perfis do Traffic Manager](traffic-manager-overview.md)

Saiba como [criar um perfil de Gestor de tráfego](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
