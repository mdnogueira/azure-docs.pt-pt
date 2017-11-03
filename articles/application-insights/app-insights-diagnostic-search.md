---
title: Utilizando a pesquisa no Azure Application Insights | Microsoft Docs
description: "Procurar e filtrar telemetria não processados enviada pela sua aplicação web."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: ce2fd9ed1ce796762cc15622cb1c59a316c1909d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="using-search-in-application-insights"></a>Utilizando a pesquisa no Application Insights
Pesquisa é uma funcionalidade do [Application Insights](app-insights-overview.md) que utilizar para localizar e explorar os itens de telemetria individuais, tais como vistas de página, exceções ou pedidos da web. E pode ver os rastreios de registo e eventos que tenham codificado.

(Para consultas mais complexas sobre os seus dados, utilize [análise](app-insights-analytics-tour.md).)

## <a name="where-do-you-see-search"></a>Em que verá pesquisa?
### <a name="in-the-azure-portal"></a>No portal do Azure
Pode abrir explicitamente a pesquisa de diagnóstico a partir do painel de descrição geral do Application Insights da aplicação:

![Abra pesquisa de diagnóstico](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Abre-se também ao clicar em através de alguns gráficos e itens de grelha. Neste caso, os respetivo filtros previamente estão definidos como focar-se no tipo de item selecionado. 

Por exemplo, no painel de descrição geral, não há um gráfico de barras de pedidos classificados por tempo de resposta. Clique em através de um intervalo de desempenho para ver uma lista de pedidos individuais nesse intervalo de tempo de resposta:

![Clicar no desempenho de pedido](./media/app-insights-diagnostic-search/07-open-from-filters.png)

O corpo principal da pesquisa de diagnóstico é uma lista de itens de telemetria - pedidos de servidor, página vistas, eventos personalizados que tenham coded e assim sucessivamente. Na parte superior da lista é um gráfico de resumo que mostra o número de eventos ao longo do tempo.

Clique em Atualizar para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, há também uma janela de pesquisa do Application Insights. É mais útil para apresentar eventos de telemetria gerados pela aplicação que está a depurar. Mas também pode mostrar os eventos recolhidos a partir da sua aplicação publicada no portal do Azure.

Abra a janela de pesquisa no Visual Studio:

![Abra o Visual Studio Application Insights procura](./media/app-insights-diagnostic-search/32.png)

A janela de pesquisa tem funcionalidades semelhantes para o portal web:

![Janela de pesquisa do Visual Studio Application Insights](./media/app-insights-diagnostic-search/34.png)

O separador de operação de controlar está disponível quando abre um pedido ou numa vista de página. Um ' operação ' é uma sequência de eventos que está associada a uma única vista de página ou a pedido. Por exemplo, chamadas de dependência, exceções, registos de rastreio e eventos personalizados podem fazer parte de uma única operação. O separador de operação de controlar graficamente mostra a temporização e a duração destes eventos em relação a vista de página ou a pedido. 

## <a name="inspect-individual-items"></a>Inspecione os itens individuais
Seleccione qualquer item de telemetria para ver os campos de chave e a itens relacionados. Se pretender ver o conjunto completo de campos, clique em "...". 

![Clique em Novo Item de trabalho, edite os campos e, em seguida, clique em OK.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>Tipos de evento de filtro
Abra o painel de filtro e escolha os tipos de eventos que pretende ver. (Se, posteriormente, pretender restaurar os filtros que tiver aberto o painel, clique em Repor.)

![Escolha de filtro e seleciona os tipos de telemetria](./media/app-insights-diagnostic-search/02-filter-req.png)

Os tipos de evento são:

* **Rastreio** - [registos de diagnóstico](app-insights-asp-net-trace-logs.md) incluindo TrackTrace, log4Net, NLog e System.Diagnostic.Trace chamadas.
* **Pedir** -pedidos HTTP recebidos pela aplicação de servidor, incluindo as páginas, scripts, imagens, ficheiros de estilo e dados. Estes eventos são utilizados para criar o pedido e resposta gráficos descrição geral.
* **Vista de página** - [a telemetria enviada pelo cliente web](app-insights-javascript.md), utilizada para criar relatórios de vista de página. 
* **Evento personalizado** - se inserir chamadas de trackevent () para [monitorizar a utilização de](app-insights-api-custom-events-metrics.md), pode procurar aqui.
* **Exceção** - não identificadas [exceções no servidor](app-insights-asp-net-exceptions.md)e os que inicia sessão utilizando trackexception ().
* **Dependência** - [chamadas a partir da sua aplicação de servidor](app-insights-asp-net-dependencies.md) a outros serviços, tais como as APIs REST ou bases de dados e AJAX chamadas a partir da sua [código de cliente](app-insights-javascript.md).
* **Disponibilidade** -resultados da [testes de disponibilidade](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrar valores de propriedade
Pode filtrar eventos nos valores das respetivas propriedades. As propriedades disponíveis dependem os tipos de evento que selecionou. 

Por exemplo, escolha os pedidos com um código de resposta específica. 

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/03-response500.png)

Escolher não existem valores de uma propriedade específica tem o mesmo efeito que escolher todos os valores. Muda a filtragem nessa propriedade.

### <a name="narrow-your-search"></a>Limite a pesquisa
Tenha em atenção que o número de à direita dos valores de filtro mostra são quantos ocorrências não existe no conjunto filtrado atual. 

Neste exemplo, é claro que 'Rpt/funcionários' pedido resulta na maioria dos erros '500':

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>Localizar os eventos com a mesma propriedade
Localize todos os itens com o mesmo valor de propriedade:

![Uma propriedade com o botão direito](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>Procurar os dados

> [!NOTE]
> Para escrever consultas mais complexas, abra [ **análise** ](app-insights-analytics-tour.md) da parte superior do painel pesquisa.
> 

Pode procurar termos em qualquer um dos valores de propriedade. Isto é particularmente útil se tiver a escrita [eventos personalizados](app-insights-api-custom-events-metrics.md) com valores de propriedade. 

Pode querer definir uma hora de intervalo, como pesquisas através de um período mais curto são de mais rapidamente. 

![Abra pesquisa de diagnóstico](./media/app-insights-diagnostic-search/appinsights-311search.png)

Procurar palavras concluídas, não subcadeias. Utilize aspas incluir carateres especiais.

| Cadeia | é *não* encontrado por | mas estes encontrá-lo |
| --- | --- | --- |
| HomeController.About |Home page<br/>Controlador<br/>Saída | homecontroller<br/>sobre<br/>"homecontroller.about"|
|Estados Unidos|Sinalização UNI<br/>TED|Unidas<br/>Estados<br/>Unidas e Estados<br/>"dos Estados Unidos"

Seguem-se as expressões de pesquisa, que pode utilizar:

| Consulta de exemplo | Efeito |
| --- | --- |
| `apple` |Localizar todos os eventos no intervalo de tempo cujos campos incluem a palavra "apple" |
| `apple AND banana` |Localize os eventos que contêm ambas as palavras. Utilizar capital "e", não "e". |
| `apple OR banana`<br/>`apple banana` |Localize os eventos que contêm o word. Utilize "Ou" não "ou".<br/>Ditada abreviada. |
| `apple NOT banana` |Localize os eventos que contêm uma palavra, mas não o outro. |



## <a name="sampling"></a>Amostragem
Se a sua aplicação gerar uma grande quantidade de telemetria (e estiver a utilizar o ASP.NET SDK versão 2.0.0-Beta3 ou posterior), o módulo de amostragem adaptável reduz automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. No entanto, os eventos relacionados com o mesmo pedido são selecionados ou desmarcados como um grupo, para que possa navegar entre os eventos relacionados. 

[Saiba mais sobre a amostragem](app-insights-sampling.md).



## <a name="create-work-item"></a>Criar item de trabalho
Pode criar um erro no GitHub ou Visual Studio Team Services com os detalhes de qualquer item de telemetria. 

![Clique em Novo Item de trabalho, edite os campos e, em seguida, clique em OK.](./media/app-insights-diagnostic-search/42.png)

A primeira que fazê-lo, é-lhe pedido para configurar uma ligação à sua conta de serviços da equipa e o projeto.

![Preencher o URL do seu servidor de serviços da equipa e o nome do projeto e clique em autorizar](./media/app-insights-diagnostic-search/41.png)

(Também pode configurar a ligação no painel de itens de trabalho.)

## <a name="save-your-search"></a>Guardar a pesquisa
Quando tiver configurado a todos os filtros que pretende, pode guardar a pesquisa como um favorito. Se funcionar numa conta organizacional, pode optar por partilhar com outros membros da equipa.

![Clique favorito, defina o nome e clique em Guardar](./media/app-insights-diagnostic-search/08-favorite-save.png)

Para ver a pesquisa novamente, **aceda ao painel de descrição geral** e abra favoritos:

![Mosaico de Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Se tiver guardado com o intervalo de tempo relativo, o painel novamente aberto tem os dados mais recentes. Se tiver guardado com o intervalo de tempo absoluto, verá os mesmos dados sempre. (Se 'Relativo' não está disponível quando pretender guardar um favorito, clique o intervalo de tempo no cabeçalho e definir um intervalo de tempo que não é um intervalo personalizado).

## <a name="send-more-telemetry-to-application-insights"></a>Enviar telemetria mais ao Application Insights
Para além de telemetria de out of box enviada pelo Application Insights SDK, pode:

* Capturar rastreios de registo da sua arquitetura de registo favorita na [.NET](app-insights-asp-net-trace-logs.md) ou [Java](app-insights-java-trace-logs.md). Isto significa que pode procurar rastreios de registo e correlacioná-los com outros eventos, exceções e vistas de página. 
* [Escrever código](app-insights-api-custom-events-metrics.md) enviar exceções, vistas de página e eventos personalizados. 

[Saiba como enviar telemetria personalizada e os registos ao Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>AS PERGUNTAS E RESPOSTAS
### <a name="limits"></a>Quantidade de dados é mantida?

Consulte o [resumo limites](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como ver dados reg nos meus pedidos de servidor?
Iremos não iniciar sessão automaticamente os dados POST, mas pode utilizar [chamadas de registo ou TrackTrace](app-insights-asp-net-trace-logs.md). Coloque os dados POST no parâmetro mensagem. Não é possível filtrar a mensagem da mesma forma, que pode filtrar por propriedades, mas ultrapassa o limite de tamanho.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Passos seguintes
* [Escrever consultas complexas na análise](app-insights-analytics-tour.md)
* [Enviar telemetria personalizada e os registos ao Application Insights](app-insights-asp-net-trace-logs.md)
* [Configurar a disponibilidade e testes de capacidade de resposta](app-insights-monitor-web-app-availability.md)
* [Resolução de problemas](app-insights-troubleshoot-faq.md)
