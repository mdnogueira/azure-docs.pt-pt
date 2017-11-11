---
title: Limpar e preparar dados do Azure Machine Learning | Microsoft Docs
description: "Pré-processar e limpar dados para o preparar para o machine learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: bdf659ec-4881-4324-8b9c-747cbfa0c3cd
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 7f0c1f0f549e746cc99db3b47f6c90bb51145d5d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar dados para machine learning otimizado
Pré-processadas e limpeza de dados são tarefas importantes que, normalmente, tem de ser a realização de um antes do conjunto de dados pode ser utilizado de forma eficaz para o machine learning. Dados não processados é frequentemente inúteis e pouco fiáveis e poderão estar em falta valores. Utilizar esses dados para a modelação pode produzir resultados enganosa. Estas tarefas fazem parte da equipa de dados de ciência processo (TDSP) e, normalmente, siga uma exploração inicial de um conjunto de dados utilizado para detetar e planear o pré-processamento de necessário. Para obter instruções sobre o processo TDSP mais detalhadas, consulte os passos descritos no [o processo de ciência de dados de equipa](overview.md).

Processamento de pré-lançamento e limpeza tarefas, como a tarefa de exploração de dados, podem ser executadas numa grande variedade de ambientes de, tais como SQL ou Hive ou do Azure Machine Learning Studio e com várias ferramentas e idiomas, tais como R ou Python, consoante onde os dados são armazenados e como é formatado. Uma vez que TDSP iterativo natureza, estas tarefas podem ocorrer em vários passos no fluxo de trabalho do processo.

Este artigo apresenta várias tarefas que podem ser undertaken antes ou depois de ingestão de dados no Azure Machine Learning e conceitos de processamento de dados.

Para obter um exemplo de exploração de dados e pré-processamento de concluído dentro do Azure Machine Learning studio, consulte o [processar previamente os dados no Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) vídeo.

## <a name="why-pre-process-and-clean-data"></a>Por que motivo pré-processar e limpar dados?
Recolha de dados do mundo real de várias origens e processos e podem conter irregularities ou dados danificados comprometer a qualidade do conjunto de dados. Os problemas de qualidade de dados típicas que surgir são:

* **Incompleta**: dados não contém atributos ou que contém valores em falta.
* **Inúteis**: os dados contêm registos errados ou valores atípicos.
* **Inconsistente**: dados contém registos em conflito ou discrepâncias.

Dados de qualidade são um pré-requisito para modelos preditivos de qualidade. Para evitar "libertação da memória no lixo fora" e melhorar a qualidade dos dados e, por conseguinte, do modelo desempenho, é imperativo para realizar um ecrã de estado de funcionamento de dados para detetar problemas de dados numa fase inicial e opte por utilizar o processamento de dados correspondente e passos de limpeza.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são alguns ecrãs de estado de funcionamento de típico de dados que estão a ser utilizadas?
Pode verificamos a qualidade dos dados geral verificando:

* O número de **registos**.
* O número de **atributos** (ou **funcionalidades**).
* O atributo **tipos de dados** (nominal, ordinal ou contínua).
* O número de **valores em falta**.
* **Formação** dos dados.
  * Se os dados estão no TSV ou CSV, certifique-se de que os separadores de coluna e separadores de linha sempre corretamente separam as colunas e linhas.
  * Se os dados estão no formato HTML ou XML, verifique se os dados de está bem formados com base no respetivas normas respetivas.
  * Análise também poderá ser necessária para extrair informações estruturadas de dados semiestruturados ou não estruturados.
* **Registos de dados inconsistentes**. Verifique o intervalo de valores são permitidos. Por exemplo, se os dados contêm estudante GPA, verifique se o GPA está dentro do intervalo designado, diga 0 ~ 4.

Quando encontrar problemas com dados, **processamento passos** são necessárias que muitas vezes envolve a limpeza valores em falta, discretização a normalização de dados, processamento de texto para remover e/ou substituir os carateres que poderão afetar os dados incorporados alinhamento, mistos tipos de dados em comum campos e outros.

