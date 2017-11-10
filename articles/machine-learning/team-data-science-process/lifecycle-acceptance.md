---
title: "Fase de aceitação de cliente do ciclo de vida o processo de ciência de dados de equipa - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de aceitação de cliente dos seus projetos de ciência de dados"
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
ms.openlocfilehash: e150b3e7c7e98443264dd5b8aaeda1bfe6047b2c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="customer-acceptance"></a>Aceitação do cliente

Este artigo descreve os objetivos, tarefas e materiais a entregar associada a fase de aceitação de cliente da equipa de dados de ciência processo (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

   1. **Compreensão de negócio**
   2. **Aquisição de dados e a compreensão**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação de cliente**

Eis uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
**Finalizar a concentrarem projeto**: Confirme que o pipeline, o modelo e os respetivos implementação num ambiente de produção satisfazem objetivos do cliente.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem duas tarefas principais resolvidas nesta fase:

   * **Validação de sistema**: Confirme que o modelo implementado e o pipeline satisfazer as necessidades do cliente.
   * **Projeto mão-desativar**: entregam o projeto para a entidade que vai executar o sistema na produção.

O cliente deve validar se o sistema cumpre as suas necessidades de negócio e de que responde a questões com precisão aceitável para implementar o sistema para a produção para utilização por aplicações do seu cliente. Toda a documentação está finalizada e revista. O projeto é entregar-desativar para a entidade responsável pelas operações. Esta entidade pode ser, por exemplo, um IT ou equipa de ciência de dados de cliente ou um agente do cliente que é responsável pela execução do sistema em produção. 

## <a name="artifacts"></a>Artefactos
O artefacto principal produzido nesta fase final é o **sair relatório do projeto do cliente**. Este relatório técnico contém todos os detalhes do projeto que são úteis para saber mais sobre como operar o sistema. TDSP fornece um [sair relatório](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) modelo. Pode utilizar o modelo como está, ou pode personalizá-lo para as necessidades do cliente específico. 


## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do TDSP:

   1. [Compreensão de negócio](lifecycle-business-understanding.md)
   2. [Aquisição de dados e a compreensão](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação de cliente](lifecycle-acceptance.md)

Fornecemos instruções completa ponto-a-ponto que demonstram todos os passos no processo de cenários específicos. O [instruções de exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com ligações e descrições em miniatura. As instruções mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](http://aka.ms/datascienceprocess).
