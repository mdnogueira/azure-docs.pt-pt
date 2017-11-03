---
title: "Analisar as estatísticas de utilização com a CDN do Azure avançadas de relatórios HTTP | Microsoft Docs"
description: "Saiba como criar relatórios avançados de HTTP na CDN do Microsoft Azure. Estes relatórios fornecem informações detalhadas na atividade CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 2dfbc046674b2da692f30c945aee3ea25ae524eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analisar as estatísticas de utilização com a CDN do Azure avançadas de relatórios HTTP
## <a name="overview"></a>Descrição geral
Este documento explica avançadas HTTP relatórios do Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas na atividade CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Ao aceder aos relatórios avançados de HTTP
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
2. Coloque o cursor sobre o **análise** separador, em seguida, coloque o cursor sobre o **relatórios avançados de HTTP** flyout.  Clique em **plataforma grande HTTP**.
   
    ![Portal de gestão de CDN - menu de relatórios avançadas](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Opções de relatórios são apresentadas.

## <a name="geography-reports-map-based"></a>Relatórios de geografia (baseado em mapa)
Existem cinco relatórios que tirar partido de um mapa para indicar as regiões a partir do qual o seu conteúdo está a ser pedido. Estes relatórios são mapa do mundo, mapa dos Estados Unidos, Canadá mapa, mapa Europa e Ásia Pacífico mapa.

Cada relatório baseado no mapa ranks geográfica entidades (ou seja, países, Estados e provinces), de acordo com a percentagem de pedidos que teve origem nessa região. Além disso, é fornecido um mapa para o ajudar a visualizar as localizações a partir do qual o seu conteúdo está a ser pedido. É possível fazê-lo por color-coding cada região, de acordo com a quantidade de procura experiente nessa região. Lighter sombreadas regiões indicam a pedido inferior para o conteúdo, enquanto regiões darker indicam níveis superiores de procura para o seu conteúdo.

As informações detalhadas sobre o tráfego e largura de banda para cada região são fornecidas diretamente abaixo do mapa. Isto permite-lhe ver o número total de pedidos com êxito, a percentagem de pedidos com êxito, a quantidade total de dados transferidos (em gigabytes) e a percentagem dos dados transferidos para cada região. Ver uma descrição para cada um destas métricas. Por fim, quando paira o rato sobre uma região (ou seja, país, estado ou distrito), o nome e a percentagem de pedidos que ocorreram na região serão apresentados como descrição.

Uma breve descrição é indicada abaixo, para cada tipo de relatório de geografia com base no mapa.

| Nome do relatório | Descrição |
| --- | --- |
| Mapa do mundo |Este relatório permite ver a procura em todo o mundo, para o conteúdo do CDN. Cada país é codificado por cores no mapa do mundo, para indicar a percentagem de pedidos que teve origem nessa região. |
| Mapa de Estados Unidos |Este relatório permite ver o pedido para o conteúdo do CDN nos Estados Unidos. Cada Estado é codificado por cores deste mapa para indicar a percentagem de pedidos que teve origem nessa região. |
| Mapa do Canadá |Este relatório permite-lhe ver o pedido para o conteúdo do CDN no Canadá. Cada distrito é codificado por cores deste mapa para indicar a percentagem de pedidos que teve origem nessa região. |
| Europa mapa |Este relatório permite ver o pedido para o conteúdo do CDN na Europa. Cada país é codificado por cores deste mapa para indicar a percentagem de pedidos que teve origem nessa região. |
| Mapa Pacífico Ásia |Este relatório permite ver o pedido para o conteúdo do CDN na Ásia. Cada país é codificado por cores deste mapa para indicar a percentagem de pedidos que teve origem nessa região. |

## <a name="geography-reports-bar-charts"></a>Relatórios de geografia (gráficos de barras)
Existem dois adicionais relatórios que fornecem informações estatísticas, de acordo com geografia, que são cidades principais e de países da parte superior. Estes relatórios classificar que cidades e países, respetivamente, de acordo com o número de pedidos que teve origem essas regiões. Após a gerar este tipo de relatório, um gráfico de barras indicará o 10 cidades principais ou que países pedida conteúdo através de uma plataforma específica. Este gráfico de barras permite-lhe avaliar rapidamente as regiões que geram o maior número de pedidos para o seu conteúdo.

O lado esquerdo do gráfico (eixo y) indica quantos pedidos ocorreu na região especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta para cada uma das principais 10 regiões.

### <a name="using-the-bar-charts"></a>Utilizar os gráficos de barras
* Se paira o rato sobre uma barra, o nome e o número total de pedidos que ocorreram na região serão apresentados como descrição.
* A descrição para o relatório de topo cidades identifica uma cidade pelo respetivo nome, distrito e abreviatura de país.
* Se não foi possível determinar o cidade ou a região (ou seja, distrito) a partir do qual um pedido teve origem, irá indicar que são desconhecidos. Se o país/região é desconhecido, em seguida, duas marcas de pergunta (ou seja,??), será apresentada.
* Um relatório pode incluir as métricas para "Europa" ou "Região Ásia/Pacífico". Os itens não se destinam a fornecer informações estatísticas sobre todos os endereços IP nessas regiões. Em vez disso, estes só se aplicam a pedidos provenientes de endereços IP que são distribuídos por Europa ou Ásia/Pacífico em vez de uma cidade específica ou o país.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existe irá encontrar o número total de pedidos com êxito, a percentagem de pedidos com êxito, a quantidade de dados transferidos (em gigabytes) e a percentagem dos dados transferidos para as regiões de topo 250. Ver uma descrição para cada um destas métricas.

É apresentada uma descrição breve para ambos os tipos de relatórios abaixo.

| Nome do relatório | Descrição |
| --- | --- |
| Parte superior cidades |Este relatório ranks cidades, de acordo com o número de pedidos que teve origem nessa região. |
| Países superiores |Este relatório ranks países, de acordo com o número de pedidos que teve origem nessa região. |

## <a name="daily-summary"></a>Resumo diário
O relatório de resumo diária permite-lhe ver o número total de pedidos e os dados transferidos através de uma plataforma específica numa base diária. Estas informações podem ser utilizadas para rapidamente discernir padrões de atividade do CDN. Por exemplo, este relatório pode ajudá-lo a detetar os dias experiente superior ou inferiores ao tráfego esperado.

Após a gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual a quantidade de procura específicos da plataforma experiente diariamente durante o período de tempo abrangido pelo relatório. Executará, pelo que, ao apresentar uma barra para cada dia no relatório. Por exemplo, selecionar o período de tempo chamado "Última semana" irá gerar um gráfico de barras com sete barras. Cada barra irá indicar o número total de pedidos experiente nesse dia.

O lado esquerdo do gráfico (eixo y) indica quantos pedidos Ocorreu a data especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data (formato: aaaa-MM-DD) para cada dia incluído no relatório.

> [!TIP]
> Se paira o rato sobre uma barra, o número total de pedidos que ocorreu a essa data será apresentado como descrição.
> 
> 

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existe irá encontrar o número total de pedidos com êxito e a quantidade de dados transferidos (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-hour"></a>Por hora
O relatório por hora permite-lhe ver o número total de pedidos e os dados transferidos através de uma plataforma específica numa base horária. Estas informações podem ser utilizadas para rapidamente discernir padrões de atividade do CDN. Por exemplo, este relatório pode ajudá-lo a detetar os períodos de tempo durante o dia experiência superior ou inferior ao tráfego esperado.

Após a gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual a quantidade de procura específicos da plataforma ocorrida numa base horária, o período de tempo abrangido pelo relatório. Será efetuado, pelo que, ao apresentar uma barra para cada hora abrangida pelo relatório. Por exemplo, selecionar uma 24 horas período de tempo irá gerar um gráfico de barras com vinte e quatro barras. Cada barra irá indicar o número total de pedidos experiente durante essa hora.

O lado esquerdo do gráfico (eixo y) indica que ocorreu o número de pedidos na hora especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data/hora (formato: aaaa-MM-DD HH) para cada hora incluída no relatório. Tempo comunicado utilizando o formato de 24 horas e for especificado com o fuso horário UTC/GMT.

> [!TIP]
> Se paira o rato sobre uma barra, o número total de pedidos que ocorreram durante essa hora será apresentado como descrição.
> 
> 

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existe irá encontrar o número total de pedidos com êxito e a quantidade de dados transferidos (em gigabytes) para cada hora abrangida pelo relatório.

## <a name="by-file"></a>Por ficheiro
O relatório por ficheiro permite-lhe ver a quantidade de pedido e o tráfego tarifas através de uma plataforma específica para os recursos mais solicitados. Após a gerar este tipo de relatório, será gerado um gráfico de barras em 10 principais recursos mais solicitados durante o período de tempo especificado.

> [!NOTE]
> Para efeitos deste relatório, limite CNAME URLs são convertidos para os respetivos equivalentes os URLs da CDN. Isto permite que um contabilização precisa para o número total de pedidos associados a um recurso, independentemente do CDN ou edge URL de CNAME utilizado para solicitá-la.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o número de pedidos para cada recurso durante o período de tempo especificado. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica o nome de ficheiro para cada uma das 10 principais recursos pedidos.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existe, encontrará as seguintes informações para cada um dos principais 250 ativos pedidos: o caminho relativo, o número total de pedidos com êxito, a percentagem de pedidos com êxito, a quantidade de dados transferidos (em gigabytes) e a percentagem dos dados transferidos.

## <a name="by-file-detail"></a>Por detalhes do ficheiro
O relatório de detalhes do ficheiro por permite-lhe ver a quantidade de pedido e o tráfego tarifas através de uma plataforma específica para um recurso específico. Na parte superior muito deste relatório é a opção de detalhes de ficheiro para. Esta opção fornece uma lista dos seus ativos mais solicitados na plataforma. Para gerar um relatório de detalhes do ficheiro por, terá de selecionar o elemento pretendido da opção de detalhes de ficheiro para. Após o qual, um gráfico de barras indicará a quantidade de procura diária que gerou durante o período de tempo especificado.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos que um recurso teve num dia específico. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data (formato: aaaa-MM-DD) para o CDN foi comunicado um pedido para o elemento.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existe irá encontrar o número total de pedidos com êxito e a quantidade de dados transferidos (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-file-type"></a>Por tipo de ficheiro
O relatório por tipo de ficheiro permite-lhe ver a quantidade de pedido e o tráfego tarifas de tipo de ficheiro. Após a gerar este tipo de relatório, um gráfico de anel indica a percentagem de pedidos, gerados pelos principais tipos de 10 ficheiro.

> [!TIP]
> Se paira o rato sobre um setor do gráfico de anel, tipo de suporte de dados de Internet de que tipo de ficheiro será apresentado como descrição.
> 
> 

Os dados que foi utilizados para gerar o gráfico de anel podem ser visualizados abaixo do mesmo. Não existe irá encontrar o tipo de suporte de Internet/extensão de nome de ficheiro, o número total de pedidos com êxito, a percentagem de pedidos, a quantidade de dados transferidos (em gigabytes) e a percentagem dos dados transferidos para cada um dos tipos de ficheiro superior 250.

## <a name="by-directory"></a>Por diretório
O relatório por diretório permite-lhe ver a quantidade de pedido e o tráfego tarifas através de uma plataforma específica para conteúdo a partir de um diretório específico. Após a gerar este tipo de relatório, um gráfico de barras irá indicar o número total de pedidos, gerados pelo conteúdo nos 10 diretórios principais.

### <a name="using-the-bar-chart"></a>Utilizar o gráfico de barras
* Coloque o cursor sobre uma barra para ver o caminho relativo para o diretório correspondente.
* Conteúdo armazenado numa subpasta de um diretório não contagem quando se calcular a pedido pelo diretório. Este cálculo baseia-se exclusivamente com o número de pedidos gerado para o conteúdo armazenado no diretório atual.
* Para efeitos deste relatório, limite CNAME URLs são convertidos para os respetivos equivalentes os URLs da CDN. Isto permite que um contabilização precisa para todas as estatísticas associado um recurso, independentemente do CDN ou edge URL de CNAME utilizado para solicitá-la.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos para os conteúdos armazenados nos seus diretórios 10 principais. Cada barra no gráfico representa um diretório. Utilize o esquema color-coding para corresponde a uma barra de para um diretório listada na secção de topo 250 completa diretórios.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existe, encontrará as seguintes informações para cada um dos diretórios de topo 250: o caminho relativo, o número total de pedidos com êxito, a percentagem de pedidos com êxito, a quantidade de dados transferidos (em gigabytes) e a percentagem dos dados transferidos.

## <a name="by-browser"></a>Pelo Browser
O relatório pelo Browser permite-lhe ver quais os browsers foram utilizados para pedidos de conteúdo. Após a gerar este tipo de relatório, um gráfico circular indica a percentagem de pedidos processados pelos principais 10 browsers.

### <a name="using-the-pie-chart"></a>Utilizar o gráfico circular
* Coloque o cursor sobre um setor do gráfico circular para ver o nome e a versão de um browser.
* Para efeitos deste relatório, cada combinação de exclusivo/versão do browser é considerada um browser diferente.
* O setor chamado "Outros" indica a percentagem de pedidos processados por todos os outros browsers e versões.

Os dados que foi utilizados para gerar o gráfico circular podem ser visualizados abaixo do mesmo. Não existe irá encontrar o número de versão do tipo de browser, o número total de pedidos com êxito e a percentagem de pedidos com êxito para cada uma dos browsers principais 250.

## <a name="by-referrer"></a>Por referência
O relatório por referência permite-lhe ver os referrers superiores para o conteúdo na plataforma. Uma referência indica o nome de anfitrião a partir da qual foi gerado a um pedido. Após a gerar este tipo de relatório, um gráfico de barras indicará a quantidade de procura (ou seja, pedidos) gerada pelos 10 referrers principais.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos que um recurso teve para cada referência. Cada barra no gráfico representa uma referência. Utilize o esquema color-coding para corresponde a uma barra para uma referência listada na secção de referência de 250 da parte superior.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existe irá encontrar o URL, o número total de pedidos com êxito e a percentagem de pedidos gerado a partir de cada um dos 250 referrers superiores.

## <a name="by-download"></a>Por transferência
O relatório por transferir permite-lhe analisar padrões de transferência para o conteúdo mais solicitado. Na parte superior do relatório contém um gráfico de barras que compara tentadas transferências com transferências concluídas para os recursos de pedido 10 principais. Cada barra é codificado por cores, de acordo com se trata de uma tentativa de transferência (azul) ou uma transferência foi concluída (verde).

> [!NOTE]
> Para efeitos deste relatório, limite CNAME URLs são convertidos para os respetivos equivalentes os URLs da CDN. Isto permite que um contabilização precisa para todas as estatísticas associado um recurso, independentemente do CDN ou edge URL de CNAME utilizado para solicitá-la.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o nome de ficheiro para cada uma das 10 principais recursos pedidos. Diretamente abaixo do gráfico (eixo x), irá encontrar rótulos que indicam o número total de transferências tentada/concluída.

Diretamente abaixo o gráfico de barras, as informações seguintes serão apresentadas para 250 principais recursos pedidos: o caminho relativo (incluindo o nome de ficheiro), o número de vezes que este foi transferido para conclusão, o número de vezes que foi pedido e a percentagem de pedidos que resultaram numa transferência completa.

> [!TIP]
> A nossa CDN não é informado por um cliente HTTP (ou seja, o browser) quando um recurso tenha sido transferido por completo. Como resultado, temos de calcular se um recurso tenha sido transferido completamente, de acordo com os códigos de estado e o intervalo de bytes pedidos. A primeira coisa que podemos procurar quando efetuar este cálculo é se o pedido resulta num código de estado OK 200. Se assim for, em seguida, vamos ver pedidos de intervalo de bytes para se certificar de que abrangem o recurso de todo. Por último, iremos comparar a quantidade de dados transferidos para o tamanho do elemento pedido. Se os dados transferidos são igual ou superior ao tamanho do ficheiro e os pedidos de intervalo de bytes são adequados para esse recurso, o acessos serão contados como uma transferência completa.
> 
> Devido à natureza interpretive deste relatório, deve ter em consideração os seguintes pontos que podem alterar a consistência e a precisão do relatório.
> 
> * Padrões de tráfego não não possível prever com exatidão quando os agentes de utilizador comportar-se de forma diferente. Isto poderá produzir resultados da transferência foi concluída que são maiores que 100%.
> * Não podem ser representados com precisão ativos tirar partido de transferência progressiva do HTTP neste relatório. Isto acontece devido a utilizadores de pesquisa para diferentes posições um vídeo.
> 
> 

## <a name="by-404-errors"></a>Por 404 erros
O relatório de erros de 404, permite-lhe identificar o tipo de conteúdo que gera o maior número de 404 códigos de estado não foi encontrado. Na parte superior do relatório contém um gráfico de barras para os recursos de 10 principais para o qual foi devolvido um código de estado não foi encontrado 404. Este gráfico de barras compara o número total de pedidos com pedidos que resultaram num 404 não encontrado código de estado para esses ativos. Cada barra é codificado por cores. Uma barra de amarela é utilizada para indicar que o pedido resultou num código de estado não foi encontrado 404. Uma barra vermelha é utilizada para indicar o número total de pedidos para o elemento.

> [!NOTE]
> Para efeitos deste relatório, tenha em atenção o seguinte:
> 
> * Um acessos representa qualquer pedido para um recurso, independentemente do código de estado.
> * Limite CNAME URLs são convertidos para os respetivos equivalentes os URLs da CDN. Isto permite que um contabilização precisa para todas as estatísticas associado um recurso, independentemente do CDN ou edge URL de CNAME utilizado para solicitá-la.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o nome de ficheiro para cada um dos principais 10 ativos solicitados que resultaram num código de estado não foi encontrado 404. Diretamente abaixo do gráfico (eixo x), irá encontrar rótulos que indicam o número total de pedidos e o número de pedidos que resultaram num código de estado não foi encontrado 404.

Diretamente abaixo o gráfico de barras, as informações seguintes serão apresentadas para 250 principais recursos pedidos: o caminho relativo (incluindo nome de ficheiro), o número de pedidos que resultaram num 404 não encontrado código de estado, o número total de vezes que o recurso foi pedido, e a percentagem de pedidos que resultaram num código de estado não foi encontrado 404.

## <a name="see-also"></a>Consultar também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real na CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)
* [Analisar o desempenho de limite](cdn-edge-performance.md)

