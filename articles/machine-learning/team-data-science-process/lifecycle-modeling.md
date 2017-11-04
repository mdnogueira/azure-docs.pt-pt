---
title: "Modelação fase equipa dados ciência processo ciclo de vida - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de modelação dos seus projetos de ciência de dados."
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
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="modeling"></a>Modelação

Este tópico descreve os objetivos, tarefas, e associados à materiais a entregar o **modelação fase** do processo de ciência de dados de equipa. Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

* **Compreensão de negócio**
* **Aquisição de dados e a compreensão**
* **Modelação**
* **Implementação**
* **Aceitação de cliente**

Eis uma representação visual do **ciclo de vida do processo de ciência de dados de equipa**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Funcionalidades de dados ideal para o modelo de machine learning.
* Um modelo de ML informativo que prevê com mais exatidão o destino.
* Um modelo de ML é adequado para produção.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem três tarefas principais resolvidas nesta fase:

* **Engenharia da funcionalidade**: criar as funcionalidades de dados a partir dos dados não processados para facilitar a preparação do modelo.
* **Modelo de formação**: localizar o modelo que responde à pergunta com mais exatidão comparando as métricas de sucesso.
* Determinar se o seu modelo **adequado para produção**.

### <a name="31-feature-engineering"></a>3.1 engenharia da funcionalidade
Engenharia da funcionalidade envolve inclusão, agregação e a transformação de variáveis não processadas para criar as funcionalidades utilizadas na análise. Se pretender obter informações sobre o que está a ocasionar a um modelo, terá de compreender como as funcionalidades relacionadas com entre si e a forma como os algoritmos do machine learning estão a utilizar essas funcionalidades. Este passo necessita de uma combinação criativos de conhecimentos de domínio e das informações que obteve no passo de exploração de dados. Este é um ato de equilíbrio de localizar e incluindo variáveis informativos enquanto evita variáveis relacionado com demasiados. Variáveis informativos melhorar o nosso resultado; variáveis relacionado com introduzem ruído desnecessário no modelo. Também terá de gerar estas funcionalidades quaisquer novos dados obtido durante a classificação. Por isso, a geração de uma destas funcionalidades só pode depender de dados que estão disponíveis no momento da classificação. Para obter orientações técnica no engenharia da funcionalidade quando utilizando várias tecnologias de dados do Azure, consulte [engenharia no processo de ciência de dados da funcionalidade](create-features.md). 

### <a name="32-model-training"></a>3.2 modelo de formação
Dependendo do tipo de pergunta que está a tentar resposta, existem muitos algoritmos de modelação disponíveis. Para obter orientações sobre como escolher os algoritmos, consulte [como escolher algoritmos para o Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo foi escrito para o Azure Machine Learning, as orientações fornece é útil para qualquer do machine learning projetos. 

O processo de preparação de modelo inclui os seguintes passos: 

* **Dividir os dados de entrada** aleatoriamente para a modelação para um conjunto de dados de formação e um conjunto de dados de teste.
* **Criar os modelos** utilizando o conjunto de dados de formação.
* **Avaliar** (formação e teste conjunto de dados) uma série de concorrentes algoritmos do machine learning, juntamente com vários associados otimização parâmetros (conhecidos como paramétrico parâmetro) que são adaptados na direção de resposta à pergunta de interesse com os dados atuais.
* **Determinar a "melhor" solução** responder à pergunta comparando a métrica de êxito entre métodos alternativos.

> [!NOTE]
> **Evitar a fuga de**: fugas de dados podem dever-se a inclusão de dados a partir de fora do conjunto de dados de formação que permite a um modelo ou o algoritmo do machine learning para tornar as predições unrealistically boas. Fugas é um motivo comum, por que motivo dados cientistas obtém nervous quando recebem preditivos resultados que parecem demasiado pronto para ser verdadeira. Estas dependências podem ser difícil de detetar. Para evitar a fuga de frequentemente requer iterating entre a criação de um conjunto de dados do Analysis Services, criar um modelo e avaliar a precisão. 
> 
> 

Fornecemos um [ferramenta automatizada modelação e relatórios](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com TDSP é capaz de executar através de vários algoritmos e parâmetros sweeps para produzir um modelo de linha de base. Também produz uma linha de base modelação relatório resumir o desempenho de cada combinação de parâmetros e o modelo, incluindo a importância de variável. Este processo também é iterativo, pode unidade mais engenharia da funcionalidade. 

## <a name="artifacts"></a>Artefactos
Os artefactos produzidos nesta fase incluem:

* [**Conjuntos de funcionalidades**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): as funcionalidades desenvolvidas para a modelação descritas o **conjuntos de funcionalidades** secção o **definição dos dados** relatório. Contém ponteiros para o código para gerar as funcionalidades e a descrição na forma como a funcionalidade foi gerada.
* [**Modelo de relatório**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo que é experimentado uma norma, é produzido baseado no modelo de relatório, que fornece detalhes sobre cada experimentação.
* **Decisão de ponto de verificação**: avaliar se o modelo está a executar corretamente suficientemente implementá-la a um sistema de produção. Algumas questões a colocar chaves são:
  * O modelo responda à pergunta com confiança suficiente fornecida os dados de teste? 
  * Deve tentar qualquer abordagens alternativas: recolher dados adicionais, não mais engenharia da funcionalidade ou experimente outros algoritmos?

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do processo de ciência de dados de equipa:

* [1. Compreensão de negócio](lifecycle-business-understanding.md)
* [2. Aquisição de dados e a compreensão](lifecycle-data.md)
* [3. Modelação](lifecycle-modeling.md)
* [4. Implementação](lifecycle-deployment.md)
* [5. Aceitação de cliente](lifecycle-acceptance.md)

Total de instruções ponto-a-ponto que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) tópico. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte o [com o Azure ML](http://aka.ms/datascienceprocess) percurso de aprendizagem. 