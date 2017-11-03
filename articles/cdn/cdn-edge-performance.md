---
title: "Analisar o desempenho do nó de extremidade na CDN do Azure | Microsoft Docs"
description: "Analise o desempenho do nó de extremidade na CDN do Microsoft Azure. Análise de desempenho de limite fornece informações granulares de tráfego e largura de banda de utilização para a CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad285b4e2226c85859acb22ba214cc44c77c08e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analisar o desempenho do nó de extremidade na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Análise de desempenho de limite fornece informações granulares de tráfego e largura de banda de utilização para a CDN. Estas informações podem ser utilizadas para gerar estatísticas tendências, que lhe permitem obter conhecimentos aprofundados sobre a forma como os recursos estão a ser colocados em cache e entregue aos seus clientes. Por sua vez, isto permite-lhe formar uma estratégia sobre como otimizar a entrega de conteúdo e determinar o que devem ser problemas tackled para melhor tire partido da CDN. Como resultado, não só irá poderá melhorar o desempenho de entrega de dados, mas também poderá reduzir os custos da CDN.

> [!NOTE]
> Todos os relatórios utilizam a notação de UTC/GMT ao especificar uma data/hora.
> 
> 

## <a name="reports-and-log-collection"></a>Relatórios e recolha de registos
Dados de atividade da CDN devem ser recolhidos pelo módulo de análise de desempenho de limite antes de pode gerar relatórios no mesmo. Este processo de recolha ocorre uma vez por dia e abrange a atividade demorou local durante o dia anterior. Isto significa que as estatísticas de um relatório representam uma amostra de estatísticas do dia no momento que foi processada e não necessariamente conter o conjunto completo de dados para o dia atual. A função principal destes relatórios consiste em avaliar o desempenho. Não deve ser utilizadas para fins de faturação ou estatísticas numérico exatas.

> [!NOTE]
> Os dados não processados a partir da qual são gerados relatórios de análise de desempenho do limite estão disponíveis pelo menos 90 dias.
> 
> 

## <a name="dashboard"></a>Dashboard
O dashboard de análise de desempenho de limite controla atual e histórico tráfego CDN através de um gráfico e estatísticas. Utilize este dashboard para detetar as tendências de recentes e a longo prazo, no desempenho de tráfego da CDN para a sua conta.

Este dashboard é composta por:

* Um gráfico interativo que lhe permite a visualização das tendências e as métricas-chave.
* Numa linha cronológica que fornece um sentido de padrões de longo prazo para as métricas-chave e as tendências.
* As principais métricas e informações estatísticas sobre nossa rede CDN melhora o tráfego do site, medido pela geral de desempenho, utilização e eficiência.

### <a name="accessing-the-edge-performance-dashboard"></a>Aceder ao dashboard de desempenho do contorno
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **análise** separador, em seguida, coloque o cursor sobre o **Edge Perfomance análise** flyout.  Clique em **Dashboard**.
   
    É apresentado o dashboard de análise de nó de extremidade.

### <a name="chart"></a>Gráfico
O dashboard contém um gráfico que controla uma métrica durante o período de tempo selecionado na linha de tempo que aparece diretamente abaixo do mesmo.  Numa linha cronológica que gráficos para o último até dois anos de atividade CDN, é apresentada diretamente abaixo do gráfico.

#### <a name="using-the-chart"></a>Utilizar o gráfico
* Por predefinição, será possível charted a taxa de eficiência da cache para os últimos 30 dias.
* Este gráfico é gerado a partir dos dados agrupados numa base diária.
* Posicionado sobre um dia no gráfico de linha indica uma data e o valor da métrica essa data.
* Clique em realce fins de semana para alternar uma sobreposição de leves barras verticais cinzentos que representam os fins de semana no gráfico. Este tipo de sobreposição é útil para identificar padrões de tráfego através de fim de semana.
* Clique em vista de um ano há para alternar uma sobreposição de atividade do ano anterior durante o mesmo período de tempo no gráfico. Este tipo de comparação fornece informações sobre os padrões de utilização CDN longo prazo. Canto superior direito do gráfico contém uma legenda que indica o código de cor para cada gráfico de linha.

