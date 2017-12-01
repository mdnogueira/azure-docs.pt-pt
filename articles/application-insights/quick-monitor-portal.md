---
title: "Monitorizar uma aplicação Web ASP.NET com o Azure Application Insights | Microsoft Docs"
description: "Fornece instruções para configurar rapidamente uma Aplicação Web ASP.NET para monitorização com o Application Insights"
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/14/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 110d1a0fe52f50f057f7ea7ccbc426706473306d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Iniciar a Monitorização de uma Aplicação Web ASP.NET

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização.  Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique.  Com as informações que recolher com o Application Insights sobre o desempenho e a eficácia da sua aplicação, pode tomar decisões informadas para manter e melhorar a sua aplicação.

Este início rápido mostra como adicionar o Application Insights a uma aplicação Web ASP.NET existente e iniciar a análise das estatísticas em direto, um dos vários métodos que pode utilizar para analisar a sua aplicação. Se não tiver uma aplicação Web ASP.NET, pode criar uma seguindo o [Início rápido para criar uma aplicação Web ASP.NET](../app-service/app-service-web-get-started-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido:

- Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Abra o projeto no Visual Studio 2017.
2. Selecione **Configurar o Application Insights** no menu Projeto. O Visual Studio adiciona o Application Insights SDK à sua aplicação.
3. Clique em **Iniciar Gratuito**, selecione o seu plano de faturação preferencial e clique em **Registar**.

    ![Adicionar o Application Insights ao Visual Studio](./media/quick-monitor-portal/add-application-insights.png)

4. Execute a sua aplicação selecionando **Iniciar Depuração** no menu **Depurar** ou premindo a tecla F5.

## <a name="confirm-app-configuration"></a>Confirmar a configuração da aplicação

O Application Insights recolhe dados de telemetria da sua aplicação, independentemente de estar ou não em execução. Utilize os passos seguintes para começar a ver estes dados.

1. Abra o Application Insights clicando em **Projeto** -> **Application Insights** -> **Pesquisar Telemetria da Sessão de Depuração**.  É apresentada a telemetria da sua sessão atual.<BR><br>![Telemetria no Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Clique no primeiro pedido na lista (GET Home/Index neste exemplo) para ver os detalhes do pedido. Tenha em atenção que o código de estado e o tempo de resposta são incluídos juntamente com outras informações importantes sobre o pedido.<br><br>![Detalhes de resposta no Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

Agora, pode abrir o Application Insights no portal do Azure para ver os vários detalhes sobre a sua aplicação em execução.

1. Clique com o botão direito na pasta **Application Insights para Serviços Ligados** no Explorador de Soluções e clique em **Abril Portal do Application Insights**.  São apresentadas algumas informações sobre a sua aplicação e uma variedade de opções.

    ![Mapeamento de Aplicações](media/quick-monitor-portal/001.png)

2. Clique em **Mapeamento de aplicações** para obter um esquema visual das relações de dependência entre os componentes da aplicação.  Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

    ![Mapeamento de Aplicações](media/quick-monitor-portal/application-map.png)

3. Clique no ícone **Análise da Aplicação** ![Mapeamento de Aplicações](media/quick-monitor-portal/app-analytics-icon.png) num dos componentes da aplicação.  Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights.  Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico.  Pode escrever as suas próprias consultas para analisar outros dados.

    ![Análise](media/quick-monitor-portal/analytics.png)

4. Volte à página **Descrição geral** página e clique em **Live Stream**.  Isto mostra estatísticas em direto sobre a sua aplicação em execução.  Inclui informações como o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram.  Também pode inspecionar métricas críticas de desempenho, tais como processador e memória.

    ![Live Stream](media/quick-monitor-portal/live-stream.png)

Se está pronto para alojar a sua aplicação no Azure, pode publicá-la agora. Siga os passos descritos em [Início Rápido para Criar uma Aplicação Web ASP.NET](../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, ativou a aplicação para monitorização pelo Azure Application Insights.  Continue para os tutoriais para saber como utilizá-lo para monitorizar as estatísticas e detetar problemas na sua aplicação.

> [!div class="nextstepaction"]
> [Azure Application Insights](app-insights-tutorial-runtime-exceptions.md)