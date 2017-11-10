---
title: "Fase de implementação, o ciclo de vida do processo de ciência de dados de equipa - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de implementação dos seus projetos de ciência de dados"
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
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: 45d801bf0096879143f91feb230445625559379f
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="deployment"></a>Implementação

Este artigo descreve os objetivos, tarefas e materiais a entregar associada à implementação de equipa dados ciência processo (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

   1. **Compreensão de negócio**
   2. **Aquisição de dados e a compreensão**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação de cliente**

Eis uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
Implemente modelos com um pipeline de dados para um produção ou ambiente de produção como para a aceitação do utilizador final. 

## <a name="how-to-do-it"></a>Como fazê-lo
A tarefa principal resolvida nesta fase:

**Operacionalizar o modelo**: implementar o modelo e o pipeline um produção ou ambiente de produção como para consumo de aplicação.

### <a name="operationalize-a-model"></a>Operacionalize um modelo
Depois de ter um conjunto de modelos que efetuar corretamente, pode operacionalizá-las para outras aplicações consumir. Consoante os requisitos de negócio, predições efetuadas em tempo real ou numa base de batch. Para implementar modelos, exponha-com uma interface de API aberta. A interface permite que o modelo a facilmente ser consumidos de várias aplicações, tais como:

   * Web sites online
   * Folhas de cálculo 
   * Dashboards
   * Aplicações de linha de negócio 
   * Aplicações de back-end 

Para obter exemplos de operationalization de modelo com um serviço web Azure Machine Learning, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). É uma melhor prática criar telemetria e monitorização para o modelo de produção e o pipeline de dados que implementar. Esta prática ajuda-o com o estado do sistema subsequentes relatórios e resolução de problemas.  

## <a name="artifacts"></a>Artefactos

* Um dashboard de estado que mostra as métricas de estado de funcionamento e a chave de sistema
* Um relatório de modelação final com detalhes de implementação
* Um documento de arquitetura de solução final


## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do TDSP:

   1. [Compreensão de negócio](lifecycle-business-understanding.md)
   2. [Aquisição de dados e a compreensão](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação de cliente](lifecycle-acceptance.md)

Fornecemos instruções completa ponto-a-ponto que demonstram todos os passos no processo de cenários específicos. O [instruções de exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com ligações e descrições em miniatura. As instruções mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](http://aka.ms/datascienceprocess).
