---
title: "Monitorizar visualmente fábricas de dados do Azure | Microsoft Docs"
description: "Saiba como monitorizar visualmente fábricas de dados do Azure"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: shlo
ms.openlocfilehash: e3ddbb88453b3f5d5f8b4566cf91aadbefd8163f
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorizar visualmente fábricas de dados do Azure
O Azure Data Factory é um serviço de integração de dados baseado na cloud que lhe permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI).
Este guia de introdução, irá aprender a monitorizar visualmente pipelines de v2 de fábrica de dados sem ter de escrever uma única linha de código.
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [monitorizar e gerir pipelines na fábrica de dados version1](v1/data-factory-monitor-manage-app.md).

## <a name="monitor-data-factory-v2-pipelines"></a>Monitorizar os pipelines de v2 de fábrica de dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para o painel de fábrica de dados criada no portal do Azure e clique no mosaico 'Monitor & Gerir'. Este procedimento inicia a experiência de monitorização visual do ADF v2.

## <a name="list-view-monitoring"></a>Monitorização de vista de lista

Monitorizar o pipeline e a atividade é executada com uma interface de vista de lista simples. Todas as execuções são apresentadas no fuso horário local do browser. Pode alterar o fuso horário e todos os campos de hora de data serão ajustado para o fuso horário selecionado.  

#### <a name="monitoring-pipeline-runs"></a>Monitorizar a execução de Pipeline
Vista de lista que mostram a cada pipeline executar para os seus pipelines de v2 de fábrica de dados. Colunas incluídas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome de pipeline | Nome do pipeline. |
| Ações | Única ação disponível para ver a atividade é executada. |
| Executar início | Início da execução de pipeline de tempo de data (MM/DD/AAAA HH: mm: SS AM/PM) |
| Duração | Duração de execução (hh: mm:) |
| Acionada por | Acionamento manual, acionador da agenda |
| Estado | Não foi possível, foi concluída com êxito, em curso |
| Parâmetros | Executar o pipeline parâmetros (nome, pares de valor) |
| Erro | Pipeline executar erro (se/qualquer) |
| Execute o ID | ID do pipeline executar |

![Monitorizar o pipeline é executada](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Execuções de atividade de monitorização
Vista de lista que mostram a execuções de atividade correspondente a cada execução de pipeline. Clique em **'Atividade é executada'** ícone sob o **'Ações'** coluna para ver a atividade é executada para cada execução de pipeline. Colunas incluídas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da atividade | Nome da atividade no pipeline. |
| Tipo de atividade | Tipo de atividade ou seja, o cópia, HDInsightSpark, HDInsightHive etc. |
| Executar início | Início de execução da atividade tempo data (MM/DD/AAAA HH: mm: SS AM/PM) |
| Duração | Duração de execução (hh: mm:) |
| Estado | Não foi possível, foi concluída com êxito, em curso |
| Input | Matriz JSON que descrevem as entradas de atividade |
| Saída | Matriz JSON que descrevem as saídas da atividade |
| Erro | Atividade executar erro (se/qualquer) |

![Execuções de atividade do monitor](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Tem de clicar em **'Atualizar'** ícone na parte superior para atualizar a lista de execuções de atividade e o pipeline. Atualização automática não é atualmente suportada.
>

![Atualizar](media/monitor-visually/refresh.png)

## <a name="features"></a>Funcionalidades

#### <a name="rich-ordering-and-filtering"></a>A ordenação avançada e filtragem

Pipeline de ordem é executada desc/asc ao iniciar a execução e o pipeline de filtro é executada pelas seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome de pipeline | Nome do pipeline. As opções incluem os filtros rápidos para 'últimas 24 horas', 'Última semana', 'últimos 30 dias' ou selecione uma data personalizado hora. |
| Executar início | Início da execução de pipeline data hora |
| Estado de execução | Filtro é executado por Estado ou seja, com êxito, falha, em curso |

![Filtro](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Adicionar/remover colunas para a vista de lista
Clique com o botão direito do rato no cabeçalho de vista de lista e escolha as colunas que pretende que sejam apresentados na vista de lista

![colunas](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Reordenar larguras de coluna na vista de lista
Aumentar e diminuir as larguras de coluna na vista de lista por cima simplesmente no cabeçalho da coluna

#### <a name="select-data-factory"></a>Selecione a fábrica de dados
Paire o rato sobre o ícone de fábrica de dados na parte superior esquerda. Clique no ícone de 'seta para' para ver uma lista de fábricas de dados e as subscrições do azure que pode monitorizar.

![Selecione a fábrica de dados](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Tours orientada
Clique em 'Ícone informações' na parte inferior esquerda e clique em 'Orientado Tours' para obter instruções passo a passo sobre como monitorizar o pipeline e a atividade executa.

![Tours orientadas](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Comentários
Clique no ícone 'Comentários' para fornecer comentários em várias funcionalidades ou quaisquer problemas que poderá funcionar.

![Comentários](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Passos seguintes

Consulte [monitorizar e gerir pipelines programaticamente](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) artigo para saber mais sobre como monitorizar e gerir pipelines
