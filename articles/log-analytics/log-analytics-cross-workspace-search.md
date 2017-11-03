---
title: "Executar consultas em áreas de trabalho do Log Analytics do Azure | Microsoft Docs"
description: "Este artigo descreve como pode consultar em várias áreas de trabalho na sua subscrição com exemplos a seguir."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Executar consultas em várias áreas de trabalho de análise de registos

Anteriormente com Log Analytics do Azure, foi apenas analisa dados a partir da área de trabalho atual e este limitado a capacidade de consulta em várias áreas de trabalho definidas na sua subscrição.  

Agora pode consultar em várias áreas de trabalho, fornecer uma vista de todo sistema dos seus dados.  Este tipo de consulta só pode executar o [portal avançada](log-analytics-log-search-portals.md#advanced-analytics-portal), não no portal do Azure.  

## <a name="querying-across-log-analytics-workspaces"></a>Consultar em áreas de trabalho de análise de registos
Para fazer referência a outra área de trabalho na sua consulta, utilize o *área de trabalho* identificador.  Por exemplo, a seguinte consulta devolve resumidas contagens de atualizações necessárias pela respetiva classificação da tabela atualização tanto a partir da área de trabalho atual, outra área de trabalho com o nome *contosoretail-it*.  


## <a name="identifying-resources"></a>Identificar recursos
Identificar uma área de trabalho pode ser executada uma das várias formas:

* Nome de recurso - é um nome legível por humanos da área de trabalho, por vezes referido como *nome do componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Identificar uma área de trabalho pelo respetivo nome parte do princípio que seja exclusivo em todas as subscrições acessíveis. Se tiver várias aplicações com o nome especificado, a consulta falha devido a ambiguidade. Neste caso, tem de utilizar um do outros identificadores.

* Nome qualificado - é o nome"completo" da área de trabalho, composta pelo nome da subscrição, grupo de recursos e o componente de nome neste formato: *resourceGroup/subscriptionName/componentName*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Porque os nomes de subscrição do Azure não são exclusivos, este identificador pode ser ambígua. 
    >

* ID da área de trabalho - um ID de área de trabalho é o identificador exclusivo, imutável, atribuído a cada área de trabalho representada como um identificador exclusivo global (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Recurso ID de Azure – identidade exclusiva definido pelo Azure da área de trabalho. Utilize este quando o nome de recurso é ambíguo.  Para áreas de trabalho, é o formato: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Áreas de trabalho/OperationalInsights/componentName*.  

    Por exemplo:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Passos seguintes

Reveja o [referência de pesquisa de registo de análise de registos](https://docs.loganalytics.io/docs/Language-Reference) para ver todas as opções de sintaxe de consulta disponíveis na análise de registos.    