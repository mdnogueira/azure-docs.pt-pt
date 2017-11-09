---
title: "Analisar e partilhar dados de utilização com os livros interativos no Azure Application Insights | Documentos da Microsoft"
description: "Análise demográficas dos utilizadores da sua aplicação web."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin
ms.openlocfilehash: 3edaefa942b834a070c55fe28daf60c74ea9f59d
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Analisar e partilhar dados de utilização com os livros interativos no Application Insights

Combinam os livros [Azure Application Insights](app-insights-overview.md) visualizações de dados, [as consultas de análises](app-insights-analytics.md)e o texto para documentos interativos. Os livros são editáveis por outros membros da equipa com acesso ao mesmo recurso do Azure. Isto significa que as consultas e controlos utilizados para criar um livro estão disponíveis para outras pessoas ao ler o livro, tornar mais fácil explorar, expandir e procurar prende.

Os livros são úteis para cenários, como:

* Explorar a utilização da sua aplicação quando não souber as métricas de interesse antecipadamente: número de utilizadores, taxas de retenção, taxas de conversão, etc. Ao contrário de outras ferramentas de análise de utilização no Application Insights, livros permitem-lhe combinar múltiplos tipos de visualizações e análises, torná-los excelente para este tipo de exploração de forma gratuita.
* Explicar a equipa da forma como está a efetuar uma funcionalidade recentemente publicada, por utilizador que mostra contagens de interações chaves e outras métricas.
* Partilha dos resultados de uma A / B experimentação na sua aplicação com outros membros da sua equipa. Pode explicar os objetivos para a experimentação com texto e mostrar cada consulta de análise utilizado para avaliar a experimentação, juntamente com outs de chamada encriptados para se cada métrica foi acima - ou abaixo-destino e métrica de utilização.
* O impacto de uma falha de relatórios sobre a utilização da sua aplicação, combinar dados, explicação de texto e ver um debate dos passos para evitar falhas no futuro.

