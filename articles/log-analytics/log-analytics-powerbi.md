---
title: "Importar dados de análise de registos do Azure para o Power BI | Microsoft Docs"
description: "Power BI é um serviço de análise de negócio baseada na nuvem da Microsoft que fornece visualizações otimizadas e relatórios para análise dos diferentes conjuntos de dados.  Este artigo descreve como configurar a importar dados de análise de registos para o Power BI e configurá-lo para atualizar automaticamente."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: bwren
ms.openlocfilehash: 163ac33af43a8cb7a23742f6336efca5fe7c4b4e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importar dados de análise de registos do Azure para o Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócio baseada na nuvem da Microsoft que fornece visualizações otimizadas e relatórios para análise dos diferentes conjuntos de dados.  Pode importar os resultados da pesquisa de análise de registos de registo para um conjunto de dados do Power BI, pelo que pode tirar partido do respetivo suchas funcionalidades combing dados de diferentes origens e partilha relatórios na web e dispositivos móveis.

Este artigo fornece detalhes sobre a importação de dados de análise de registos para o Power BI e agendamento-lo para atualizar automaticamente.  Diferentes processos estão incluídos para um [atualizado](#upgraded-workspace) e um [legado](#legacy-workspace) área de trabalho.

## <a name="upgraded-workspace"></a>Área de trabalho atualizada


Para importar dados a partir de um [atualizado a área de trabalho de análise de registos](log-analytics-log-search-upgrade.md) no Power BI, crie um conjunto de dados no Power BI com base numa consulta de pesquisa de registo na análise de registos.  A consulta é executada sempre que o conjunto de dados está atualizado.  Em seguida, pode criar relatórios do Power BI que utilizam dados do conjunto de dados.  Para criar o conjunto de dados no Power BI, exportar a consulta de análise de registos para [idioma de consulta de energia (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Em seguida, utilize esta opção para criar uma consulta no Power BI Desktop e, em seguida, publicá-lo no Power BI como um conjunto de dados.  Os detalhes para este processo são descritos abaixo.

![Análise de registos para o Power BI](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>Consulta de exportação
Comece por criar um [pesquisa registo](log-analytics-log-search-new.md) que devolve os dados de análise de registos que pretende povoar o conjunto de dados do Power BI.  Em seguida, exportar essa consulta para [idioma de consulta de energia (M)](https://msdn.microsoft.com/library/mt807488.aspx) que pode ser utilizado pelo Power BI Desktop.

1. Crie a pesquisa de registo na análise de registos para extrair os dados para o conjunto de dados.
2. Se estiver a utilizar o portal de pesquisa de registo, clique em **Power BI**.  Se estiver a utilizar o portal da análise, selecione **exportar** > **Power BI consulta (M)**.  A consulta ambas estas opções de exportação para um ficheiro de texto chamado **PowerBIQuery.txt**. 

    ![Pesquisa de registo de exportação](media/log-analytics-powerbi/export-logsearch.png) ![Pesquisa de registo de exportação](media/log-analytics-powerbi/export-analytics.png)

3. Abra o ficheiro de texto e copie o respetivo conteúdo.

### <a name="import-query-into-power-bi-desktop"></a>Importar consulta para o Power BI Desktop
Ambiente de trabalho do Power BI é uma aplicação de ambiente de trabalho que permite-lhe criar conjuntos de dados e relatórios que podem ser publicados para o Power BI.  Também pode utilizar para criar uma consulta utilizando a linguagem de Power Query exportada a partir da análise de registos. 

1. Instalar [Power BI Desktop](https://powerbi.microsoft.com/desktop/) se não tiver já-lo e, em seguida, abra a aplicação.
2. Selecione **obter dados** > **consulta em branco** para abrir uma nova consulta.  Em seguida, selecione **avançadas Editor** e cole o conteúdo do ficheiro exportado para a consulta. Clique em **Concluído**.

    ![Consulta do Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. A consulta é executada, e os respetivos resultados são apresentados.  Poderão ser-lhe solicitadas as credenciais ligar ao Azure.  
6. Escreva um nome descritivo para a consulta.  A predefinição é **Query1**. Clique em **fechar e aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Publicar para o Power BI
Quando publica para o Power BI, serão criados um conjunto de dados e um relatório.  Se criar um relatório no Power BI Desktop, em seguida, este será publicado com os seus dados.  Caso contrário, em seguida, irá criar um relatório em branco.  Pode modificar o relatório no Power BI ou crie uma nova com base no conjunto de dados.

8. Crie um relatório com base nos seus dados.  Utilize [documentação do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) se não estiver familiarizado com o mesmo.  Quando estiver pronto para enviar ao Power BI, clique em **publicar**.  Quando lhe for pedido, selecione um destino na sua conta do Power BI.  A menos que tenha um destino específico em mente, utilize **minha área de trabalho**.

    ![Publicar o Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Quando a publicação estiver concluída, clique em **abrir no Power BI** para abrir o Power BI com o novo conjunto de dados.


### <a name="configure-scheduled-refresh"></a>Configurar a atualização agendada
O conjunto de dados criado no Power BI terão os mesmos dados que vimos anteriormente no Power BI Desktop.  Terá de atualizar o conjunto de dados periodicamente para executar a consulta novamente e preenchê-lo com os dados mais recentes da análise de registos.  

1. Clique na área de trabalho onde que carregou o relatório e selecione o **conjuntos de dados** menu. Selecione o menu de contexto junto ao seu novo conjunto de dados e selecione **definições**. Em **as credenciais da origem de dados** deve ter uma mensagem que as credenciais são inválidas.  Isto acontece porque ainda não forneceu as credenciais ainda para o conjunto de dados a utilizar quando se atualiza os dados.  Clique em **editar credenciais** e especificar credenciais com acesso a análise de registos.

    ![Agendamento do Power BI](media/log-analytics-powerbi/powerbi-schedule.png)

5. Em **atualização agendada** ative a opção para **manter os seus dados atualizados**.  Opcionalmente, pode alterar o **atualizar frequência** e um ou mais horas específicas ao executar a atualização.

    ![Atualização do Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>Área de trabalho legada
Quando configura o Power BI com um [área de trabalho de análise de registos legada](log-analytics-powerbi.md), criar consultas de registo que exportar os resultados para conjuntos de dados correspondentes no Power BI.  A consulta e a exportação continua a ser executado automaticamente com base numa agenda que define para manter o conjunto de dados atualizados com os dados mais recentes recolhidos pelo registo de análise.

![Análise de registos para o Power BI](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Agendas do Power BI
A *Power BI agenda* inclui uma pesquisa de registo que exporta um conjunto de dados do repositório OMS para um conjunto de dados correspondente no Power BI e uma agenda que define a frequência esta pesquisa é executada para manter o conjunto de dados atual.

Os campos no conjunto de dados corresponderá as propriedades dos registos devolvidos pela pesquisa de registo.  Se a pesquisa devolve os registos de diferentes tipos, em seguida, o conjunto de dados irão incluir todas as propriedades de cada um dos tipos de registo incluídos.  

### <a name="connecting-oms-workspace-to-power-bi"></a>Ligar a área de trabalho OMS ao Power BI
Pode exportar da análise de registos para o Power BI, tem de ligar a área de trabalho OMS à sua conta Power BI utilizando o procedimento seguinte.  

1. Na consola do OMS clique o **definições** mosaico.
2. Selecione **contas**.
3. No **informações de área de trabalho** secção clique **ligar a conta do Power BI**.
4. Introduza as credenciais para a sua conta Power BI.

### <a name="create-a-power-bi-schedule"></a>Criar uma agenda do Power BI
Crie uma agenda de Power BI para cada conjunto de dados utilizando o procedimento seguinte.

1. Na consola do OMS clique o **pesquisa registo** mosaico.
2. Escreva uma nova consulta ou selecione uma pesquisa guardada que devolve os dados que pretende exportar para **Power BI**.  
3. Clique em de **Power BI** botão na parte superior da página para abrir o **Power BI** caixa de diálogo.
4. Forneça as informações na tabela seguinte e clique em **guardar**.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |Nome para identificar a agenda ao ver a lista de agendas do Power BI. |
| Pesquisa guardada |A pesquisa de registo para ser executada.  Pode selecionar a consulta atual ou selecionar uma pesquisa guardada existente na caixa pendente. |
| Agenda |Como muitas vezes, execute a pesquisa guardada e exportar para o conjunto de dados do Power BI.  O valor tem de estar entre 15 minutos e 24 horas. |
| Nome do conjunto de dados |O nome do conjunto de dados no Power BI.  Será criado se não existir e atualizada se existir. |

### <a name="viewing-and-removing-power-bi-schedules"></a>Visualizar e remover as agendas do Power BI
Ver a lista de agendas de BI de energia existentes com o seguinte procedimento.

1. Na consola do OMS clique o **definições** mosaico.
2. Selecione **Power BI**.

Além dos detalhes da agenda, o número de vezes que a agenda foi executado da semana passada e o estado da última sincronização são apresentados.  Se a sincronização detectados erros, pode clicar na ligação para executar uma pesquisa de registo de registos com detalhes do erro.

Pode remover uma agenda ao clicar no **X** no **Remover coluna**.  Pode desativar uma agenda selecionando **desativar**.  Para modificar uma agenda tem de removê-lo e recriá-lo com as novas definições.

![Agendas do Power BI](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>Instruções de exemplo
A secção seguinte explica um exemplo de criar uma agenda do Power BI e para criar um relatório simples utilizando o respetivo conjunto de dados.  Neste exemplo, todos os dados de desempenho para um conjunto de computadores são exportados para o Power BI e, em seguida, um gráfico de linha é criado para apresentar a utilização do processador.

#### <a name="create-log-search"></a>Criar a pesquisa de registo
Vamos começar por criar uma pesquisa de registo para os dados que pretendemos para enviar para o conjunto de dados.  Neste exemplo, vamos utilizar uma consulta que devolva todos os dados de desempenho para computadores com um nome que começa com *srv*.  

![Agendas do Power BI](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Criar a pesquisa do Power BI
Clicarmos no **Power BI** botão para abrir a caixa de diálogo do Power BI e forneça as informações necessárias.  Queremos esta pesquisa para executar uma vez por hora e criar um conjunto de dados denominado *Contoso desempenho*.  Uma vez que já temos a abertura de pesquisa que cria os dados que pretendemos, iremos manter as predefinições de *consulta de pesquisa atual utilize* para **pesquisa guardada**.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Certifique-se de pesquisa do Power BI
Para verificar que criámos a agenda corretamente, vamos ver a lista de Power BI pesquisas sob o **definições** mosaico no dashboard do OMS.  Iremos aguarde vários minutos e atualizar esta vista até mesmo os relatórios que a sincronização foi executada.  Normalmente, irá agendar o conjunto de dados são atualizados automaticamente.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Verifique se o conjunto de dados no Power BI
Vamos iniciar sessão na nossa conta em [powerbi.microsoft.com](http://powerbi.microsoft.com/) e desloque-se para **conjuntos de dados** na parte inferior do painel esquerdo.  É possível ver que o *Contoso desempenho* conjunto de dados está listado com a indicação de que o nosso exportação foi executada com êxito.

![O conjunto de dados do Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>Criar o relatório com base no conjunto de dados
Iremos selecionar o **Contoso desempenho** conjunto de dados e, em seguida, clique em **resultados** no **campos** painel à direita para ver os campos que fazem parte deste conjunto de dados.  Para criar um gráfico de linha com a utilização do processador para cada computador, iremos efetuar as seguintes ações.

1. Selecione a visualização de gráfico de linha.
2. Arraste **ObjectName** para **filtro de nível de relatório** e verifique **processador**.
3. Arraste **CounterName** para **filtro de nível de relatório** e verifique **% de tempo do processador**.
4. Arraste **CounterValue** para **valores**.
5. Arraste **computador** para **legenda**.
6. Arraste **TimeGenerated** para **eixo**.

É possível ver que o gráfico de linha resultante é apresentado com os dados do nosso conjunto de dados.

![Gráfico de linha do Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>Guardar o relatório
Iremos guardar o relatório clicando no botão Guardar na parte superior do ecrã e confirme que está agora listada na secção dos relatórios no painel esquerdo.

![Relatórios do Power BI](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para criar consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre [Power BI](http://powerbi.microsoft.com) criar visualizações baseadas nos exportações de análise de registos.
