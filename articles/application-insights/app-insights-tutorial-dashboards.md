---
title: Crie dashboards personalizados no Azure Application Insights | Microsoft Docs
description: Tutorial para criar dashboards personalizados do KPI com o Azure Application Insights.
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 0d2f98ca2fb39289b2916ddd24590924856507d6
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Crie dashboards personalizados do KPI com o Azure Application Insights

Pode criar dashboards vários no portal do Azure que cada mosaicos incluir visualizar dados de vários recursos do Azure entre subscrições e grupos de recursos diferentes.  Pode afixar gráficos diferentes e vistas a partir da Azure Application Insights para criar dashboards personalizados que fornecem visão geral do Estado de funcionamento e desempenho da aplicação.  Este tutorial orienta-o através da criação de um dashboard personalizado que inclua vários tipos de dados e as visualizações do Azure Application Insights.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard personalizado no Azure
> * Adicione um mosaico da galeria do mosaico
> * Adicionar métricas padrão no Application Insights ao dashboard 
> * Adicionar um gráfico de métrico personalizado Application Insights ao dashboard
> * Adicionar os resultados de uma consulta de análise para o dashboard 



## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Implementar uma aplicação .NET do Azure e [ativar o Application Insights SDK](app-insights-asp-net.md). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard
Um único dashboard pode conter recursos a partir de várias aplicações, grupos de recursos e subscrições.  O tutorial de início ao criar um novo dashboard para a sua aplicação.  

