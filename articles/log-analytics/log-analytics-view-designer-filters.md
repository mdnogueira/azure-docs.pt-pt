---
title: Filtros nas vistas do Log Analytics do Azure | Microsoft Docs
description: "Um filtro numa vista de análise de registos permite aos utilizadores filtrar os dados na vista pelo valor de uma propriedade específica sem modificar a vista de si próprio.  Este artigo descreve como utilizar um filtro e adicione uma a uma vista personalizada."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="filters-in-log-analytics-views"></a>Filtros nas vistas de análise de registos
A **filtro** num [ver análise de registos](log-analytics-view-designer.md) permite aos utilizadores filtrar os dados na vista pelo valor de uma propriedade específica sem modificar a vista de si próprio.  Por exemplo, pode permitir que os utilizadores da sua vista para filtrar a vista de dados apenas a partir de um computador específico ou um conjunto de computadores.  Pode criar vários filtros numa única vista para permitir aos utilizadores filtrar por várias propriedades.  Este artigo descreve como utilizar um filtro e adicione uma a uma vista personalizada.

## <a name="using-a-filter"></a>Utilizando um filtro
Clique em **filtro** para abrir o painel de filtro para uma vista.  Isto permite-lhe selecionar um intervalo de tempo e os valores para quaisquer filtros que estão disponíveis para a vista.  Quando seleciona um filtro, apresenta uma lista de valores disponíveis.  Pode selecionar um ou mais valores ou escreva-las no. A vista é atualizada automaticamente para filtrar os valores que especificar. 

Se nenhum valor para um filtro, não está aplicado esse filtro para a vista.  Se remover todos os valores para um filtro, esse filtro já não será aplicado.


![Exemplo de filtro](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>Criar um filtro

Criar um filtro do **filtros** separador quando [editar uma vista](log-analytics-view-designer.md).  O filtro é global para a vista e aplica-se a todas as partes na vista.  

![Definições de filtro](media/log-analytics-view-designer/filters-settings.png)

A tabela seguinte descreve as definições para um filtro.

| Definição | Descrição |
|:---|:---|
| Nome do campo | Nome do campo utilizado para filtragem.  Isto deve corresponder ao campo summarize em **consulta para os valores**. |
| Consulta para os valores | Consulta seja executada para preencher a lista pendente de filtro para o utilizador.  Este tem de utilizar um [resumir](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) ou [distintos](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) para fornecer valores exclusivos para um determinado campo e têm de corresponder a **o nome do campo**.  Pode utilizar [ordenação](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) para ordenar os valores que são apresentados ao utilizador. |
| Etiqueta | Nome para o campo que é utilizado em consultas que suporta o filtro e também é apresentado ao utilizador. |

### <a name="examples"></a>Exemplos

A tabela seguinte inclui alguns exemplos de filtros comuns.  

| Nome do campo | Consulta para os valores | Etiqueta |
|:--|:--|:--|
| Computador   | Heartbeat &#124; distintos computador &#124; Ordenar por computador asc | Computadores |
| EventLevelName | Evento &#124; EventLevelName distinto | Gravidade |
| Nível de gravidade | Syslog &#124; nível de gravidade distinto | Gravidade |
| SvcChangeType | ConfigurationChange &#124; svcChangeType distinto | ChangeType |


## <a name="modify-view-queries"></a>Modificar consultas de vista

Para um filtro têm qualquer efeito, tem de modificar todas as consultas na vista para filtrar valores selecionados.  Se não modificar todas as consultas na vista de quaisquer valores que o utilizador seleciona terá qualquer efeito.

A sintaxe para utilizar um valor de filtro numa consulta é: 

    where ${filter name}  

Por exemplo, se a vista com uma consulta os eventos de devolve e utiliza um filtro chamado computadores, pode utilizar o seguinte.

    Event | where ${Computers} | summarize count() by EventLevelName

Se tiver adicionado filtro outro chamado gravidade, pode utilizar a seguinte consulta para utilizar ambos os filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [partes de visualização](log-analytics-view-designer-parts.md) pode adicionar à sua vista personalizada.