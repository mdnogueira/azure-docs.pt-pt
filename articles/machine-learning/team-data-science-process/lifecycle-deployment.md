---
title: "Fase de implementação da equipa de dados de ciência processo ciclo de vida - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de implementação dos seus projetos de ciência de dados."
services: machine-learning
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
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>Implementação

Este tópico descreve os objetivos, tarefas, e associados à materiais a entregar o **implementação** do processo de ciência de dados de equipa. Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

* **Compreensão de negócio**
* **Aquisição de dados e a compreensão**
* **Modelação**
* **Implementação**
* **Aceitação de cliente**

Eis uma representação visual do **ciclo de vida do processo de ciência de dados de equipa**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
* Modelos com um pipeline de dados são implementados num produção ou ambiente de produção como para a aceitação do utilizador final. 

## <a name="how-to-do-it"></a>Como fazê-lo
A tarefa principal resolvida nesta fase:

* **Operacionalizar o modelo**: implementar o modelo e o pipeline um produção ou ambiente de produção como para consumo de aplicação.

### <a name="41-operationalize-a-model"></a>4.1 operacionalizar um modelo
Depois de ter um conjunto de modelos que efetuar corretamente, pode ser operationalized para outras aplicações consumir. Consoante os requisitos de negócio, predições efetuadas em tempo real ou numa base de batch. Modelos são implementados da exposição-los com uma interface de API aberta. A interface permite que o modelo a facilmente ser consumidos de várias aplicações, tais como Web sites online, folhas de cálculo, dashboards ou de linha de negócio e back-end de aplicações. Para obter exemplos de operationalization de modelo com um serviço web Azure Machine Learning, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). Também é uma melhor prática criar telemetria e monitorização para o modelo de produção e o pipeline de dados que são implementadas. Esta prática ajuda-o com o estado do sistema subsequentes relatórios e resolução de problemas.  

## <a name="artifacts"></a>Artefactos
* Dashboard de estado de métricas de estado de funcionamento e a chave de sistema.
* Relatório de modelação final com detalhes de implementação.
* Documento de arquitetura de solução final.


## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do processo de ciência de dados de equipa:

* [1. Compreensão de negócio](lifecycle-business-understanding.md)
* [2. Aquisição de dados e a compreensão](lifecycle-data.md)
* [3. Modelação](lifecycle-modeling.md)
* [4. Implementação](lifecycle-deployment.md)
* [5. Aceitação de cliente](lifecycle-acceptance.md)

Total de instruções ponto-a-ponto que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) tópico. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte o [com o Azure ML](http://aka.ms/datascienceprocess) percurso de aprendizagem.