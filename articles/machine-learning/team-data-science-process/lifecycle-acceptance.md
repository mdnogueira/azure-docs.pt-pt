---
title: "Fase de aceitação de cliente da equipa de dados de ciência processo ciclo de vida - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de aceitação de cliente dos seus projetos de ciência de dados."
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>Aceitação do cliente

Este tópico descreve os objetivos, tarefas, e associados à materiais a entregar o **fase de aceitação de cliente** do processo de ciência de dados de equipa. Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

* **Compreensão de negócio**
* **Aquisição de dados e a compreensão**
* **Modelação**
* **Implementação**
* **Aceitação de cliente**

Eis uma representação visual do **ciclo de vida do processo de ciência de dados de equipa**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
* **Finalizar a concentrarem projeto**: Confirme que o pipeline, o modelo e os respetivos implementação num ambiente de produção estão que satisfaçam os objetivos do cliente.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem duas tarefas principais resolvidas nesta fase:

* **Validação de sistema**: confirmar o modelo implementado e o pipeline são satisfazer as necessidades do cliente.
* **Projeto mão-desativar**: à entidade que está a ser executado o sistema na produção.

O cliente deve validar se o sistema cumpre as suas necessidades de negócio e as respostas as perguntas com precisão aceitável para implementar o sistema para a produção para utilizam pela sua aplicação de cliente. Toda a documentação está finalizada e revista. Um mão-desativar do projeto para a entidade responsável por operações de conclusão. Esta entidade dever-se, por exemplo, um IT ou equipa de ciência de dados de cliente ou um agente do cliente que é responsável pela execução do sistema em produção. 

## <a name="artifacts"></a>Artefactos
O artefacto principal produzido nesta fase final é o **saída relatório do projeto de cliente**. Este é o relatório técnico que contém todos os detalhes do projeto que é útil para saber mais sobre e operar o sistema. Um [saída relatório](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) modelo é fornecido pela TDSP que pode ser utilizado tal como está ou personalizada para necessidades do cliente específico. 


## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do processo de ciência de dados de equipa:

* [1. Compreensão de negócio](lifecycle-business-understanding.md)
* [2. Aquisição de dados e a compreensão](lifecycle-data.md)
* [3. Modelação](lifecycle-modeling.md)
* [4. Implementação](lifecycle-deployment.md)
* [5. Aceitação de cliente](lifecycle-acceptance.md)

Total de instruções ponto-a-ponto que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) tópico. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte o [com o Azure ML](http://aka.ms/datascienceprocess) percurso de aprendizagem.