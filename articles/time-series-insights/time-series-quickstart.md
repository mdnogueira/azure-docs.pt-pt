---
title: "Guia de introdução - Explorador de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este guia de introdução mostra como começar com o Explorador de informações de séries de tempo do Azure no seu browser para visualizar grandes volumes de dados de IoT. Tour principais funcionalidades num ambiente de demonstração."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: v-mamcge
manager: jhubbard
editor: MarkMcGeeAtAquent, jasonwhowell, kfile, MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 11/15/2017
ms.openlocfilehash: b1f2881da21849c3ac09b008640fc9f72dc158dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Início rápido: Explorar Insights de séries de tempo do Azure
Este guia de introdução mostra como começar com o Explorador de informações de séries de tempo do Azure num ambiente de demonstração gratuita. Irá aprender a utilizar, browser para visualizar grandes volumes de dados de IoT e tour principais funcionalidades do Explorador de informações de séries de tempo. 

O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a exploração e a análise de milhões de eventos de IoT em simultâneo. Proporciona uma vista global dos seus dados, permitindo-lhe validar a sua solução de IoT e evitar períodos de indisponibilidade dispendiosos dispositivos fundamentais, ajudando-o a detetar as tendências ocultas, rapidamente detetar anomalias e realizar a causa raiz estatísticas em tempo real.  Se está a criar uma aplicação que necessita de consulta ou o arquivo de dados de série de tempo, pode desenvolver com as APIs de REST de informações de série de tempo.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Explorar o Explorador de informações de séries de tempo num ambiente de demonstração

1. No seu browser, navegue para [https://insights.timeseries.azure.com/demo](https://insights.timeseries.azure.com/demo). 

2. Se lhe for solicitado, inicie sessão no Explorador de informações de séries de tempo com as suas credenciais de conta do Azure. 
 
3. É apresentada a página de visita guiada das informações de séries de tempo. Clique em **seguinte** para começar a visita guiada.

   ![Clique em seguinte](media/quickstart/quickstart1.png)

4. O **painel de seleção de tempo** é apresentado. Utilize este painel para selecionar um intervalo de tempo para visualizar.

   ![Painel de seleção de tempo](media/quickstart/quickstart2.png)

5. Clique e arraste na região, em seguida, clique em de **pesquisa** botão.
 
   ![Selecionar um intervalo de tempo](media/quickstart/quickstart3.png) 

   Informações de séries de tempo apresenta uma visualização de gráfico para o intervalo de tempo especificado. Pode efetuar diversas ações dentro com o gráfico de linhas, tais como a filtragem, afixação, ordenação e stacking. 

   Para voltar para o **painel de seleção de tempo**, clique na seta para baixo, conforme mostrado:

   ![Gráfico](media/quickstart/quickstart4.png)

6. Clique em **adicionar** no **painel termos** para adicionar um novo termo de pesquisa.

   ![Adicionar item](media/quickstart/quickstart5.png)

7. No gráfico, pode selecionar uma região, região com o botão direito e selecione **explorar eventos**.
 
   ![Explorar eventos](media/quickstart/quickstart6.png)

   É apresentada uma grelha dos dados não processados a região que são explorar:

   ![Vista de grelha](media/quickstart/quickstart7.png)

8. Editar os termos de licenciamento para alterar os valores no gráfico e adicionar outro termo para diferentes tipos de valores entre-correlacione:

   ![Adicionar um termo](media/quickstart/quickstart8.png)

9. Introduza um termo de filtro no **filtrar série...**  caixa para filtrar série ad hoc. Para o guia de introdução, introduza **Station5** para correlacione entre relativos à temperatura e pressão para essa estação.
 
   ![Série de filtro](media/quickstart/quickstart9.png)

Depois de concluir o início rápido, pode experimentar o conjunto de dados de exemplo para criar visualizações diferentes. 

### <a name="next-steps"></a>Passos seguintes
Está pronto para criar o seu ambiente de informações de séries de tempo:
> [!div class="nextstepaction"]
> [Planear o ambiente de informações de séries de tempo](time-series-insights-environment-planning.md)