**O Azure Machine Learning consome dados em tabela formados**.  Se os dados já estão em formato tabular, pré-o processamento de dados pode ser executado diretamente com o Azure Machine Learning no Machine Learning Studio.  Se os dados não estão em formato tabular, indiquem faz parte de XML, a análise poderá ser necessária para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das tarefas principais na pré-o processamento de dados?
* **Limpeza de dados**: preencha ou valores em falta, detetar e remover dados inúteis e valores atípicos.
* **Transformação de dados**: normalizar os dados para reduzir as dimensões e ruído.
* **Redução de dados**: registos de dados ou os atributos de processamento mais fácil de dados de exemplo.
* **Discretização dados**: converter os atributos contínuos para atributos categórico facilidade de utilização com determinadas métodos de aprendizagem máquina.
* **Limpeza de texto**: remova os caracteres incorporados que podem fazer com que o desalinhamento de dados, para por exemplo, separadores incorporados num ficheiro de valores separados por separador dados, incorporadas novas linhas que poderão quebrar registos, etc.

As secções abaixo de detalhe alguns destes passos de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com os valores em falta?
Para lidar com os valores em falta, é melhor primeiro identificar o motivo para os valores em falta para o identificador de melhor o problema. Os métodos de processamento de valor em falta típicos são:

* **Eliminação**: remover registos com os valores em falta
* **Fictício substituição**: substitua os valores em falta um valor fictício: por exemplo, *desconhecido* para categórico ou 0 para os valores de numérico.
* **Significa que a substituição**: se os dados em falta são numéricos, substitua os valores em falta a média.
* **Substituição de induzirem frequentes**: se os dados em falta são categórico, substitua os valores em falta com o item mais frequente
* **Substituição de regressão**: utilizar um método de regressão para substituir os valores em falta com valores regressed.  

## <a name="how-to-normalize-data"></a>Como normalizar os dados?
Normalização de dados novamente dimensiona numérico valores para um intervalo especificado. Os métodos de normalização de dados populares incluem:

* **Máx. de min normalização**: forma linear transformar os dados para um intervalo, diga entre 0 e 1, onde o valor mínimo é dimensionado para 0 e o máximo valor para 1.
* **Z-pontuação normalização**: Dimensionar com base em média e o desvio-padrão de dados: a diferença entre os dados e a média de divisão pelo desvio-padrão.
* **Dimensionamento decimal**: dimensionar os dados ao mover a vírgula decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como discretizar dados?
Podem ser discretizados dados ao converter valores contínuos para atributos nominal ou intervalos. Seguem-se algumas formas de fazê-lo:

* **A Discretização igual largura**: dividir o intervalo de todos os valores possíveis de um atributo em N grupos do mesmo tamanho e atribua os valores que se inserem num bin com o número de reciclagem.
* **Altura igual a Discretização**: dividir o intervalo de todos os valores possíveis de um atributo em N grupos, cada um possuindo o mesmo número de instâncias, em seguida, atribuir os valores que se inserem num bin com o número de reciclagem.  

## <a name="how-to-reduce-data"></a>Como reduzir dados?
Existem vários métodos para reduzir o tamanho dos dados para processar os dados mais fácil. Dependendo do tamanho dos dados e o domínio, podem ser aplicados os seguintes métodos:

* **Registe a amostragem**: os registos de dados de exemplo e só escolher o subconjunto representativo a partir dos dados.
* **Atributo amostragem**: selecione apenas um subconjunto dos atributos mais importantes a partir dos dados.  
* **Agregação**: dividir os dados em grupos e armazenar os números para cada grupo. Por exemplo, os números de receitas diária de uma cadeia de restaurante ao longo dos últimos anos 20 podem ser agregados a receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como a limpeza de dados de texto?
**Campos de texto em dados de tabela** pode incluir carateres que afetam os limites de alinhamento e/ou de registo de colunas. Por exemplo, incorporada separadores desalinhamento de coluna de causa um ficheiro de valores separados por separador e incorporados carateres de nova linha quebrar linhas registos. Processamento de codificação de texto incorrecto ao texto de escrita/leitura leva a perda de informações, inadvertida introdução de ilegíveis carateres, por exemplo, valores nulos, e pode também efeito texto analisar. Tenha o cuidado de análise e editar poderão ser necessárias para limpar os campos de texto para alinhamento adequado e/ou para extrair estruturado dados a partir dos dados não estruturados ou semiestruturados texto.

**Exploração de dados** oferece uma vista antecipada para os dados. Um número de problemas de dados pode ser uncovered durante este passo e métodos correspondentes podem ser aplicados a resolver esses problemas.  É importante fazer perguntas, tais como o que é a origem do problema e como o problema poderá ter sido introduzido. Isto também ajuda a decidir os passos de processamento de dados que necessitam de seguimento resolvê-los. O tipo de informações uma pretende derivar os dados também pode ser utilizado para definir prioridades o esforço de processamento de dados.

## <a name="references"></a>Referências
> *Extração de dados: Conceitos e técnicas*, edição terceira, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei
> 
> 

