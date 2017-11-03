---
title: "Principais relatórios da Verizon | Microsoft Docs"
description: "Pode ver os padrões de utilização para a CDN utilizando os seguintes relatórios: largura de banda, os dados transferidos, pedidos com êxito, os Estados da Cache, rácio de acertos na Cache, IPV4/IPV6 dados transferidos."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9ee0041061296e313b3372dce13b5b86b2369c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="core-reports-from-verizon"></a>Relatórios de núcleos da Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Através da Verizon Core relatórios através do portal de gerir para perfis da Verizon, pode ver os padrões de utilização para a CDN com os seguintes relatórios:

* Largura de Banda
* Dados transferidos
* Pedidos com êxito
* Estados da cache
* Rácio de acertos na cache
* Transferida de dados de IPv4/IPV6

## <a name="accessing-verizon-core-reports"></a>Ao aceder aos relatórios de núcleos da Verizon
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **análise** separador, em seguida, coloque o cursor sobre o **Core relatórios** flyout.  Clique no relatório pretendido no menu.
   
    ![Portal de gestão de CDN - menu de relatórios de núcleo](./media/cdn-reports/cdn-core-reports.png)

## <a name="bandwidth"></a>Largura de Banda
O relatório de largura de banda é composta por uma tabela de gráfico e os dados que indica a utilização de largura de banda para HTTP e HTTPS durante um período de tempo específico. Pode ver a utilização de largura de banda em faz todos os do CDN aparecer ou um específico POP do CDN. Este relatório permite-lhe ver os picos de tráfego e a distribuição entre CDN faz aparecer em Mbps.

* Selecione todos os nós de contorno para ver o tráfego de todos os nós ou escolha um região/nó na lista pendente.
* Selecione o intervalo de datas para ver dados atuais esta semana/mês, etc. ou introduzir datas personalizadas, em seguida, clique em **aceda** para se certificar de que a seleção é atualizada.
* Pode exportar e transferir os dados clicando no ícone de folha do excel localizado junto a **aceda**.

O relatório é atualizado a cada cinco minutos.

