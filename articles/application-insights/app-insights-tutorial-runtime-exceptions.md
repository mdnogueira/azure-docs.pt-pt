---
title: "Diagnosticar exceções de tempo de execução, utilizar o Azure Application Insights | Microsoft Docs"
description: "Tutorial para localizar e diagnosticar exceções de tempo de execução na sua aplicação com o Azure Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 11e0f2f19acc843f1c558b5d0cfe84291035a6a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Localizar e diagnosticar exceções de tempo de execução com o Azure Application Insights

Azure Application Insights recolhe telemetria da sua aplicação para ajudar a identificar e diagnosticar exceções de tempo de execução.  Este tutorial guia-o ao longo deste processo com a sua aplicação.  Saiba como:

> [!div class="checklist"]
> * Modificar o seu projeto para ativar o controlo de exceções
> * Identificar as exceções de diferentes componentes da aplicação
> * Ver detalhes de uma exceção
> * Transfira um instantâneo da exceção para o Visual Studio para depuração
> * Analise os detalhes de pedidos falhados utilizando a linguagem de consulta
> * Criar um novo item de trabalho para corrigir o código defeituoso


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
- Transfira e instale o [depurador de instantâneo do Visual Studio](http://aka.ms/snapshotdebugger).
- Ativar [depurador de instantâneos do Visual Studio](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-snapshot-debugger)
- Implementar uma aplicação .NET do Azure e [ativar o Application Insights SDK](app-insights-asp-net.md). 
- O tutorial controla a identificação de uma exceção na sua aplicação, por isso, modifique o código no seu ambiente de desenvolvimento ou teste para gerar uma exceção. 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analisar falhas
Application Insights recolhe eventuais falhas na sua aplicação e permite-lhe ver os respetivos frequência em operações diferentes para o ajudar a focar-se os esforços nessas com o maior impacto.  Pode, em seguida, desagregar em detalhes uma destas falhas para identificar a causa raiz.   

1. Selecione **Application Insights** e, em seguida, a sua subscrição.  
1. Para abrir o **falhas** painel selecione **falhas** sob o **investigar** menu ou clique o **pedidos falhados** gráfico.

    ![Pedidos falhados](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

2. O **pedidos falhados** painel mostra a contagem de pedidos falhados e o número de utilizadores afetados para cada operação para a aplicação.  Por estas informações ao utilizador a ordenação pode identificar as falhas de que a maioria afeta os utilizadores.  Neste exemplo, o **obter funcionários/cria** e **obter clientes/detalhes** provável candidatos para investigar devido ao respetivo grande número de falhas e que os utilizadores afetados.  Selecionar uma operação mostra mais informações sobre esta operação no painel à direita.

    ![Falha ao painel de pedidos](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

3. Reduza a janela de tempo para aplicar zoom no período em que a taxa de falhas mostra um pico de pedidos.

    ![Janela de pedidos falhados](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

4. Clique em **ver detalhes** para ver os detalhes para a operação.  Isto inclui um Gantt gráfico que mostra as duas dependências falhadas que coletivamente demorou quase metade de um segundo para concluir.  Pode saber mais sobre como analisar problemas de desempenho por concluir o tutorial [localizar e diagnosticar problemas de desempenho com o Azure Application Insights](app-insights-tutorial-performance.md).

    ![Falha ao obter detalhes de pedidos](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

5. Os detalhes de operações também mostra um FormatException que parece ter causou a falha.  Clique em de exceção ou no **tipos de exceção da parte superior 3** contagem para ver os detalhes.  Pode ver o que é devido a um código postal inválido.

    ![Detalhes da exceção](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)



## <a name="identify-failing-code"></a>Identificar com falhas de código
O depurador de instantâneo recolhe instantâneos das exceções mais frequentes na sua aplicação para o ajudar a diagnosticar a causa de raiz na produção.  Pode ver instantâneos de depuração no portal para ver a chamada da pilha e inspecionar as variáveis em cada frame de pilha de chamadas. Em seguida, pode depurar o código de origem ao descarregar o instantâneo e abri-lo no Visual Studio 2017.

1. Nas propriedades da exceção, clique em **depuração Abrir instantâneo**.
2. O **depurar instantâneo** painel abre-se com a pilha de chamadas para o pedido.  Clique em qualquer método para ver os valores de todas as variáveis locais no momento do pedido.  A partir do método superior neste exemplo, é possível ver as variáveis locais que não tenham nenhum valor.

    ![Depurar o instantâneo](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. É a primeira chamada que tem os valores válidos **ValidZipCode**, e for possível ver que um código postal fornecido com letras que não não capazes de ser convertido de um número inteiro.  Isto parece ser o erro no código que tem de ser corrigido.

    ![Depurar o instantâneo](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Para transferir este instantâneo para o Visual Studio onde iremos possam localizar o código real que tem de ser corrigido, clique em **transferir instantâneo**.
6. O instantâneo é carregado para o Visual Studio.
7. Agora, pode executar uma sessão de depuração no Visual Studio rapidamente identifica a linha de código que causou a exceção.

    ![Exceção no código](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Dados de análise de utilização
Todos os dados recolhidos pelo Application Insights é armazenado no Log Analytics do Azure, que fornece um idioma de consulta avançada permite-lhe analisar os dados de diversas formas.  Podemos utilizar estes dados para analisar os pedidos que gerou a exceção que está a pesquisar. 

8. Clique nas informações de CodeLens acima o código para ver a telemetria fornecida pelo Application Insights.

    ![Código](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Clique em **analisar impacto** para abrir o Application Insights Analytics.  Este é preenchido com várias consultas que fornecem detalhes sobre pedidos com falhas, tais como utilizadores afectados, os browsers e regiões.<br><br>![Análise](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Adicionar item de trabalho
Se ligar Application Insights para um sistema de controlo, tais como o Visual Studio Team Services ou o GitHub, pode criar um item de trabalho diretamente a partir do Application Insights.

1. Volte à **exceção propriedades** painel no Application Insights.
2. Clique em **Novo Item de trabalho**.
3. O **Novo Item de trabalho** painel abre-se com detalhes sobre a exceção já preenchidos.  Pode adicionar qualquer informação adicional antes de guardar.

    ![Novo Item de trabalho](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como identificar as exceções de tempo de execução, avançar para o próximo tutorial para saber como identificar e diagnosticar problemas de desempenho.

> [!div class="nextstepaction"]
> [Identificar problemas de desempenho](app-insights-tutorial-performance.md)