#### <a name="updating-the-chart"></a>Atualizar o gráfico
* Intervalo de tempo: Efetue um dos seguintes procedimentos:
  * Selecione a região pretendida na linha de tempo. O gráfico será atualizado com os dados que corresponde ao período de tempo selecionado.
  * Faça duplo clique o gráfico para apresentar todos os dados históricos disponíveis até um máximo de dois anos.
* Métrica: Clique no ícone de gráfico que é apresentado junto a métrica pretendida. O gráfico e a linha cronológica serão atualizados com os dados para a métrica correspondente.

### <a name="key-metrics-and-statistics"></a>As métricas-chave e as estatísticas
#### <a name="efficiency-metrics"></a>Métricas de eficiência
É o objetivo estas métricas ver se a eficiência da cache pode ser melhorada. Os principais benefícios derivados eficiência da cache são:

* Carga reduzida no servidor de origem que pode levar a:
  * Melhor desempenho do servidor web.
  * Redução de custos operacionais.
* Melhorado aceleração de entrega de dados, uma vez que irão ser servidos pedidos mais diretamente da CDN.

| Campo | Descrição |
| --- | --- |
| Eficiência da cache |Indica a percentagem dos dados transferidos que servido a partir da cache. Esta métrica medidas quando uma versão em cache do conteúdo pedido foi servido diretamente da CDN (servidores edge) para autores de pedido (por exemplo, browser web) |
| Taxa de acertos |Indica a percentagem de pedidos que foram enviadas a partir da cache. Esta métrica medidas quando uma versão em cache do conteúdo pedido foi servido diretamente da CDN (servidores edge) para autores de pedido (por exemplo, browser web). |
| % dos Bytes remotos - nenhuma configuração de Cache |Indica a percentagem do tráfego foi servido a partir de servidores de origem para a CDN (servidores edge) que será não ser colocadas em cache devido a funcionalidade de ignorar a Cache (motor de regras de HTTP). |
| % dos Bytes remotos - Cache expirado |Indica a percentagem do tráfego foi servido a partir de servidores de origem para a CDN (servidores edge) como resultado obsoleta revalidação de conteúdo. |

#### <a name="usage-metrics"></a>Métricas de utilização
O objetivo estas métricas é obter informações sobre as seguintes medidas de custo corte:

* Minimizar os custos operacionais pela CDN.
* Reduzir as despesas CDN através da eficiência da cache e compressão.

> [!NOTE]
> Números de volume de tráfego representam o tráfego que foi utilizado nos cálculos de rácios e percentagens e poderá mostrar apenas uma parte do tráfego total de clientes de elevado volume.
> 
> 

| Campo | Descrição |
| --- | --- |
| Ave Bytes enviados |Indica o número médio de bytes transferidos para cada pedido servido da CDN (servidores edge) para o autor do pedido (por exemplo, browser web). |
| Taxa de bytes de configuração não Cache |Indica a percentagem do tráfego da CDN (servidores edge) para o autor do pedido (por exemplo, browser web) que será não ser colocadas em cache devido a funcionalidade de ignorar a Cache. |
| Taxa de bytes comprimida |Indica a percentagem de tráfego enviado da CDN (servidores edge) para autores de pedido (por exemplo, browser web) num formato comprimido. |
| Bytes enviados |Indica a quantidade de dados, em bytes, que foram fornecidas da CDN (servidores edge) para o autor do pedido (por exemplo, browser web). |
| Bytes na |Indica a quantidade de dados, em bytes, enviadas pelo autores de pedido (por exemplo, browser web) para a CDN (servidores edge). |
| Bytes remoto |Indica a quantidade de dados, em bytes, enviados a partir de servidores de origem da CDN e cliente para a CDN (servidores edge). |

#### <a name="performance-metrics"></a>Métricas de desempenho
O objetivo estas métricas consiste em monitorizar o desempenho global da CDN para o tráfego.

