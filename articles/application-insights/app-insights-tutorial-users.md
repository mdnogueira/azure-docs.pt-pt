---
title: Compreender os seus clientes no Azure Application Insights | Microsoft Docs
description: "Tutorial sobre como utilizar o Azure Application Insights para compreender como os clientes estão a utilizar a aplicação."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: db61c300ad82270e59d315fa3372d9e4390c7a21
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Utilizar o Azure Application Insights para compreender como os clientes estão a utilizar a aplicação

Azure Application Insights recolhe informações de utilização para ajudar a compreender como os utilizadores interagem com a sua aplicação.  Este tutorial orienta-o através de diferentes recursos que estão disponíveis para analisar estas informações.  Ficará a saber como:

> [!div class="checklist"]
> * Analise os detalhes sobre os utilizadores que acedem a sua aplicação
> * Utilize as informações de sessão para analisar como os clientes utilizam a sua aplicação
> * Definir funnels permitem-lhe comparar a atividade do utilizador pretendida para as respetivas atividades real 
> * Criar um livro para consolidar as visualizações e consultas para um único documento
> * Grupo semelhantes aos utilizadores analisá-las em conjunto
> * Saiba quais os utilizadores são regressar à sua aplicação
> * Inspecione a forma como os utilizadores navegarem através da sua aplicação


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
- Transfira e instale o [depurador de instantâneo do Visual Studio](http://aka.ms/snapshotdebugger).
- Implementar uma aplicação .NET do Azure e [ativar o Application Insights SDK](app-insights-asp-net.md). 
- [Enviar a telemetria da sua aplicação](app-insights-usage-overview.md#send-telemetry-from-your-app) para adicionar vistas de página/eventos personalizados
- Enviar [contexto de utilizador](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context) para controlar o que faz um utilizador ao longo do tempo e utilizar totalmente as funcionalidades de utilização.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Obter informações sobre os seus utilizadores
O **utilizadores** painel permite-lhe compreender detalhes importantes sobre os seus utilizadores numa variedade de formas. Pode utilizar este painel para compreender informações como onde os utilizadores estão a ligar de detalhes do respetivo cliente e que áreas da sua aplicação está a aceder. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.
2. Selecione **utilizadores** no menu.
3. A vista predefinida mostra o número de utilizadores exclusivos que ligou-se a sua aplicação ao longo das últimas 24 horas.  Pode alterar a janela de tempo e definir vários outros critérios para filtrar estas informações.

    ![Construtor de consultas](media\app-insights-tutorial-users\QueryBuilder.png)

6. Clique em de **durante** pendente e altere a janela de tempo para 7 dias.  Isto aumenta os dados incluídos nos gráficos diferentes no painel.

    ![Intervalo de tempo de alteração](media\app-insights-tutorial-users\TimeRange.png)

4. Clique em de **dividir por** pendente para adicionar uma divisão por uma propriedade de utilizador ao gráfico.  Selecione **país ou região**.  O gráfico inclui os mesmos dados, mas permite-lhe ver uma divisão do número de utilizadores para cada país.

    ![Gráfico de país ou região](media\app-insights-tutorial-users\CountryorRegion.png)

5. Posicione o cursor sobre barras diferentes no gráfico e tenha em atenção que a contagem de cada país reflete apenas a janela de tempo representada por essa barra.
6. Veja o **Insights** coluna no lado direito que executar uma análise nos seus dados de utilizador.  Este artigo fornece informações como o número de sessões exclusivos ao longo do período de tempo e os registos com propriedades comuns que compõem significativas dos dados de utilizador 

    ![Coluna insights](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>Analisar as sessões de utilizador
O **sessões** painel é semelhante para o **utilizadores** painel.  Onde **utilizadores** ajuda-o a compreender os detalhes sobre os utilizadores aceder à sua aplicação, **sessões** ajuda-o a compreender como os utilizadores utilizaram a aplicação.  

1. Selecione **sessões** no menu.
2. Veja o gráfico e tenha em atenção que tem as mesmas opções para filtrar e dividir os dados como o **utilizadores** painel.

    ![Construtor de consultas de sessões](media\app-insights-tutorial-users\SessionsBuilder.png)

3. O **amostra destas sessões** painel à direita apresenta uma lista de sessões que incluem um grande número de eventos.  Estes são sessões interessantes para analisar.

    ![Exemplo destas sessões](media\app-insights-tutorial-users\SessionsSample.png)

4. Clique das sessões para ver o **linha cronológica de sessão**, que mostra cada ação nas sessões.  Isto pode ajudar a identificar informações tais como as sessões com um grande número de exceções.

    ![Linha cronológica de sessões](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Agrupar os utilizadores semelhantes
A **coorte** é um conjunto de utilizadores groupd em caraterísticas semelhantes.  Pode utilizar cohorts para os dados de filtro outros painéis, permitindo-lhe analisar determinados grupos de utilizadores.  Por exemplo, pode querer analisar apenas os utilizadores foi concluída uma compra.

1.  Selecione **Cohorts** no menu.
2.  Clique em **novo** para criar um novo coorte.
3.  Selecione o **quem utilizado** pendente e selecione uma ação.  Apenas os utilizadores que realizaram esta ação dentro da janela de tempo do relatório serão incluídos.

    ![Coorte que efetuou ações especificadas](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  Selecione **utilizadores** no menu.
5.  No **mostrar** lista pendente, selecione coorte que acabou de criar.  Os dados de gráfico estão limitados aos utilizadores.

    ![Coorte na ferramenta de utilizadores](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Comparar atividade pretendida para realidade
Enquanto os painéis anteriores são concentra-se em que os utilizadores da sua aplicação tiver, **Funnels** focar-se no que que pretende que os utilizadores para o fazer.  Um funil representa um conjunto de passos na sua aplicação e a percentagem de utilizadores que mover entre passos.  Por exemplo, pode criar um funil que avalia a percentagem de utilizadores que estabelecem ligação à sua aplicação pesquisar o produto.  Em seguida, pode ver a percentagem de utilizadores que adicionar produto ao carrinho de compras e, em seguida, a percentagem de quem concluir uma compra.

1. Selecione **Funnels** no menu e, em seguida, clique em **novo**. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. Escreva um **funil nome**.
3. Crie um funil com, pelo menos, dois passos, selecionando uma ação para cada passo.  A lista de ações é criada a partir dos dados de utilização recolhidos pelo Application Insights.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. Clique em **guardar** para guardar o funil e, em seguida, ver os resultados.  A janela para o direito de funil mostra os eventos mais comuns antes da primeira atividade e após a última atividade para o ajudar a compreender tendencies de utilizador à volta a sequência de específica.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>Saiba que clientes devolver
**Retenção** ajuda-o a compreender os utilizadores que são provenientes de volta à sua aplicação.  

1. Selecione **retenção** no menu.
2. Por predefinição, as informações analisadas incluem os utilizadores que efetuar qualquer ação e, em seguida, é devolvido para efetuar qualquer ação.  Pode alterar este filtro para qualquer incluir, por exemplo, apenas os utilizadores que devolveu depois de concluir uma compra.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. Os utilizadores devolvidos que correspondem aos critérios são apresentados no gráfico e tabela formulário para durações de hora diferente.  O padrão normal destina-se uma redução gradual na devolução de utilizadores ao longo do tempo.  Um drop repentino de um período de tempo para a seguinte poderá gerar uma preocupação. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analisar navegação do utilizador
A **fluxo de utilizador** visualiza como os utilizadores navegam entre as páginas e funcionalidades da aplicação.  Isto ajuda a responder a perguntas, tais como onde os utilizadores passam normalmente de uma página específica, como, normalmente, saia da sua aplicação, e se existem quaisquer ações que são repetidas regularmente.

1.  Selecione **fluxos utilizador** no menu.
2.  Clique em **novo** para criar um novo fluxo de utilizador e, em seguida, clique em **editar** para editar os respetivos detalhes.
3.  Aumentar a **intervalo de tempo** 7 dias e, em seguida, selecione um evento inicial.  O fluxo controlará sessões de utilizador que começam com esse evento.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  O fluxo de utilizador é apresentado, pode ver os caminhos de utilizador diferente e contagens da sessão.  Linhas azuis indicam uma ação que o utilizador efetuado após a ação atual.  Uma linha vermelha indica o fim da sessão de utilizador.

    ![](media\app-insights-tutorial-users\flows.png)

5.  Para remover um evento do fluxo, clique em de **x** no canto da ação e, em seguida, clique em **criar gráfico**.  O gráfico é redesenhado com quaisquer instâncias de que o evento removido.  Clique em **editar** para ver que o evento foi agora adicionado à **excluídos eventos**.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>Consolidar os dados de utilização
**Os livros** combinar o texto, as consultas de análises e visualizações de dados em documentos interativos.  Pode utilizar os livros para agrupar as informações de utilização comuns, consolidar informações a partir de um incidente específico ou informar a equipa na utilização da sua aplicação.

1.  Selecione **livros** no menu.
2.  Clique em **novo** para criar um novo livro.
3.  Uma consulta já foi fornecida o que inclui todos os dados de utilização no último dia apresentado como um gráfico de barras.  Pode utilizar esta consulta, manualmente editá-lo ou clique em **consultas de exemplo** para selecionar a partir de outras consultas útil.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  Clique em **terminar a edição**.
5.  Clique em **editar** no painel superior para editar o texto na parte superior do livro.  Isto está formatado com o markdown.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  Clique em **adicionar utilizadores** para adicionar um gráfico com as informações de utilizador.  Editar os detalhes do gráfico se pretender e, em seguida, clique em **terminar a edição** para guardá-lo.


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como analisar os seus utilizadores, avançar para o próximo tutorial para saber como criar dashboards personalizados que combinam estas informações com outros dados úteis sobre a sua aplicação.

> [!div class="nextstepaction"]
> [Crie dashboards personalizados](app-insights-tutorial-dashboards.md)
