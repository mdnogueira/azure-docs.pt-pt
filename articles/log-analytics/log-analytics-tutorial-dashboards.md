---
title: Criar e partilhar dashboards de dados do Log Analytics do Azure | Microsoft Docs
description: "Este tutorial ajuda-o a compreender como dashboards de análise de registos podem visualizar a todas as pesquisas de registo guardado, dando-lhe uma lente única para ver o seu ambiente."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 272945134b534a5ded794379ce5e96b0902a4227
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Criar e partilhar dashboards de dados de análise de registos

Análise de registos dashboards podem visualize todas as suas pesquisas de registo guardado, dando-lhe a capacidade para localizar, correlacionar e partilha dados operacionais de IT na organização.  Este tutorial abrange a criação de uma pesquisa de registo que será utilizada para suportar um dashboard partilhado que será acedido por sua equipa de suporte de operações de TI.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard partilhado no portal do Azure
> * Visualizar uma pesquisa de registo de desempenho 
> * Adicionar uma pesquisa de registo a um dashboard partilhado 
> * Personalizar um mosaico no dashboard partilhado

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligado à área de trabalho de análise de registos](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Inicie sessão no portal do Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Criar um dashboard partilhado

A primeira coisa que vê depois de iniciar sessão no portal do Microsoft Azure é um [dashboard](../azure-portal/azure-portal-dashboards.md).<br> ![Dashboard do portal do Azure](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Aqui, pode reunir dados operacionais que são mais importantes para IT em todos os recursos do Azure, incluindo a telemetria de Log Analytics do Azure.  Antes, avance para visualizar uma pesquisa de registo, vamos primeiro criar um dashboard e partilhá-lo.  Isto permite-nos obter de forma, antes de efetuar é nossa pesquisa de registo de desempenho de exemplo, que irá ser composto como um gráfico de linhas e adicioná-lo ao dashboard.  

Para criar um dashboard, selecione o **novo dashboard** botão junto ao nome do dashboard atual.<br> ![Criar novo dashboard no portal do Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Esta ação cria um dashboard novo, vazio, privado e coloca-o no modo de personalização onde pode nome o dashboard e adicionar ou reorganizar os mosaicos. Editar o nome do dashboard e especificar *Dashboard de exemplo* para este tutorial, selecione **feito personalizar**.<br><br> ![Guardar personalizada dashboard do Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Quando cria um dashboard, é privado por predefinição, o que significa que é o único utilizador possa vê-la. Para torná-lo visível a outras pessoas, utilize o **partilha** botão que é apresentada juntamente com outros comandos do dashboard.<br> ![Partilhar um novo dashboard no portal do Azure](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

É-lhe pedido para escolher uma subscrição e o grupo de recursos para o seu dashboard para ser publicado. Para sua comodidade, o portal da publicação de guias de experiência, para um padrão onde colocar dashboards num grupo de recursos chamado **dashboards**.  Certifique-se a subscrição selecionada e, em seguida, clique em **publicar**.  O acesso às informações apresentadas no dashboard é controlado com [controlo de acesso com base do Azure Resource](../active-directory/role-based-access-control-configure.md).   

## <a name="visualize-a-log-search"></a>Visualizar uma pesquisa de registo

Pode criar consultas básicas numa única linha partir do portal da pesquisa de registo no portal do Azure. O portal de registo de pesquisa pode ser utilizado sem iniciar um portal externo e pode utilizá-lo para efetuar uma variedade de funções com pesquisas de registo, incluindo a criação de regras de alerta, criar grupos de computadores e exportar os resultados da consulta. 

O [portal da análise avançadas](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) é um portal dedicado que fornece funcionalidade avançada não está disponível no portal de registo de pesquisa. As funcionalidades incluem a capacidade de editar uma consulta em várias linhas, seletivamente executar código, o Intellisense context confidencial e análise inteligente. No portal da análise avançada, irá criar uma vista de desempenho no formulário gráfico, guarde-a para uma pesquisa futura e afixá-lo para o dashboard partilhado que criou anteriormente.   

Inicie o portal de análise avançadas de uma ligação no portal de registo de pesquisa.<br> ![Inicie o portal da análise avançada](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

No portal da análise, introduza a seguinte consulta para devolver o processador apenas registos de utilização para computadores Windows e Linux, agrupadas por computador e TimeGenerated e apresentado no gráfico de visual:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Guardar a consulta, selecionando o **guardar consultas** botão a partir do canto superior direito.<br> ![Guardar a consulta do portal da análise avançada](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> No **guardar consultas** painel de controlo, forneça um nome, tal como *VMs do Azure - utilização do processador* e, em seguida, clique em **guardar**.  Desta forma, pode criar uma biblioteca de consultas comuns para procurar com ou modificá-la sem ter de voltar a escrevê-la completamente.  Por fim, Afixar este dashboard partilhado criado anteriormente selecionando **gráfico de Pin ao dashboard do Azure** botão a partir do canto direito meio da página.  

Agora que temos uma consulta afixada ao dashboard, irá reparar que tem um título genérico e um comentário abaixo do mesmo.<br> ![Exemplo de dashboard do Azure](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Iremos deve alterá-lo para algo significativo que possam ser compreendidos facilmente por essas visualizá-lo.  Clique com botão direito do mosaico e selecione **editar mosaico**.  Quando tiver terminado de personalizar o título e subtítulo para o mosaico, clique em **atualização**.  Uma faixa será apresentada a pedir-lhe para publicar as alterações ou eliminar.  Clique em **publicar alterações** e, em seguida, feche o **editar mosaico** painel de controlo.  

![Configuração concluída do dashboard de exemplo](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar um dashboard no portal do Azure e adicione-lhe uma pesquisa de registo.  Avançadas para o próximo tutorial para saber o que pode implementar de respostas de diferentes com base nos resultados de pesquisa de registo.  

> [!div class="nextstepaction"]
> [Responder a eventos com alertas de análise do registo](log-analytics-tutorial-response.md)