| Campo | Descrição |
| --- | --- |
| Velocidade de transferência |Indica a taxa média de que o conteúdo foi transferido da CDN para um autor de pedido. |
| Duração |Indica o tempo médio, em milissegundos, demorou a entrega de um recurso a um autor de pedido (por exemplo, browser web). |
| Taxa de pedidos comprimida |Indica a percentagem de pedidos que foram fornecidas da CDN (servidores edge) para o autor do pedido (por exemplo, browser web) num formato comprimido. |
| Taxa de erros de 4xx |Indica a percentagem de pedidos que gerou um código de estado 4xx. |
| Taxa de erros de 5XX |Indica a percentagem de pedidos que gerou um código de estado 5xx. |
| Pedidos com êxito |Indica o número de pedidos para o conteúdo do CDN. |

#### <a name="secure-traffic-metrics"></a>Métrica do tráfego seguro
O objetivo estas métricas consiste em monitorizar o desempenho da CDN para tráfego HTTPS.

| Campo | Descrição |
| --- | --- |
| Proteger a eficiência da Cache |Indica a percentagem dos dados transferidos para pedidos HTTPS servidos foram da cache. Esta métrica medidas quando uma versão em cache do conteúdo pedido foi servido diretamente da CDN (servidores edge) para autores de pedido (por exemplo, browser web) através de HTTPS. |
| Taxa de transferência segura |Indica a taxa média de que o conteúdo foi transferido da CDN (servidores edge) para autores de pedido (por exemplo, servidores web) através de HTTPS. |
| Duração média de segura |Indica o tempo médio, em milissegundos, demorou a entrega de um recurso a um autor de pedido (por exemplo, browser web) através de HTTPS. |
| Proteger os pedidos com êxito |Indica o número de pedidos HTTPS para o conteúdo do CDN. |
| Proteger os Bytes enviados |Indica a quantidade de tráfego HTTPS, em bytes, que foram fornecidas da CDN (servidores edge) para o autor do pedido (por exemplo, browser web). |

## <a name="reports"></a>Relatórios
Cada relatório neste módulo contém um gráfico e estatísticas de utilização de largura de banda e o tráfego para diferentes tipos de métricas (por exemplo, códigos de estado HTTP, códigos de estado da cache, o pedido URL, etc.). Estas informações podem ser utilizadas para delve mais profundo na forma como a ser servido conteúdo para os seus clientes e para otimizar o comportamento da CDN para melhorar o desempenho de entrega de dados.

### <a name="accessing-the-edge-performance-reports"></a>Aceder os relatórios de desempenho do contorno
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **análise** separador, em seguida, coloque o cursor sobre o **Edge Perfomance análise** flyout.  Clique em **objeto grande HTTP**.
   
    É apresentado o ecrã de relatórios de análise de nó de extremidade.

