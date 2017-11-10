---
title: "Modelação fase do ciclo de vida o processo de ciência de dados de equipa - Azure | Microsoft Docs"
description: "Os objetivos, tarefas e materiais a entregar para a fase de modelação dos seus projetos de ciência de dados"
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
ms.openlocfilehash: a7fc5f2128e9c13182ca5bd7a6bd61ae41ef775c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="modeling"></a>Modelação

Este artigo descreve os objetivos, tarefas e materiais a entregar associada a fase de modelação de equipa dados ciência processo (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar seus projetos de ciência de dados. O ciclo de vida destaca as fases principais que projetos normalmente executar, muitas vezes, iteratively:

   1. **Compreensão de negócio**
   2. **Aquisição de dados e a compreensão**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação de cliente**

Eis uma representação visual do ciclo de vida TDSP:

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Determine as funcionalidades de dados ideal para o modelo de machine learning.
* Crie um modelo de machine-learning informativo que prevê com mais exatidão o destino.
* Crie um modelo de machine-learning que é adequado para produção.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem três tarefas principais resolvidas nesta fase:

  * **Engenharia da funcionalidade**: criar as funcionalidades de dados a partir dos dados não processados para facilitar a preparação do modelo.
  * **Modelo de formação**: localizar o modelo que responde à pergunta com mais exatidão comparando as métricas de sucesso.
  * Determinar se o seu modelo **adequado para produção.**

### <a name="feature-engineering"></a>Com engenharia
Engenharia da funcionalidade envolve a inclusão, a agregação e a transformação de variáveis não processadas para criar as funcionalidades utilizadas na análise. Se pretender obter informações sobre o que está a ocasionar a um modelo, terá de compreender como as funcionalidades se relacionam entre si e a forma como os algoritmos de machine-learning estão a utilizar essas funcionalidades. 

Este passo necessita de uma combinação criativos de conhecimentos de domínio e as informações que obteve no passo de exploração de dados. Engenharia da funcionalidade é um ato de equilíbrio de localizar e incluindo variáveis informativos, mas ao mesmo tempo evitar demasiados variáveis relacionado com a tentar. Variáveis informativos melhorar o seu resultado; variáveis relacionado com introduzem ruído desnecessário no modelo. Também terá de gerar estas funcionalidades quaisquer novos dados obtido durante a classificação. Como resultado, a geração de uma destas funcionalidades só pode depender de dados que estão disponíveis no momento da classificação. 

Para técnica orientações sobre a funcionalidade de engenharia quando tornar a utilização de várias tecnologias de dados do Azure, consulte [engenharia no processo de ciência de dados da funcionalidade](create-features.md). 

### <a name="model-training"></a>modelo de formação
Dependendo do tipo de pergunta que está a tentar responder, existem muitos algoritmos de modelação disponíveis. Para obter orientações sobre como escolher os algoritmos, consulte [como escolher algoritmos para o Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo utiliza o Azure Machine Learning, as orientações fornece é útil para os projetos de aprendizagem automática. 

O processo de preparação de modelo inclui os seguintes passos: 

   * **Dividir os dados de entrada** aleatoriamente para a modelação para um conjunto de dados de formação e um conjunto de dados de teste.
   * **Criar os modelos** utilizando o conjunto de dados de preparação.
   * **Avaliar** a formação e o conjunto de dados de teste. Utilize uma série de algoritmos de machine-learning concorrentes juntamente com os vários parâmetros de otimização associados (conhecido como um *paramétrico parâmetro*) que são adaptadas na direção de resposta à pergunta de interesse com os dados atuais.
   * **Determinar a "melhor" solução** responder à pergunta comparando as métricas de sucesso entre métodos alternativos.

> [!NOTE]
> **Evitar a fuga de**: pode causar fugas de dados se incluir dados de fora do conjunto de dados de formação que permite a um modelo ou o algoritmo de aprendizagem automática para fazer predições unrealistically boas. Fugas é um motivo comum, por que motivo dados cientistas obtém nervous quando recebem preditivos resultados que parecem demasiado pronto para ser verdadeira. Estas dependências podem ser difícil de detetar. Para evitar a fuga de frequentemente requer iterating entre a criação de um conjunto de dados do Analysis Services, criar um modelo e avaliar a exatidão dos resultados. 
> 
> 

Fornecemos um [automatizada modelação e a ferramenta de relatórios](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com TDSP é capaz de executar através de vários algoritmos e parâmetros sweeps para produzir um modelo de linha de base. Também produz uma linha de base modelação relatório resume o desempenho de cada combinação de parâmetros e o modelo, incluindo a importância de variável. Este processo também é iterativo, pode unidade mais engenharia da funcionalidade. 

## <a name="artifacts"></a>Artefactos
Os artefactos produzidos nesta fase incluem:

   * [Conjuntos de funcionalidades](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): as funcionalidades desenvolvidas para a modelação descritas o **conjuntos de funcionalidades** secção o **definição dos dados** relatório. Contém ponteiros para o código para gerar as funcionalidades e uma descrição de como a funcionalidade foi gerada.
   * [Modelo de relatório](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo que é experimentado uma norma, é produzido baseado no modelo de relatório, que fornece detalhes sobre cada experimentação.
   * **Decisão de ponto de verificação**: avaliar se o modelo suficientemente também efetua implementá-la a um sistema de produção. Algumas questões a colocar chaves são:
     * O modelo responda à pergunta com confiança suficiente fornecida os dados de teste? 
     * Deve tentar qualquer abordagens alternativas? Deve recolher dados adicionais, não mais engenharia da funcionalidade ou experimente outros algoritmos?

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada passo no ciclo de vida do TDSP:

   1. [Compreensão de negócio](lifecycle-business-understanding.md)
   2. [Aquisição de dados e a compreensão](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação de cliente](lifecycle-acceptance.md)

Fornecemos instruções completa ponto-a-ponto que demonstram todos os passos no processo de cenários específicos. O [instruções de exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com ligações e descrições em miniatura. As instruções mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](http://aka.ms/datascienceprocess). 
