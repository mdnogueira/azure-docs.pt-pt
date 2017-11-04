---
title: Diagnosticar problemas de desempenho com o Azure Application Insights | Microsoft Docs
description: "Tutorial para localizar e diagnosticar problemas de desempenho na sua aplicação utilizar o Azure Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 0edec15c7f14ee5338555b03700b7be32c3a1023
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Localizar e diagnosticar problemas de desempenho com o Azure Application Insights

Azure Application Insights recolhe telemetria da sua aplicação para ajudar a analisar o desempenho e operação.  Pode utilizar estas informações para identificar problemas que poderão estar a ocorrer ou para identificar melhoramentos para a aplicação que teria maior parte dos utilizadores de impacto.  Este tutorial guia-o ao longo do processo de analisar o desempenho de ambos os componentes de servidor da sua aplicação e a perspetiva do cliente.  Saiba como:

> [!div class="checklist"]
> * Identificar o desempenho de operações do lado do servidor
> * Analisar as operações de servidor para determinar a causa de raiz de um desempenho lento
> * Identificar operações mais lentas do lado do cliente
> * Analise os detalhes de vistas de página utilizando a linguagem de consulta


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
- Implementar uma aplicação .NET do Azure e [ativar o Application Insights SDK](app-insights-asp-net.md).
- [Ativar o gerador de perfis do Application Insights](app-insights-profiler.md#installation) para a sua aplicação.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identificar as operações do servidor lenta
Application Insights recolhe detalhes de desempenho para as operações diferentes na sua aplicação.  Ao identificar essas operações com a duração mais longo, pode diagnosticar potenciais problemas ou melhor o desenvolvimento em curso a melhorar o desempenho global da aplicação de destino.

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  
1. Para abrir o **desempenho** painel selecione **desempenho** sob o **investigar** menu ou clique o **tempo de resposta do servidor** gráfico .

    ![Desempenho](media/app-insights-tutorial-performance/performance.png)

2. O **desempenho** painel mostra a duração da contagem e a média de cada operação para a aplicação.  Pode utilizar estas informações para identificar essas operações que maioria afeta os utilizadores. Neste exemplo, o **obter clientes/detalhes** e **GET Home/Index** provável candidatos para investigar devido a respetiva duração relativamente elevada e o número de chamadas.  Outras operações podem ter uma duração superior, mas foram raramente chamadas, pelo que o efeito da respetiva melhoramento estarão mínimo.  

    ![Painel de desempenho](media/app-insights-tutorial-performance/performance-blade.png)

3. O gráfico mostra a duração média de todas as operações ao longo do tempo.  Adicione as operações que está a interessadas por afixação-los ao gráfico.  Isto mostra que existem alguns picos vale a investigar.  Isole isto ainda mais ao reduzir a janela de tempo do gráfico.

    ![Operações de PIN](media/app-insights-tutorial-performance/pin-operations.png)

4.  Clique uma operação para ver o painel de desempenho à direita. Isto mostra a distribuição das durações para pedidos de diferentes.  Os utilizadores, normalmente, repare abrandar o desempenho em cerca de meio segundo, por isso, reduzir a janela de pedidos por 500 milissegundos.  

    ![Distribuição da duração](media/app-insights-tutorial-performance/duration-distribution.png)

5.  Neste exemplo, pode ver que um número significativo de pedidos está a demorar através de um segundo para processar. Pode ver os detalhes desta operação, clicando no **detalhes de operação**.

    ![Detalhes de operação](media/app-insights-tutorial-performance/operation-details.png)

6.  As informações que tiver recolheu até ao momento apenas confirma que existe um desempenho lento, mas existir pouco para obter a causa raiz.  O **gerador de perfis** ajuda-o com esta mostrando o código real que foi executada para a operação e o tempo necessário para cada passo. Algumas operações não podem ter um rastreio de uma vez que o gerador de perfis é executado periodicamente.  Ao longo do tempo, mais operações devem ter rastreios.  Para iniciar o gerador de perfis para a operação, clique em **rastreios de gerador de perfis**.
5.  O rastreio mostra os eventos individuais para cada operação, pelo que pode diagnosticar a causa de raiz para a duração da operação geral.  Clique dos exemplos de principais, que têm a duração mais longo.
6.  Clique em **Mostrar frequente caminho** para realçar o caminho específico dos eventos mais contribuem para a duração total da operação.  Neste exemplo, pode ver que a chamada mais lentas é de *FabrikamFiberAzureStorage.GetStorageTableData* método. É a parte que demora mais tempo a *CloudTable.CreateIfNotExist* método. Se esta linha de código é executada sempre que a função é chamada, serão consumidas chamada de rede desnecessárias e recursos de CPU. A melhor forma para corrigir o seu código é colocar esta linha em algum método de arranque apenas por uma vez a executar. 

    ![Detalhes do gerador de perfis](media/app-insights-tutorial-performance/profiler-details.png)

7.  O **desempenho sugestão** na parte superior do ecrã suporta a avaliação de que a duração excessiva devido à espera.  Clique em de **a aguardar** ligação para obter documentação sobre como interpretar os diferentes tipos de eventos.

    ![Sugestão de desempenho](media/app-insights-tutorial-performance/performance-tip.png)

8.  Para análise adicional, pode clicar em **transferir. etl rastreio** para transferir o rastreio no Visual Studio.

## <a name="use-analytics-data-for-server"></a>Dados de análise de utilização para o servidor
Application Insights Analytics fornece um idioma de consulta avançada permite-lhe analisar todos os dados recolhidos pelo Application Insights.  Pode utilizar este para efetuar uma análise detalhada nos dados de pedido e o desempenho.

1. Regresse ao painel de detalhes de operação e clique no botão de análise.

    ![Botão de análise](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics abre-se com uma consulta para cada uma das vistas no painel.  Pode executar estas consultas como estão ou modificá-las para os seus requisitos.  A primeira consulta mostra a duração para esta operação ao longo do tempo.

    ![Análise](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identificar as operações de cliente lenta
Para além de identificar os processos do servidor para otimizar, Application Insights pode analisar a perspetiva dos browsers cliente.  Isto pode ajudar a identificar possíveis melhoramentos para os componentes de cliente e até mesmo identificam problemas com os browsers diferentes ou em diferentes localizações.

1. Selecione **Browser** em **investigar** para abrir o browser resumo.  Esta opção fornece um resumo visual de vários telemetries da sua aplicação da perspetiva do browser.

    ![Resumo do browser](media/app-insights-tutorial-performance/browser-summary.png)

2.  Desloque para baixo até **quais são as minhas páginas mais lentas?**.  Mostra uma lista das páginas na sua aplicação que efetuou o tempo mais longo para os clientes de carga.  Pode utilizar esta informação para atribuir prioridades a essas páginas que tenham o impacto mais significativo no utilizador.
3.  Clique das páginas para abrir o **página vista** painel.  No exemplo, o **/FabrikamProd** página é apresentada uma duração média excessiva.  O **página vista** painel fornece detalhes sobre esta página, incluindo uma divisão de intervalos de duração diferentes.

    ![Vista de página](media/app-insights-tutorial-performance/page-view.png)

4.  Clique na duração máxima para inspecionar os detalhes sobre estes pedidos.  Em seguida, clique em de pedido individual para ver os detalhes do cliente solicitar a página, incluindo o tipo de browser e a respetiva localização.  Estas informações podem ajudar a determinar se existem problemas de desempenho relacionados específica para tipos de clientes.

    ![Detalhes do pedido](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Dados de análise de utilização para o cliente
Como os dados recolhidos para desempenho do servidor, Application Insights disponibiliza todos os dados de cliente para uma análise detalhada através da análise.

1. Regresse ao navegador resumo e clique no ícone de análise.

    ![Ícone de análise](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics abre-se com uma consulta para cada uma das vistas no painel. A primeira consulta mostra a duração das vistas de página diferente ao longo do tempo.

    ![Análise](media/app-insights-tutorial-performance/client-analytics.png)

3.  Diagnóstico inteligente é uma funcionalidade do Application Insights Analytics que identifica exclusivos padrões nos dados.  Ao clicar o ponto de diagnóstico inteligente no gráfico de linhas, a mesma consulta é executada sem os registos que causou a anomalias.  Detalhes desses registos são apresentados na secção de comentário da consulta para que possa identificar as propriedades dessas vistas de página que estão a causar a duração excessiva.

    ![Diagnóstico inteligente](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como identificar as exceções de tempo de execução, avançar para o próximo tutorial para saber como criar alertas em resposta a falhas.

> [!div class="nextstepaction"]
> [Alerta sobre o estado de funcionamento da aplicação](app-insights-tutorial-alert.md)
