---
title: "O ciclo de vida do processo de ciência de dados de equipa - Azure | Microsoft Docs"
description: "Os passos necessários para executar os seus projetos de ciência de dados"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: 5d69d4d2371b42b3595cf3dc71d99d913e225c59
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="the-team-data-science-process-lifecycle"></a>O ciclo de vida do processo de ciência de dados de equipa

O processo de ciência de dados de equipa (TDSP) fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os passos, do início ao fim, que projetos siga, normalmente, quando estes são executados. Se utilizar outro ciência de dados do ciclo de vida, tais como cruzada da indústria processo padrão para extração de dados [(CRISP-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), deteção de dados de conhecimento em bases de dados [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), ou o processo personalizado da organização , pode continuar a utilizar o TDSP baseado em tarefas. 

Este ciclo de vida foi concebido para projetos de ciência de dados que foram concebidos para são enviados como parte das aplicações inteligentes. Estas aplicações implementar machine learning ou artificial intelligence modelos para Análise Preditiva. Projetos de ciência de dados exploratórias e projetos de análise ad hoc também podem beneficiar da utilização deste processo. Mas para os projetos, alguns dos passos aqui descritos poderão não ser necessária. 

O ciclo de vida TDSP é constituído por cinco fases principais que são executados iteratively. Estas fases incluem:

   1. [Compreensão de negócio](lifecycle-business-understanding.md)
   2. [Aquisição de dados e a compreensão](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação de cliente](lifecycle-acceptance.md)

Eis uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida TDSP é modelado como uma sequência de passos iterated que fornecem orientações sobre as tarefas necessárias para utilizar modelos preditivos. Implementar os modelos preditivos no ambiente de produção que pretende utilizar para criar aplicações inteligentes. O objetivo deste ciclo de vida do processo é continuar a mover um projeto de ciência de dados para um ponto final do engagement encriptado. Ciência de dados é um exercício na investigação e deteção. A capacidade de comunicar tarefas a sua equipa e os seus clientes através de um conjunto bem definido de artefactos que utilizam modelos padronizados ajuda a evitar desentendimentos. Também é utilizar estes modelos aumenta a probabilidade da conclusão com êxito de um projeto de ciência de dados complexas.

Para cada fase, podemos fornecer as seguintes informações:

   * **Objetivos**: os objetivos específicos.
   * **Como fazê-lo**: uma descrição das tarefas específicas e orientações sobre como conclui-los.
   * **Artefactos**: A concentrarem e o suporte para produzi-los.

## <a name="next-steps"></a>Passos seguintes

Fornecemos instruções completa ponto-a-ponto que demonstram todos os passos no processo de cenários específicos. O [instruções de exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com ligações e descrições em miniatura. As instruções mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](http://aka.ms/datascienceprocess).