![Relatório de largura de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Dados transferidos
Este relatório é composto por uma tabela de dados e o gráfico indicado a utilização de tráfego para HTTP e HTTPS durante um período de tempo específico. Pode ver a utilização de tráfego em faz todos os do CDN aparecer ou um POP específico. Este relatório permite-lhe ver os picos de tráfego e a distribuição entre CDN faz aparecer em GB.

* Selecione todos os nós de contorno para ver o tráfego de todas as notas ou selecione um região/nó na lista pendente.
* Selecione o intervalo de datas para ver dados atuais esta semana/mês, etc. ou introduzir datas personalizadas, em seguida, clique em **aceda** para se certificar de que a seleção é atualizada.
* Pode exportar e transferir os dados clicando no ícone de folha do excel localizado junto a **aceda**.

O relatório é atualizado a cada cinco minutos.

![Dados transferidos relatório](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Pedidos com êxito (códigos de estado)
Este relatório descreve a distribuição de códigos de estado de pedido para o seu conteúdo. Todos os pedidos para conteúdo, gera um código de estado HTTP. O código de estado descreve como edge POPs processada o pedido. Por exemplo, um código de estado 2xx indica que o pedido foi servido com êxito para um cliente, enquanto um código de estado 4xx indica que ocorreu um erro. Para mais informações sobre códigos de estado HTTP, consulte [códigos de estado](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

* Selecione o intervalo de datas para ver dados atuais esta semana/mês, etc. ou introduzir datas personalizadas, em seguida, clique em **aceda** para se certificar de que a seleção é atualizada.
* Pode exportar e transferir os dados clicando a folha do excel localizada junto a **aceda**.

![Relatório de pedidos](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Estados da cache
Este relatório descreve a distribuição de acertos na cache e pedidos sem êxito de cache para o pedido de cliente. Porque o desempenho mais rápido provém de acertos na cache, pode otimizar a velocidades de entrega de dados, minimizando a pedidos sem êxito de cache e acertos na cache expirado. Pode reduzir pedidos sem êxito de cache ao configurar o servidor de origem para evitar a atribuição de cabeçalhos de resposta "não-cache", evitando a cadeia de consulta a colocação em cache, exceto onde estritamente necessária e pelo evitando códigos de resposta não colocáveis. Pode evitar acertos na cache expirada ao tornar idade um recurso máxima tão longas quanto possível minimizar o número de pedidos para o servidor de origem.

![Relatório de Estados de cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Estados de cache principal incluem:
* TCP_HIT: Servido a partir da extremidade. O objeto foi na cache e não foi excedido o respetiva idade máxima.
* TCP_MISS: Servido a partir de origem. O objeto não foi na cache e a resposta foi novamente para a origem.
* TCP_EXPIRED _MISS: servido a partir de origem após a revalidação de origem. O objeto foi na cache, mas tinha excedeu a respetiva idade máxima. Uma revalidação com origem resultou no objeto de cache a ser substituído por uma nova resposta da origem.
* TCP_EXPIRED _HIT: servidos limite após a revalidação de origem. O objeto foi na cache, mas tinha excedeu a respetiva idade máxima. Uma revalidação com o servidor de origem resultou no objeto de cache que está a ser não modificado.
* Selecione o intervalo de datas para ver dados atuais esta semana/mês, etc. ou introduzir datas personalizadas, em seguida, clique em **aceda** para se certificar de que a seleção é atualizada.
* Pode exportar e transferir os dados clicando no ícone de folha do excel localizado junto a **aceda**.

### <a name="full-list-of-cache-statuses"></a>Obter uma lista completa dos Estados de cache
* TCP_HIT - este estado é comunicado quando um pedido é servido diretamente a partir do POP ao cliente. Um recurso é servido imediatamente a partir de um POP quando é colocado em cache no POP mais próximo para o cliente e tem um válido time-to-live (TTL). TTL é determinado pelos seguintes cabeçalhos de resposta:
  
  * Cache-Control: s-maxage
  * Controlo de cache: idade máxima
  * Expirar
* TCP_MISS - este estado indica que uma versão em cache do elemento pedida não foi encontrada no POP mais próximo para o cliente. O elemento é pedido a partir de um servidor de origem ou de um servidor de proteção da origem. Se o servidor de origem ou o servidor de proteção da origem devolve um recurso, é fornecido ao cliente e colocadas em cache no cliente e o servidor edge. Caso contrário, um código de estado não 200 (por exemplo, 403 proibido ou 404 não encontrado) é devolvido.
* TCP_EXPIRED _HIT - este estado é comunicado quando um pedido que tenha como destino um elemento com um valor de TTL expirado servido diretamente a partir do POP ao cliente. Por exemplo, quando o elemento da idade máxima expirou. 
  
    Um pedido de expirada normalmente resulta num pedido revalidação para o servidor de origem. Para um _HIT TCP_EXPIRED ocorrer, o servidor de origem tem de indicar que uma versão mais recente do elemento não existe. Esta situação resulta normalmente numa atualização de Cache-Control e cabeçalhos de expira o elemento.
* TCP_EXPIRED _MISS - este estado é comunicado quando uma versão mais recente de um ativo em cache expirada é servida a partir de POP ao cliente. Este estado ocorre quando o valor de TTL para um ativo em cache expirou (por exemplo, expirado idade máxima) e o servidor de origem devolve uma versão mais recente esse recurso. Esta nova versão do elemento é fornecida para o cliente em vez da versão em cache. Além disso, este é colocado em cache no servidor edge e o cliente.
* CONFIG_NOCACHE - este estado indica que uma configuração específica do cliente no nosso edge POP impediu o elemento que está a ser colocados em cache.
* NENHUM - este estado indica que não foi efetuada uma verificação de conteúdo de actualização da cache.
* TCP_ CLIENT_REFRESH _MISS - este estado é comunicado quando um cliente HTTP, por exemplo, um browser, força uma extremidade POP para obter uma nova versão de um recurso obsoleto do servidor de origem.
  
    Por predefinição, nossos servidores impedem que um cliente HTTP forçar os nossos servidores edge para obter uma nova versão do elemento do servidor de origem.
* TCP_ PARTIAL_HIT - este estado é comunicado quando um pedido de intervalo de byte resulta num acessos para um ativo parcialmente em cache. O intervalo de bytes pedido imediatamente é servido a partir de POP ao cliente.
* UNCACHEABLE - este estado é comunicado quando cabeçalhos Cache-Control e expira de um recurso indicam que este deve não ser colocadas em cache num POP ou pelo cliente HTTP. São servidos estes tipos de pedidos do servidor de origem.

## <a name="cache-hit-ratio"></a>Rácio de acertos na cache
Este relatório indica a percentagem de pedidos em cache que foram enviadas diretamente a partir da cache.

O relatório fornece os seguintes detalhes:

* O conteúdo solicitado foi colocado em cache no POP mais próximo para o autor do pedido.
* O pedido foi servido diretamente a partir do limite da nossa rede.
* O pedido não obrigava a revalidação com o servidor de origem.

O relatório não incluem:

* Pedidos que são negados devido a opções de filtragem de país.
* Pedidos de recursos cujos cabeçalhos indicam que eles devem não ser colocados em cache. Por exemplo, a Cache-Control: privada, Cache-Control: não-cache ou Pragma: cabeçalhos de cache não impedem que um recurso que está a ser colocados em cache.
* Pedidos de intervalo de byte para o conteúdo parcialmente em cache.

A fórmula é: (TCP_ ACESSOS / (TCP_ ACESSOS + TCP_MISS)) * 100

* Selecione o intervalo de datas para ver dados atuais esta semana/mês, etc. ou introduzir datas personalizadas, em seguida, clique em **aceda** para se certificar de que a seleção é atualizada.
* Pode exportar e transferir os dados clicando no ícone de folha do excel localizado junto a **aceda**.

![Relatório de rácio de acertos na cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Transferir os dados de IPv4/IPV6
Este relatório mostra a distribuição de utilização de tráfego no IPV4 vs IPV6.

![Transferir os dados de IPv4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

* Selecione o intervalo de datas para ver dados atuais esta semana/mês, etc. ou introduzir datas personalizadas.
* Em seguida, clique em **aceda** para se certificar de que a seleção é atualizada.

## <a name="considerations"></a>Considerações
Relatórios só podem ser gerados dentro os últimos 18 meses.

