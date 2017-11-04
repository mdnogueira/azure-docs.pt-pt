---
title: "Métodos de encaminhamento de tráfego do Traffic Manager do Azure - | Microsoft Docs"
description: "Artigos de ajuda a compreender os métodos de encaminhamento de tráfego diferentes utilizados pelo Gestor de tráfego"
services: traffic-manager
documentationcenter: 
author: KumudD
manager: timlt
editor: 
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: fe776e24a4f78b389c6096694055b38befa3c419
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-routing-methods"></a>Métodos de encaminhamento do Traffic Manager

Traffic Manager do Azure suporta quatro métodos de encaminhamento de tráfego para determinar como encaminhar o tráfego de rede para os vários pontos finais de serviço. Gestor de tráfego aplica-se o método de encaminhamento de tráfego para cada consulta DNS que recebe. O método de encaminhamento de tráfego determina qual endpoint devolvido na resposta DNS.

Existem quatro métodos de encaminhamento de tráfego disponível no Gestor de tráfego:

* **[Prioridade](#priority):** selecione **prioridade** quando pretender utilizar um ponto final de serviço principal para todo o tráfego e fornecer cópias de segurança no caso o principal ou os pontos finais de cópia de segurança não estão disponíveis.
* **[Weighted](#weighted):** selecione **Weighted** quando pretender distribuir tráfego por um conjunto de pontos finais, o uniformemente ou de acordo com ponderações, que pode definir.
* **[Desempenho](#performance):** selecione **desempenho** quando tiver pontos finais em localizações geográficas diferentes e pretender que os utilizadores finais a utilizar o ponto final "mais próximo" em termos da menor latência de rede.
* **[Geográfica](#geographic):** selecione **geográfica** para que os utilizadores são direcionados para os pontos finais (do Azure, externo ou aninhados) com base na qual localização geográfica as respetivas consultas DNS tem origem. Isto proporciona aos clientes do Gestor de tráfego para ativar cenários onde saberem a região geográfica de um utilizador e o encaminhamento-los com base no que são importante. Os exemplos incluem complying com indica a soberania dos dados, localização de experiência de utilizador e de conteúdo e medir o tráfego de regiões diferentes.

Todos os perfis do Traffic Manager incluem a monitorização de estado de funcionamento do ponto final e ativação pós-falha do ponto final automática. Para obter mais informações, consulte [monitorização de ponto final do Gestor de tráfego](traffic-manager-monitoring.md). Um único perfil do Gestor de tráfego pode utilizar apenas um método de encaminhamento de tráfego. Pode selecionar um método de encaminhamento de tráfego diferentes para o seu perfil em qualquer altura. As alterações sejam aplicadas dentro de um minuto e, sem períodos de indisponibilidade é necessária. Métodos de encaminhamento de tráfego podem ser combinados utilizando aninhada perfis do Traffic Manager. Aninhamento permite sofisticadas e flexíveis configurações de encaminhamento de tráfego que satisfazem as necessidades das aplicações maiores e complexas. Para obter mais informações, consulte [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Método de encaminhamento de tráfego de prioridade

Muitas vezes, uma organização pretende fornecer fiabilidade para os respetivos serviços ao implementar um ou mais serviços de cópia de segurança no caso dos respetivos serviço primário fica inativo. O método de encaminhamento de tráfego de 'Priority' permite aos clientes do Azure facilmente implementar este padrão de ativação pós-falha.

![Traffic Manager do Azure 'Priority' método de encaminhamento de tráfego][1]

O perfil do Gestor de tráfego contém uma lista prioritária de pontos finais de serviço. Por predefinição, o Gestor de tráfego envia todo o tráfego para o ponto de final (mais alta prioridade) principal. Se o ponto final principal não estiver disponível, o Gestor de tráfego encaminha o tráfego para o segundo ponto final. Se ambos os principais e secundários pontos finais não estão disponíveis, passa o tráfego de terceiros e assim sucessivamente. Disponibilidade do ponto final baseia o estado configurado (ativado ou desativado) e a monitorização do ponto final em curso.

### <a name="configuring-endpoints"></a>Configurar pontos finais

Com o Azure Resource Manager, pode configurar a prioridade de ponto final utilizando explicitamente a propriedade 'priority' para cada ponto final. Esta propriedade é um valor entre 1 e 1000. Valores mais baixos representam uma prioridade mais alta. Pontos finais não podem partilhar os valores de prioridade. A definição da propriedade é opcional. Quando for omitido, é utilizada uma prioridade de predefinido com base na ordem de ponto final.

##<a name = "weighted"></a>Método de encaminhamento de tráfego ponderado
O método de encaminhamento de tráfego de mensagens em fila 'Ponderado' permite-lhe distribuir uniformemente o tráfego de ou para utilizar um peso predefinido.

![Azure Traffic Manager método da 'Ponderado' Encaminhamento de tráfego][2]

O método de encaminhamento de tráfego ponderado, atribua uma ponderação para cada ponto final na configuração de perfil do Traffic Manager. O peso é um número inteiro entre 1 e 1000. Este parâmetro é opcional. Se for omitido, o tráfego gestores utiliza uma ponderação predefinido de '1'.

Para cada consulta DNS recebida, o Gestor de tráfego escolhe aleatoriamente um ponto de final disponível. A probabilidade de escolher um ponto final é baseada em ponderações atribuídas para todos os pontos finais disponíveis. Em todos os resultados de pontos finais uma distribuição de tráfego, mesmo que a utilizar a mesma ponderação. Utilizar ponderações superiores ou inferiores em pontos finais específicos, faz com que esses pontos finais deve ser devolvida mais ou menos frequência nas respostas DNS.

O método ponderado permite alguns cenários úteis:

* Atualização da aplicação gradual: alocar uma percentagem do tráfego para encaminhar para um novo ponto final e aumentar gradualmente o tráfego ao longo do tempo para 100%.
* Migração de aplicação para o Azure: criar um perfil com pontos finais do Azure e externos. Ajuste a importância dos pontos finais para preferir os pontos finais de novo.
* Segurança na nuvem para a capacidade adicional: rapidamente expanda uma implementação no local para a nuvem colocando-o por trás de um perfil do Traffic Manager. Quando precisar de capacidade extra na nuvem, pode adicionar ou ativar mais pontos finais e especifique a que parte do tráfego entra em cada ponto final.

Portal do Azure Resource Manager suporta a configuração de encaminhamento de tráfego ponderado.  Pode configurar as ponderações utilizando as versões do Gestor de recursos do Azure PowerShell, CLI e as APIs REST.

É importante compreender que respostas DNS são colocadas em cache por clientes e os servidores DNS recursiva que os clientes utilizam para resolver nomes DNS. Esta colocação em cache, pode ter um impacto no distribuições ponderado tráfego. Quando o número de clientes e servidores DNS recursiva for grande, a distribuição de tráfego funciona conforme esperado. No entanto, quando o número de clientes ou servidores DNS recursiva for pequeno, colocação em cache pode significativamente prejudicar a distribuição de tráfego.

Casos de utilização comuns incluem:

* Ambientes de teste e desenvolvimento
* Comunicações de aplicação para aplicação
* Aplicações diversificado um estreito-base de utilizadores que partilhem uma infraestrutura DNS recursiva comum (por exemplo, os funcionários da empresa ao ligar através de um proxy)

Estes efeitos de colocação em cache DNS são comuns a todos os tráfego com base no DNS encaminhamento sistemas, não apenas Traffic Manager do Azure. Em alguns casos, explicitamente limpar a cache DNS poderá fornecer uma solução. Noutros casos, um método alternativo de encaminhamento de tráfego pode ser mais adequado.

## <a name = "performance"></a>Método de encaminhamento de tráfego de desempenho

Implementar pontos finais em dois ou mais localizações em todo o mundo, pode melhorar a capacidade de resposta de muitas aplicações ao tráfego de encaminhamento para a localização ' mais próximo '. O método de encaminhamento de tráfego de 'Desempenho' fornece esta capacidade.

![Traffic Manager do Azure 'Desempenho' método de encaminhamento de tráfego][3]

O ponto final 'mais próximo' não é necessariamente mais próximo, medido pela distância geográfica. Em vez disso, o método de encaminhamento de tráfego de 'Desempenho' determina o ponto final mais próximo, medindo a latência de rede. Gestor de tráfego mantém uma tabela de latência de Internet para rastrear o tempo reportadas round-trip entre intervalos de endereços IP e cada datacenter do Azure.

Gestor de tráfego procura o endereço IP de origem do pedido a receber DNS na tabela de latência de Internet. Gestor de tráfego escolhe um ponto de final disponível no Centro de dados do Azure que tem a latência mais baixa para esse intervalo de endereços IP, em seguida, devolve esse ponto final na resposta DNS.

Conforme explicado no [como Gestor de tráfego funciona](traffic-manager-overview.md#how-traffic-manager-works), Gestor de tráfego não receber as consultas de DNS diretamente a partir de clientes. Em vez disso, as consultas DNS provenientes do serviço DNS recursiva que os clientes estão configurados para utilizar. Por conseguinte, o endereço IP utilizado para determinar o ponto final 'mais próximo' não é um endereço IP do cliente, mas é o endereço IP do serviço DNS recursiva. Na prática, este endereço IP é um boa proxy para o cliente.


Gestor de tráfego atualiza regularmente a tabela de latência de Internet para a conta para que as alterações na global Internet e novo regiões do Azure. No entanto, o desempenho de aplicações varia consoante variações em tempo real na carga através da Internet. Encaminhamento de tráfego de desempenho não monitorizar a carga de um ponto final de serviço fornecido. No entanto, se um ponto final ficar indisponível, Gestor de tráfego não incluí-la nas respostas de consulta DNS.


Pontos a ter em atenção:

* Se o seu perfil contém vários pontos finais na mesma região do Azure, em seguida, do Traffic Manager distribui o tráfego equitativamente pelos pontos finais disponíveis nessa região. Se preferir uma distribuição de tráfego diferentes numa região, pode utilizar [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md).
* Se todos os pontos finais ativados na região do Azure mais próxima estão degradados, o Gestor de tráfego move o tráfego para os pontos finais na região do Azure mais próxima seguinte. Se pretender definir uma sequência de ativação pós-falha preferencial, utilize [aninhada perfis do Traffic Manager](traffic-manager-nested-profiles.md).
* Ao utilizar o método de encaminhamento de tráfego de desempenho com pontos finais externos ou de pontos finais aninhados, terá de especificar a localização desses pontos finais. Escolha a região do Azure mais próxima da sua implementação. Essas localizações são os valores suportados pela tabela de latência de Internet.
* O algoritmo de que escolhe o ponto final é determinística. Consultas DNS repetidas do mesmo cliente são direcionadas para o ponto final do mesmo. Normalmente, os clientes utilizam recursiva diferentes servidores DNS quando estiverem em deslocação. O cliente pode ser encaminhado para outro ponto final. Encaminhamento também pode ser afetado pelas atualizações para a tabela de latência de Internet. Por conseguinte, o método de encaminhamento de tráfego de desempenho não garante que um cliente é sempre encaminhado para o ponto final do mesmo.
* Quando altera a tabela de latência de Internet, poderá reparar que alguns clientes são direcionados para um ponto de final diferentes. Esta alteração de encaminhamento é mais exata com base nos dados de latência atual. Estas atualizações são essenciais para manter a precisão do encaminhamento de tráfego de desempenho à Internet medida que continuamente evolui.

## <a name = "geographic"></a>Método de encaminhamento de tráfego geográfico

Perfis do Traffic Manager podem ser configurados para utilizar o método de encaminhamento geográfico para que os utilizadores são direcionados para os pontos finais (do Azure, externo ou aninhados) com base no que localização geográfica as respetivas consultas DNS tem origem. Isto proporciona aos clientes do Gestor de tráfego para ativar cenários onde saberem a região geográfica de um utilizador e o encaminhamento-los com base no que são importante. Os exemplos incluem complying com indica a soberania dos dados, localização de experiência de utilizador e de conteúdo e medir o tráfego de regiões diferentes.
Quando é configurado um perfil para o encaminhamento geográfica, cada ponto final associado que perfil tem de ter um conjunto de regiões geográficas atribuída. Pode ser uma região demográfica nos seguintes níveis de granularidade 
- Mundo – qualquer região
- Regional agrupamento – por exemplo, África, Médio Oriente, Austrália/Pacífico etc. 
- País/região – por exemplo, Irlanda, Peru, RAE de Hong Kong etc. 
- Distrito – por exemplo, EUA Califórnia, Austrália-Queensland, etc. do Canadá Alberta (Nota: este nível de granularidade só é suportada para Estados / provinces da Austrália, Canadá, RU e E.U.A.).

Quando uma região ou um conjunto de regiões é atribuído a um ponto final, quaisquer pedidos dessas regiões obtém encaminhado apenas para esse ponto final. Gestor de tráfego utiliza o endereço IP de origem da consulta DNS para determinar a região a partir do qual um utilizador está a consultar de – normalmente, este é o endereço IP da resolução DNS local, fazer a consulta em nome do utilizador.  

![Azure Traffic Manager método da 'Geográfica' Encaminhamento de tráfego](./media/traffic-manager-routing-methods/geographic.png)

Gestor de tráfego lê o endereço IP de origem da consulta DNS e decide que está a ser provenientes de região geográfica. Em seguida, procura para ver se existe um ponto final que tenha esta região geográfica mapeada para este. Esta pesquisa é iniciado no nível mais baixo de granularidade (distrito onde é suportada, ou ao nível do país/região) e entra até até o nível mais elevado, o que é **mundo**. A correspondência de primeiro localizar utilizar este transversal está designado como o ponto final para devolver a resposta de consulta. Quando é devolvido a correspondência com um aninhados tipo ponto final, um ponto final nesse perfil subordinado, com base no respetivo método de encaminhamento. Os pontos seguintes são aplicáveis a este comportamento:

- Uma região demográfica pode ser mapeada apenas para um ponto final num perfil do Traffic Manager quando o tipo de encaminhamento é geográfica encaminhamento. Isto garante que o encaminhamento de utilizadores é determinista e os clientes, podem ativar cenários que necessitam de limites geográficos inequívoca.
- Se a região de um utilizador vem mapeamento geográfica em dois pontos finais diferentes, o Gestor de tráfego seleciona o ponto final com a granularidade mais baixa e considere pedidos de encaminhamento de nessa região para o ponto final. Por exemplo, considere um perfil de tipo de encaminhamento geográfica com dois pontos finais - Endpoint1 e Endpoint2. Endpoint1 está configurado para receber o tráfego de Irlanda e Endpoint2 está configurado para receber o tráfego da Europa. Se um pedido tem origem Irlanda, é sempre encaminhada para Endpoint1.
- Uma vez que uma região pode ser mapeada apenas para um ponto final, o Gestor de tráfego devolve-independentemente se o ponto final está em bom estado ou não.

    >[!IMPORTANT]
    >É vivamente recomendado que os clientes utilizando o método de encaminhamento geográfico associá-la com os pontos finais de tipo Nested que tem os perfis de subordinados que contém, pelo menos, dois pontos finais dentro de cada.
- Se for encontrada uma correspondência de ponto final e esse ponto final está a ser o **parado** Estado, o Gestor de tráfego devolve uma resposta NODATA. Neste caso, não existem mais pesquisas é efetuada superior cópias de segurança na hierarquia de região geográfica. Este comportamento também é aplicável a tipos de ponto final aninhada quando o perfil subordinado está no **parado** ou **desativado** estado.
- Se um ponto final apresenta um **desativado** Estado, este não será incluído na região do processo de correspondência. Este comportamento também é aplicável para tipos de ponto final aninhada quando o ponto final está no **desativado** estado.
- Se uma consulta for proveniente de uma região geográfica que não tem nenhum mapeamento desse perfil, o Gestor de tráfego devolve uma resposta NODATA. Por conseguinte, é vivamente recomendado que os clientes utilizam geográfica encaminhamento com um ponto final, idealmente tipo aninhadas com, pelo menos, dois pontos finais no perfil subordinado, com a região **mundo** atribuída. Isto também garante que os endereços IP que não mapeiam para uma região são processados.

Conforme explicado no [como Gestor de tráfego funciona](traffic-manager-how-traffic-manager-works.md), Gestor de tráfego não receber as consultas de DNS diretamente a partir de clientes. Em vez disso, as consultas DNS provenientes do serviço DNS recursiva que os clientes estão configurados para utilizar. Por conseguinte, o endereço IP utilizado para determinar a região não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursiva. Na prática, este endereço IP é um boa proxy para o cliente.


## <a name="next-steps"></a>Passos seguintes

Saiba como desenvolver aplicações de elevada disponibilidade utilizando [monitorização de pontos finais do Gestor de tráfego](traffic-manager-monitoring.md)

Saiba como [criar um perfil de Gestor de tráfego](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