| Relatório | Descrição |
| --- | --- |
| Resumo diário |Permite-lhe ver tendências de tráfego diária durante um período de tempo especificado. Cada barra neste gráfico representa uma data específica. O tamanho da barra indica o número total de pedidos que ocorreu a essa data. |
| Resumo por hora |Permite-lhe ver tendências de tráfego de hora a hora durante um período de tempo especificado. Cada barra neste gráfico representa uma hora único numa data específica. O tamanho da barra indica o número total de pedidos que ocorreram durante essa hora. |
| Protocolos |Mostra a divisão do tráfego entre os protocolos HTTP e HTTPS. Um gráfico de anel indica a percentagem de pedidos que ocorreram para cada tipo de protocolo. |
| Métodos de HTTP |Permite-lhe ter uma noção rápida que HTTP métodos estão a ser utilizados para pedir os seus dados. Normalmente, os métodos de pedido HTTP mais comuns são GET, HEAD e POST. Um gráfico de anel indica a percentagem de pedidos que ocorreram para cada tipo de método de pedido HTTP. |
| URLs |Contém um gráfico que mostra os URLs de pedido 10 principais. É apresentada uma barra para cada URL. A altura da barra indica quantos pedidos que determinado URL tiver gerado durante o intervalo de tempo abrangido pelo relatório. Estatísticas para os primeiros 100 pedida que URLs são apresentados diretamente abaixo este gráfico. |
| CNAMEs |Contém um gráfico que mostra as principais span 10 CNAMEs utilizados para pedir ativos ao longo do tempo de um relatório. Estatísticas para os primeiros 100 pedida que CNAMEs são apresentados diretamente abaixo este gráfico. |
| Origens |Contém um gráfico que mostra a CDN 10 principais ou servidores de origem do cliente de que recursos foram pedidos num determinado período de tempo. Estatísticas para os primeiros 100 pedida CDN ou cliente servidores de origem são apresentados diretamente abaixo este gráfico. Servidores de origem do cliente são identificados pelo nome definido na opção de nome de diretório. |
| Georreplicação POPs |Mostra a quantidade de tráfego está a ser encaminhado através de uma determinado ponto de presença (POP). A abreviatura com três letras representa um POP na nossa rede CDN. |
| Clientes |Contém um gráfico que mostra os principais 10 clientes que pediram ativos num determinado período de tempo. Para efeitos deste relatório, todos os pedidos provenientes do mesmo endereço IP são considerados como sendo do mesmo cliente. As estatísticas de 100 clientes principais são apresentadas diretamente abaixo este gráfico. Este relatório é útil para determinar os padrões de atividade de transferência para os seus clientes superiores. |
| Estados da cache |Fornece uma repartição de detalhado de comportamento da cache, que pode revelar abordagens para melhorar a experiência de utilizador final global. Uma vez que o desempenho mais rápido provém de acertos na cache, pode otimizar a velocidades de entrega de dados, minimizando a pedidos sem êxito de cache e acertos na cache expirado. |
| NENHUM detalhes |Contém um gráfico que mostra os URLs de 10 principais para recursos para o qual reatualização conteúdo da cache não foi verificada através de um período de tempo especificado. Estatísticas para os URLs de 100 superiores para estes tipos de recursos são apresentadas diretamente abaixo este gráfico. |
| Detalhes CONFIG_NOCACHE |Contém um gráfico que mostra os URLs de 10 principais para recursos que não foram colocadas em cache devido a configuração da CDN do cliente. Estes tipos de recursos foram servidos diretamente a partir do servidor de origem. Estatísticas para os URLs de 100 superiores para estes tipos de recursos são apresentadas diretamente abaixo este gráfico. |
| Detalhes UNCACHEABLE |Contém um gráfico que mostra os URLs de 10 principais para recursos que podem não ser colocadas em cache devido a dados de cabeçalho de pedido. Estatísticas para os URLs de 100 superiores para estes tipos de recursos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_HIT |Contém um gráfico que mostra os URLs de 10 principais para recursos que são servidos imediatamente a partir da cache. Estatísticas para os URLs de 100 superiores para estes tipos de recursos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_MISS |Contém um gráfico que mostra os URLs de 10 principais para recursos que têm um Estado de cache de TCP_MISS. Estatísticas para os URLs de 100 superiores para estes tipos de recursos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_EXPIRED_HIT |Contém um gráfico que mostra os URLs de 10 principais para obsoletos ativos que foram enviados diretamente a partir do POP. Estatísticas para os URLs de 100 superiores para estes tipos de recursos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_EXPIRED_MISS |Contém um gráfico que mostra os URLs de 10 principais para ativos obsoletos para o qual uma nova versão que tiveram de ser obtidos a partir do servidor de origem. Estatísticas para os URLs de 100 superiores para estes tipos de recursos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_CLIENT_REFRESH_MISS |Contém um gráfico de barras que mostra os URLs de 10 principais para recursos foram obtidos a partir de um servidor de origem devido a um pedido de não-cache do cliente. Estatísticas para os URLs de 100 superiores para estes tipos de pedidos são apresentadas diretamente abaixo este gráfico. |
| Tipos de pedido de cliente |Indica o tipo de pedidos que foram efetuadas por clientes HTTP (por exemplo, browsers). Este relatório inclui um gráfico de anel que fornece um sentido relativamente a forma como os pedidos estão a ser processados. Informações de tráfego e da largura de banda para cada tipo de pedido são apresentadas abaixo do gráfico. |
| Agente de utilizador |Contém um gráfico de barras apresenta os agentes de 10 utilizador principais para solicitar o conteúdo através do nosso CDN. Normalmente, um agente de utilizador é um web browser, leitor de multimédia ou um browser de telemóvel. Estatísticas para os agentes de 100 utilizador principais são apresentadas diretamente abaixo este gráfico. |
| Referrers |Contém um gráfico de barras apresentar os 10 referrers superiores ao conteúdo acedido através do nosso CDN. Normalmente, uma referência é o URL da página web ou recurso que liga ao seu conteúdo. São fornecidas informações detalhadas abaixo o gráfico para referrers os primeiros 100. |
| Tipos de compressão |Contém um gráfico de anel divide ativos pedidos pelo se foram comprimidos pelos nossos servidores edge. A percentagem de recursos comprimidos é reduzida por tipo de compressão utilizado. São fornecidas informações detalhadas abaixo o gráfico para cada tipo de compressão e o estado. |
| Tipos de ficheiro |Contém um gráfico de barras que mostra os principais tipos de 10 ficheiro que pediu através do nosso CDN para a sua conta. Para efeitos deste relatório, um tipo de ficheiro é definido pela extensão de nome de ficheiro do recurso e tipo de suporte de Internet (por exemplo,. HTML \[texto/html\], *.htm \[texto/html\],. aspx \[texto/html\], etc.). São fornecidas informações detalhadas abaixo o gráfico para os principais tipos de 100 ficheiros. |
| Ficheiros exclusivos |Contém um gráfico que rastreia o número total de recursos exclusivos que foram pedidos num dia específico durante um período de tempo especificado. |
| Token de autenticação de resumo |Contém um gráfico circular que fornece uma rápida descrição geral sobre se os recursos de pedido foram protegidos pela autenticação baseada em tokens. Recursos protegidos são apresentados no gráfico, de acordo com os resultados da respetiva autenticação tentativa. |
| Detalhes de negação de autenticação de token |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que foram negados devido a autenticação baseada em tokens. |
| Códigos de resposta HTTP |Fornece uma repartição dos códigos de estado de HTTP (por exemplo, 200 OK, 403 proibido, não foi encontrada 404, etc.) que foram fornecidas aos seus clientes HTTP pelos nossos servidores edge. Um gráfico circular permite-lhe avaliar rapidamente a forma como os recursos foram servidos. Dados de estatísticos detalhados são fornecidos para cada código de resposta abaixo do gráfico. |
| 404 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta não foi encontrado 404. |
| 403 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta proibido 403. Um código de resposta 403 Proibido ocorre quando um pedido é negado por um servidor de origem do cliente ou um servidor edge no nosso POP. |
| Erros de 4xx |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta no intervalo entre 400. Excluídos deste relatório são 403 não foi encontrada e 404 códigos de resposta de proibido. Normalmente, um código de resposta 4xx ocorre quando um pedido é negado devido a um erro do cliente. |
| 504 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta de tempo limite do Gateway 504. Um código de resposta de tempo limite do Gateway 504 ocorre quando um tempo limite ocorre quando um proxy HTTP está a tentar comunicar com outro servidor. No caso do nosso CDN, um código de resposta de tempo limite do Gateway 504 ocorre normalmente quando um servidor edge não é possível estabelecer a comunicação com um servidor de origem do cliente. |
| 502 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta Gateway incorreto 502. Um código de resposta de Gateway incorreto 502 ocorre quando ocorre uma falha de protocolo HTTP entre um servidor e um proxy HTTP. No caso do nosso CDN, um código de resposta de Gateway incorreto 502 ocorre normalmente quando um servidor de origem do cliente devolve uma resposta inválida para um servidor edge. Uma resposta é inválida, se este não é possível analisar ou se está incompleto. |
| Erros de 5XX |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta no intervalo 500.  Excluídos deste relatório são 502 Gateway incorreta e 504 códigos de resposta de tempo limite do Gateway. |

## <a name="see-also"></a>Consultar também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real na CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)
* [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)

