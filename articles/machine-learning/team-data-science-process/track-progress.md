---
title: "Execução de projetos de ciência de dados - Azure Machine Learning | Microsoft Docs"
description: "Como um scientist dados acompanhar o progresso de um projeto de ciência de dados."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: c8e1882570a8cfcb4a75f0904a2138d007e0bc5b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="track-progress-of-data-science-projects"></a>Controlar o progresso de projetos de ciência de dados

Gestores de grupo de ciência de dados, clientes potenciais clientes potenciais de equipa e a necessidade de clientes potenciais clientes potenciais de projeto para acompanhar o progresso dos seus projetos de equipa, o trabalho tiver sido realizado nos mesmos e por quem e permanece na lista de ações pendentes. 

## <a name="vsts-dashboards"></a>VSTS dashboards
Se estiver a utilizar serviços de equipa do Visual Studio (VSTS), é capaz de criar dashboards para controlar as atividades e os itens de trabalho associados um determinado projeto seja ágil. 

Para obter mais informações sobre como criar e personalizar os dashboards e widgets no Visual Studio Team Services, consulte os seguintes conjuntos de instruções:

- [Adicionar e gerir dashboards](https://docs.microsoft.com/vsts/report/dashboards/dashboards)
- [Adicionar widgets a um dashboard](https://docs.microsoft.com/vsts/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Dashboard de exemplo

Eis um dashboard de exemplo simples que é criado para controlar as atividades de sprint de um projeto de ciência de dados seja ágil, bem como o número de consolidações para repositórios associados. O **principais esquerda** painel mostra:

- a contagem decrescente de sprint atual, 
- o número de consolidações de cada repositório nos últimos 7 dias
- o item de trabalho para utilizadores específicos. 

Os painéis restantes mostram o diagrama de fluxo cumulativo (CFD), burndown e burnup para um projeto:

- **Na parte inferior esquerda**: CFD a quantidade de trabalho num determinado Estado, Mostrar aprovados em cinzento, consolidada azul e efetuada a verde.
- **Principais direito**: burndown gráfico o trabalho restante para concluir versus o tempo restante).
- **Na parte inferior direita**: burnup gráfico o trabalho que foi concluído em comparação com a quantidade total de trabalho.

![dashboard](./media/track-progress/dashboard.png)

Para obter uma descrição de como criar estes gráficos, consulte os inícios rápidos e tutoriais em [Dashboards](https://docs.microsoft.com/vsts/report/dashboards/).
 
## <a name="next-steps"></a>Passos seguintes

Instruções que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) artigo. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 
