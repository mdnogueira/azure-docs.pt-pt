---
title: "Exportar dados de análise de registos para o Power BI | Microsoft Docs"
description: "Power BI é um serviço de análise de negócio baseada na nuvem da Microsoft que fornece visualizações otimizadas e relatórios para análise dos diferentes conjuntos de dados.  Análise de registos pode continuamente exportar dados do repositório OMS no Power BI para pode tirar partido do respetivo visualizações e ferramentas de análise.  Este artigo descreve como configurar consultas na análise de registos automaticamente exportar para o Power BI em intervalos regulares."
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
ms.date: 07/24/2017
ms.author: bwren
ms.openlocfilehash: 271747e25f319c76195ec643025d24c6b7cdc9c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="export-log-analytics-data-to-power-bi"></a>Exportar dados de análise de registos para o Power BI

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, este processo para exportar dados de análise de registos para o Power BI deixará de funcionar.  As agendas existentes que criou antes de atualizar irão tornar-se desativada. Também poderá já não ver a capacidade para ativar a capacidade de exportação do Power BI nas definições de funcionalidades de pré-visualização, como esta funcionalidade é totalmente lançada em áreas de trabalho atualizadas. 
>
> Após a atualização, o Log Analytics do Azure utiliza a mesma plataforma do Application Insights e utilizar o mesmo processo para exportar as consultas de análises de registo para o Power BI como [o processo para exportar consultas do Application Insights para o Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Pode a exportação a consulta utilizando a consola de análise, conforme descrito nesse artigo ou pode selecionar o **Power BI** botão na parte superior do ecrã no portal de registo de pesquisa.
>
> Os utilizadores terão acesso ao recurso da área de trabalho no Azure para utilizar o capabilitiy de exportação do Power BI em áreas de trabalho atualizadas. Sem acesso, os utilizadores verão uma mensagem de erro ao importar a consulta para o ambiente de trabalho do Power BI que não têm acesso.



[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócio baseada na nuvem da Microsoft que fornece visualizações otimizadas e relatórios para análise dos diferentes conjuntos de dados.  Análise de registos pode automaticamente exportar dados do repositório OMS no Power BI para pode tirar partido do respetivo visualizações e ferramentas de análise.

Quando configura o Power BI com a análise de registos, pode criar consultas de registo que exportar os resultados para conjuntos de dados correspondentes no Power BI.  A consulta e a exportação continua a ser executado automaticamente com base numa agenda que define para manter o conjunto de dados atualizados com os dados mais recentes recolhidos pelo registo de análise.

![Análise de registos para o Power BI](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Agendas do Power BI
A *Power BI agenda* inclui uma pesquisa de registo que exporta um conjunto de dados do repositório OMS para um conjunto de dados correspondente no Power BI e uma agenda que define a frequência esta pesquisa é executada para manter o conjunto de dados atual.

Os campos no conjunto de dados corresponderá as propriedades dos registos devolvidos pela pesquisa de registo.  Se a pesquisa devolve os registos de diferentes tipos, em seguida, o conjunto de dados irão incluir todas as propriedades de cada um dos tipos de registo incluídos.  

> [!NOTE]
> É recomendável utilizar uma consulta de pesquisa de registo que devolve dados não processados, por oposição a efetuar qualquer consolidação através de comandos, como [medidas](log-analytics-search-reference.md#measure).  Pode efetuar qualquer agregação e cálculos no Power BI a partir de dados não processados.
>
>

## <a name="connecting-oms-workspace-to-power-bi"></a>Ligar a área de trabalho OMS ao Power BI
Pode exportar da análise de registos para o Power BI, tem de ligar a área de trabalho OMS à sua conta Power BI utilizando o procedimento seguinte.  

1. Na consola do OMS clique o **definições** mosaico.
2. Selecione **contas**.
3. No **informações de área de trabalho** secção clique **ligar a conta do Power BI**.
4. Introduza as credenciais para a sua conta Power BI.

## <a name="create-a-power-bi-schedule"></a>Criar uma agenda do Power BI
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

## <a name="viewing-and-removing-power-bi-schedules"></a>Visualizar e remover as agendas do Power BI
Ver a lista de agendas de BI de energia existentes com o seguinte procedimento.

1. Na consola do OMS clique o **definições** mosaico.
2. Selecione **Power BI**.

Além dos detalhes da agenda, o número de vezes que a agenda foi executado da semana passada e o estado da última sincronização são apresentados.  Se a sincronização detectados erros, pode clicar na ligação para executar uma pesquisa de registo de registos com detalhes do erro.

Pode remover uma agenda ao clicar no **X** no **Remover coluna**.  Pode desativar uma agenda selecionando **desativar**.  Para modificar uma agenda tem de removê-lo e recriá-lo com as novas definições.

![Agendas do Power BI](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>Instruções de exemplo
A secção seguinte explica um exemplo de criar uma agenda do Power BI e para criar um relatório simples utilizando o respetivo conjunto de dados.  Neste exemplo, todos os dados de desempenho para um conjunto de computadores são exportados para o Power BI e, em seguida, um gráfico de linha é criado para apresentar a utilização do processador.

### <a name="create-log-search"></a>Criar a pesquisa de registo
Vamos começar por criar uma pesquisa de registo para os dados que pretendemos para enviar para o conjunto de dados.  Neste exemplo, vamos utilizar uma consulta que devolva todos os dados de desempenho para computadores com um nome que começa com *srv*.  

![Agendas do Power BI](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Criar a pesquisa do Power BI
Clicarmos no **Power BI** botão para abrir a caixa de diálogo do Power BI e forneça as informações necessárias.  Queremos esta pesquisa para executar uma vez por hora e criar um conjunto de dados denominado *Contoso desempenho*.  Uma vez que já temos a abertura de pesquisa que cria os dados que pretendemos, iremos manter as predefinições de *consulta de pesquisa atual utilize* para **pesquisa guardada**.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Certifique-se de pesquisa do Power BI
Para verificar que criámos a agenda corretamente, vamos ver a lista de Power BI pesquisas sob o **definições** mosaico no dashboard do OMS.  Iremos aguarde vários minutos e atualizar esta vista até mesmo os relatórios que a sincronização foi executada.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Verifique se o conjunto de dados no Power BI
Vamos iniciar sessão na nossa conta em [powerbi.microsoft.com](http://powerbi.microsoft.com/) e desloque-se para **conjuntos de dados** na parte inferior do painel esquerdo.  É possível ver que o *Contoso desempenho* conjunto de dados está listado com a indicação de que o nosso exportação foi executada com êxito.

![O conjunto de dados do Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Criar o relatório com base no conjunto de dados
Iremos selecionar o **Contoso desempenho** conjunto de dados e, em seguida, clique em **resultados** no **campos** painel à direita para ver os campos que fazem parte deste conjunto de dados.  Para criar um gráfico de linha com a utilização do processador para cada computador, iremos efetuar as seguintes ações.

1. Selecione a visualização de gráfico de linha.
2. Arraste **ObjectName** para **filtro de nível de relatório** e verifique **processador**.
3. Arraste **CounterName** para **filtro de nível de relatório** e verifique **% de tempo do processador**.
4. Arraste **CounterValue** para **valores**.
5. Arraste **computador** para **legenda**.
6. Arraste **TimeGenerated** para **eixo**.

É possível ver que o gráfico de linha resultante é apresentado com os dados do nosso conjunto de dados.

![Gráfico de linha do Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>Guardar o relatório
Iremos guardar o relatório clicando no botão Guardar na parte superior do ecrã e confirme que está agora listada na secção dos relatórios no painel esquerdo.

![Relatórios do Power BI](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para criar consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre [Power BI](http://powerbi.microsoft.com) criar visualizações baseadas nos exportações de análise de registos.