2.  No ecrã principal do portal, selecione **novo dashboard**.

    ![Novo dashboard](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Escreva um nome para o dashboard.
4. Veja o **mosaico galeria** para uma variedade de mosaicos que pode adicionar ao dashboard.  Para além de adicionar peças de mosaicos a partir da galeria pode afixar gráficos e a outras vistas diretamente a partir do Application Insights ao dashboard.
5. Localize o **Markdown** mosaico e arraste-o para o dashboard.  Este mosaico permite-lhe adicionar texto formatado no markdown que é ideal para adicionar o texto descritivo para o dashboard.
6. Adicionar texto a propriedades no mosaico e redimensioná-la na tela dashboard.
    
    ![Editar o mosaico de markdown](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. Clique em **feito personalizar** na parte superior do ecrã, para sair do modo de personalização do mosaico e, em seguida, **publicar alterações** para guardar as alterações.

    ![Dashboard com mosaico de markdown](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Adicionar descrição geral do Estado de funcionamento
Um dashboard com texto apenas estático não está muito interessante, por isso, agora, adicione um mosaico do Application Insights para mostrar as informações sobre a sua aplicação.  Pode adicionar mosaicos do Application Insights na galeria do mosaico, ou pode afixá-los diretamente a partir de ecrãs de Application Insights.  Isto permite-lhe configurar gráficos e vistas já estiver familiarizado com antes de afixá-los ao dashboard.  Comece por adicionar a descrição geral do Estado de funcionamento padrão para a sua aplicação.  Isto requer nenhuma configuração e permite aos personalização mínima no dashboard.


1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione a aplicação.
2. No **linha cronológica de descrição geral**, selecione o menu de contexto e clique em **afixar ao dashboard**.  Esta ação adiciona o mosaico do dashboard do último que estava a visualizar.  

    ![Linha cronológica de descrição geral do PIN](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. Na parte superior do ecrã, clique em **veja o dashboard** para regressar ao dashboard.
4. A linha cronológica de descrição geral é agora adicionada ao dashboard.  Clique e arraste-o na posição e, em seguida, clique em **feito personalizar** e **publicar alterações**.  O dashboard tem agora um mosaico algumas informações úteis.

    ![Dashboard com a linha cronológica de descrição geral](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Adicionar gráfico métrico personalizado
O **métricas** painel permite-lhe uma métrica recolhida pelo Application Insights ao longo do tempo com filtros opcionais e o agrupamento da graph.  Como tudo o resto no Application Insights, pode adicionar este gráfico ao dashboard.  Isto requer que efetue um pequeno personalização primeiro.

1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione a aplicação.
1. Selecione **métricas**.  
2. Já tiver sido criado um gráfico vazio e está a ser pedido para adicionar uma métrica.  Adicionar uma métrica do gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo abaixo mostra o número de pedidos de servidor, agrupados por com êxito.  Isto proporciona uma vista de execução de pedidos com êxito e êxito.

    ![Adicionar métrica](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Selecione o menu de contexto para o gráfico e selecione **afixar ao dashboard**.  Esta ação adiciona a vista de dashboard do último que estava a trabalhar com.

    ![Gráfico de métrico de PIN](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. Na parte superior do ecrã, clique em **veja o dashboard** para regressar ao dashboard.

4. A linha cronológica de descrição geral é agora adicionada ao dashboard.  Clique e arraste-o na posição e, em seguida, clique em **feito personalizar** e, em seguida, **publicar alterações**. 

    ![Dashboard de métricas](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Explorador de métricas
**Explorador de métricas** é semelhante à métrica embora permite significativamente mais personalização quando adicionado ao dashboard.  Que utilizar para as métricas da graph depende da sua preferência específica e requisitos.

1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione a aplicação.
1. Selecione **Explorador de métricas**. 
2. Clique para editar o gráfico e selecione uma ou mais métricas e, opcionalmente, uma configuração de detalhado.  O exemplo apresenta um gráfico de linhas tempo de resposta médio da página de controlo.
3. Clique no ícone de pin na parte superior direita para adicionar o gráfico ao dashboard e, em seguida, arraste-o na posição.

    ![Explorador de métricas](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. O mosaico do Explorador de métricas permite personalização mais uma vez que é adicionado ao dashboard.  Clique com o botão direito do rato no mosaico e selecione **editar título** para adicionar um título personalizado.  Avançar e efetuar outras personalizações, se pretender.

    ![Dashboard com o Explorador de métricas](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. Tem agora o gráfico de Explorador de métricas adicionado ao dashboard.

    ![Dashboard com o Explorador de métricas](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Adicionar a consulta de análise
Application Insights Analytics do Azure fornece um idioma de consulta avançada permite-lhe analisar todos os dados recolhidos Application Insights.  Tal como a outras vistas e gráficos, pode adicionar a saída de uma consulta de análise ao dashboard.   

Uma vez que a análise de informações de aplicações do Azure é um serviço separado, terá de partilhar o dashboard para o mesmo incluir uma consulta de análise.  Quando partilha um dashboard do Azure, publicá-lo como um recurso do Azure que pode tornar disponível para outros utilizadores e recursos.  

1. Na parte superior do ecrã de dashboard, clique em **partilha**.

    ![Publicar o dashboard](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Manter o **nome do Dashboard** mesmo e selecione o **nome da subscrição** para partilhar o dashboard.  Clique em **Publicar**.  O dashboard está agora disponível para outros serviços e subscrições.  Opcionalmente, pode definir a utilizadores específicos que devem ter acesso ao dashboard.
1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione a aplicação.
2. Clique em **análise** na parte superior do ecrã, para abrir o portal de análise.

    ![Análise de início](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Escreva a consulta seguinte, que devolve as superiores 10 mais pedidas as páginas e respetiva contagem de pedido:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Clique em **aceda** para validar os resultados da consulta.
5. Clique no ícone de pin e selecione o nome do dashboard.  O motivo que esta opção tem que selecione um dashboard ao contrário dos passos anteriores, em que foi utilizado o último dashboard é porque a consola de análise é um serviço separado e tem de selecionar a partir de todos os dashboards partilhados disponíveis.

    ![Consulta de análise do PIN](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Antes de regressar ao dashboard, adicione outra consulta, mas desta vez compor-lo como um gráfico para ver as diferentes formas para visualizar uma consulta de análise num dashboard.  Começar com a seguinte consulta que resume as principais 10 operações com a maioria das exceções.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Selecione **gráfico** e, em seguida, mude para um **anel** para visualizar os resultados.

    ![Gráfico de análise](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. Clique no ícone de pin para afixar o gráfico ao dashboard e desta vez, selecione a ligação para regressar ao dashboard.
4. Agora, os resultados das consultas são adicionados ao seu dashboard no formato que selecionou.  Clique e arraste cada na posição e, em seguida, clique em **terminar a edição**.
5. Com o botão direito clique em cada um dos mosaicos e selecione **editar título** atribuir-lhes um título descritivo.

    ![Dashboard de análise](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. Clique em **publicar alterações** para consolidar as alterações ao seu dashboard que inclui agora uma variedade de gráficos e visualizações do Application Insights.


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como criar dashboards personalizados, ter um aspeto inativos de documentação do Application Insights, incluindo um caso prático.

> [!div class="nextstepaction"]
> [Diagnóstico profundo](app-insights-devops.md)