> [!NOTE]
> O recurso do Application Insights tem de conter vistas de página ou eventos personalizados para utilizar os livros. [Saiba como configurar a sua aplicação para recolher as vistas de página automaticamente com o Application Insights SDK de JavaScript](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Editar, reorganização, a clonagem e eliminação de secções de livro

Um livro é um efetuadas das secções: visualizações de utilização de forma independente editável, gráficos, tabelas, texto ou os resultados da consulta de análise.

Para editar o conteúdo da secção de um livro, clique em de **editar** no botão abaixo e para a direita da secção de livro.

![Application Insights livros secção controlos de edição](./media/app-insights-usage-workbooks/editing-controls.png)

1. Quando tiver terminado a editar uma secção, clique em **Terminar edição** no canto inferior esquerdo da secção.

2. Para criar um duplicado de uma secção, clique o **clonar nesta secção** ícone. A criação de secções de duplicados é ótimo forma para iterar uma consulta sem perder iterações anteriores.

3. Para mover para cima uma secção num livro, clique em de **mover para cima** ou **mover para baixo** ícone.

4. Para remover uma secção permanentemente, clique o **remover** ícone.

## <a name="adding-usage-data-visualization-sections"></a>A adição de secções de visualização de dados de utilização

Os livros oferecem quatro tipos de visualizações de análise de utilização incorporada. Cada responde a uma pergunta comum sobre a utilização da sua aplicação. Para adicionar tabelas e gráficos além destas quatro secções, adicione secções de consulta de análise (ver abaixo).

Para adicionar os utilizadores, sessões, eventos ou retenção secção ao seu livro, utilize o **adicionar utilizadores** ou outro botão correspondente na parte inferior do livro ou na parte inferior de qualquer secção.

![Seção de utilizadores nos livros](./media/app-insights-usage-workbooks/users-section.png)

**Os utilizadores** secções responder "quantos utilizadores visualizar algumas página ou utilizada alguma funcionalidade do meu site?"

**Sessões** secções responder "quantos sessões utilizadores gastam a ver alguns página ou utilizar algumas funcionalidade do meu site?"

**Eventos** secções responder "quantas vezes aos utilizadores visualizar algumas página ou utilizar algumas funcionalidade do meu site?"

Cada um destes tipos de três secção oferece os conjuntos de controlos e visualizações mesmos:

* [Saiba mais sobre a edição de secções de utilizadores, sessões e os eventos](app-insights-usage-segmentation.md)
* Alternar o gráfico principal, grelhas de histograma, insights automáticas e visualizações de utilizadores de exemplo utilizando o **Mostrar gráfico**, **grelha mostrar**, **Mostrar Insights**, e **exemplo estes utilizadores** caixas de verificação na parte superior de cada secção.

![Secção de retenção nos livros](./media/app-insights-usage-workbooks/retention-section.png)

**Retenção** secções responder "De pessoas que visualizadas algumas página ou numa alguma funcionalidade, um dia ou semana, quantos veio novamente num dia subsequente ou semana?"

* [Saiba mais sobre a edição de secções de retenção](app-insights-usage-retention.md)
* Ativar/desativar a utilização de gráfico de retenção global opcional a **gráfico de retenção geral Show** caixa de verificação na parte superior da secção.

## <a name="adding-application-insights-analytics-sections"></a>Adicionar Application Insights Analytics secções

![Secção de análise nos livros](./media/app-insights-usage-workbooks/analytics-section.png)

Para adicionar uma secção de consulta do Application Insights Analytics ao seu livro, utilize o **consulta de análise adicionar** botão na parte inferior do livro ou na parte inferior de qualquer secção.

Secções de consulta de análise permitem-lhe adicionar consultas arbitrários sobre os dados do Application Insights para livros. Esta flexibilidade significa secções de consulta de análise devem ser a aceda-a para a resposta a quaisquer perguntas sobre o seu site diferente quatro listado acima para os utilizadores, sessões, eventos e retenção, como:

* Como muitas exceções acionar o site durante o período de tempo mesmo como um recusar em utilização?
* Qual era a distribuição de tempos de carregamento de página para os utilizadores visualizar algumas página?
* Quantos utilizadores visualizar algumas conjunto de páginas no seu site, mas não algum outro conjunto de páginas? Isto pode ser útil para entender se tiver de clusters de utilizadores que utilizam diferentes subconjuntos de funcionalidade do site (utilizar o `join` operador com o `kind=leftanti` modificador na linguagem de consulta de análise de registos).

Utilize o [referência de linguagem de consulta de análise de registos](https://docs.loganalytics.io/) para obter mais informações sobre como escrever consultas.

## <a name="adding-text-and-markdown-sections"></a>A adição de texto e secções de Markdown

A adição de cabeçalhos, explicações e interrompa para os livros ajuda a ativar um conjunto de tabelas e gráficos para uma narrativa. Secções de texto no suporte de livros o [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) para formatação, como a negrito, cabeçalhos, italics e apresenta uma lista com marcas de texto.

Para adicionar uma secção de texto ao seu livro, utilize o **adicionar texto** botão na parte inferior do livro ou na parte inferior de qualquer secção.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Guardar e partilhar livros com a sua equipa

Livros são guardados dentro de um recurso do Application Insights, encontra-se no **os meus relatórios** secção que é privada ou no **partilhado relatórios** secção que se encontra acessível para todos os utilizadores com acesso ao recurso do Application Insights. Para ver todos os livros no recurso, clique o **abra** botão na barra de ação.

Para partilhar um livro está atualmente no **os meus relatórios**:

1. Clique em **abra** na barra de ação
2. Clique no botão "..." ao lado do livro que pretende partilhar
3. Clique em **mover para relatórios partilhados**.

Para partilhar um livro com uma ligação ou através de e-mail, clique em **partilhar** na barra de ação. Tenha em atenção que os destinatários da ligação necessitam de aceder a este recurso no portal do Azure para ver o livro. Para tornar as edições, os destinatários precisam de, pelo menos, permissões de contribuinte do recurso.

Para afixar uma hiperligação para um livro para um Dashboard do Azure:

1. Clique em **abra** na barra de ação
2. Clique no botão "..." ao lado do livro que pretende afixar
3. Clique em **afixar ao dashboard**.

## <a name="next-steps"></a>Passos seguintes

## <a name="next-steps"></a>Passos seguintes
- Para ativar as experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o serviço.
    - [Utilizadores, Sessões, Eventos](app-insights-usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Retenção](app-insights-usage-retention.md)
    - [Fluxos do Utilizador](app-insights-usage-flows.md)
    - [Adicionar o contexto de utilizador](app-insights-usage-send-user-context.md)
    
