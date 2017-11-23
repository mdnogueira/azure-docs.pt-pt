---
title: "Funcionalidade de seleção no processo de ciência de dados de equipa | Microsoft Docs"
description: "Explica o objectivo de seleção de funcionalidades e fornece exemplos da sua função no processo de melhoramento de dados de aprendizagem."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: c69e7a7e86ccea2073209eabe123049e59a2b8e3
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de características no Team Data Science Process (TDSP)
Este artigo explica o efeito de seleção de funcionalidades e fornece exemplos da sua função no processo de melhoramento de dados de aprendizagem. Estes exemplos são desenhados a partir do Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A seleção das funcionalidades e de engenharia é uma parte da equipa de dados de ciência processo (TDSP) descritos no artigo [o que é o processo de ciência de dados de equipa?](overview.md). Engenharia da funcionalidade e seleção de são partes do **desenvolver funcionalidades** passo o TDSP.

* **engenharia da funcionalidade**: Este processo tenta criar funcionalidades relevantes adicionais a partir as funcionalidades existentes não processadas nos dados de e para aumentar a capacidade preditiva para o algoritmo do learning.
* **seleção de funcionalidade**: Este processo seleciona o subconjunto das funcionalidades de dados original chave numa tentativa para reduzir dimensionalidade do problema formação.

Normalmente **engenharia da funcionalidade** é aplicado primeiro ao gerar funcionalidades adicionais e, em seguida, o **seleção de funcionalidade** passo é realizado para eliminar irrelevantes, redundantes ou altamente correlacionadas funcionalidades.

## <a name="filter-features-from-your-data---feature-selection"></a>Funcionalidades de filtro de dados - seleção de funcionalidades
Seleção de funcionalidade é um processo que normalmente é aplicado para a construção de conjuntos de dados de formação para tarefas de modelação preditiva como tarefas de classificação ou regressão. O objetivo consiste em selecionar um subconjunto das funcionalidades do conjunto de dados original que reduzem o respetivas dimensões utilizando um conjunto mínimo de funcionalidades para representar a quantidade máxima de variância a partir dos dados. Este subconjunto das funcionalidades é utilizado para preparar o modelo. A seleção de funcionalidades serve duas finalidades principais.

* Em primeiro lugar, a seleção de funcionalidades, muitas vezes, aumenta a precisão da classificação através da eliminação irrelevantes, redundante ou altamente correlacionadas funcionalidades.
* Segundo,-diminui o número de funcionalidades, o que torna o processo de preparação do modelo mais eficiente. Eficiência é particularmente importante para learners são dispendiosas preparar, tais como máquinas de vetor de suporte.

Apesar da seleção de funcionalidades de pesquisa reduzir o número de funcionalidades no conjunto de dados utilizados para preparar o modelo, este não é referido pelo termo "redução dimensionalidade". Métodos de seleção de funcionalidade extrair um subconjunto das funcionalidades originais nos dados sem alterá-los.  Métodos de redução dimensionalidade utilizam funcionalidades foi desenvolvidas que podem transformar as funcionalidades originais e, por conseguinte, modificá-las. Exemplos de métodos de redução dimensionalidade incluem Principal Componente de análise, análise de correlação canónico e decomposição de valor único.

Entre outras, categoria amplamente aplicada um dos métodos de seleção de funcionalidade num contexto supervisionado é denominada "seleção de funcionalidade com base em filtro". Através da avaliação de correlação entre cada funcionalidade e o atributo de destino, estes métodos, aplicam-se uma medida de análises para atribuir uma classificação para cada funcionalidade. As funcionalidades, em seguida, estão ordenadas pela classificação, que pode ser utilizada para ajudar a definir o limiar para manter ou eliminar uma funcionalidade específica. Exemplos das medidas de estatísticas utilizados nestes métodos incluem correlação, informações mútua e o teste ao quadrado ho pessoa.

No Azure Machine Learning Studio, são fornecidos para a seleção de funcionalidades de módulos. Como é mostrado na figura seguinte, estes módulos incluem [filtro com base na seleção de funcionalidades] [ filter-based-feature-selection] e [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis].

![Exemplo de seleção de funcionalidade](./media/select-features/feature-Selection.png)

Considere, por exemplo, a utilização do [filtro com base na seleção de funcionalidades] [ filter-based-feature-selection] módulo. Para sua comodidade, continue a utilizar o exemplo de extração de texto. Partem do princípio de que pretende criar um modelo de regressão depois de um conjunto de 256 funcionalidades são criadas através do [funcionalidade hash] [ feature-hashing] módulo, e que a variável de resposta a "Col1" que contém as classificações de revisão de livro entre 1 e 5. Ao definir a "Funcionalidade o método de classificação" para ser "Pearson correlação", a coluna"destino" "Col1" e o "número de funcionalidades pretendidos" a 50 carateres. Em seguida, o módulo [filtro com base na seleção de funcionalidades] [ filter-based-feature-selection] produz um conjunto de dados com 50 funcionalidades juntamente com o atributo de destino "Col1". A figura seguinte mostra o fluxo desta fase experimental e os parâmetros de entrada:

![Exemplo de seleção de funcionalidade](./media/select-features/feature-Selection1.png)

A figura seguinte mostra os conjuntos de dados resultantes:

![Exemplo de seleção de funcionalidade](./media/select-features/feature-Selection2.png)

Cada funcionalidade é classificada com base na correlação Pearson entre si próprio e o atributo de destino "Col1". As funcionalidades com pontuações superiores são mantidas.

As pontuações correspondentes das funcionalidades selecionadas são mostradas na figura seguinte:

![Exemplo de seleção de funcionalidade](./media/select-features/feature-Selection3.png)

Ao aplicar este [filtro com base na seleção de funcionalidades] [ filter-based-feature-selection] módulo, 50 fora de 256 funcionalidades estão selecionadas porque têm as funcionalidades mais correlacionadas com a variável de destino "Col1", com base no método de classificação "Pearson correlação".

## <a name="conclusion"></a>Conclusão
Engenharia da funcionalidade e a seleção de funcionalidades são dois normalmente foi desenvolvido e funcionalidades selecionadas aumentam a eficiência do processo de formação que tenta extrair as informações da chave incluída nos dados. Também podem melhorar o power destes modelos de classificar os dados de entrada com precisão e prever resultados de interesse mais robustly. Engenharia da funcionalidade e a seleção também podem combinar para tornar a aprendizagem mais viáveis tractable. Isto é feito através da Otimização e, em seguida, reduzir o número de funcionalidades necessárias para calibrar os ou preparar um modelo. Matematicamente falando, as funcionalidades selecionadas para preparar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões de dados e, em seguida, prever resultados com êxito.

Não é sempre necessariamente para efetuar a seleção de engenharia ou funcionalidade de funcionalidades. Se for necessário ou não depende dos dados recolhidos, o algoritmo selecionado e o objetivo da experimentação.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

