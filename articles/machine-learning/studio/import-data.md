---
title: Importar dados para o Machine Learning Studio | Microsoft Docs
description: "Como importar os seus dados no Azure Machine Learning Studio de várias origens de dados. Saiba que tipos de dados e formatos de dados são suportados."
keywords: "Importar dados, o formato de dados, os tipos de dados, as origens de dados, dados de formação"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: garye;bradsev
ms.openlocfilehash: eb22f516f298df9396ca809acaa9c8cb62589c2a
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importar os seus dados de preparação para o Azure Machine Learning Studio a partir de várias origens de dados
Para utilizar os seus próprios dados no Machine Learning Studio para desenvolver e dar formação sobre uma solução de Análise Preditiva, pode: 

* carregar dados a partir de um **ficheiro local** antecedência do disco rígido para criar um módulo de conjunto de dados na sua área de trabalho
* aceder a dados a partir de um dos vários **origens de dados online** enquanto a sua experimentação está a ser executado utilizando o [importar dados] [ import-data] módulo 
* utilizar dados a partir de outro Azure Machine learning **experimentação** guardada como um conjunto de dados
* utilizar dados a partir de um local **base de dados do SQL Server**

Cada uma destas opções está descrita em um dos tópicos no menu abaixo. Estes tópicos mostram como importar dados a partir destas diversas origens de dados para utilizar no Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Um número de conjuntos de dados de exemplo estão disponíveis no Machine Learning Studio, pode utilizar para dados de preparação. Para obter informações acerca deste assunto, consulte [utilizar os conjuntos de dados de exemplo no Azure Machine Learning Studio](use-sample-datasets.md)).
> 
> 

Este tópico introdutórias também descreve como obter os dados pronto a utilizar no Machine Learning Studio e descreve os formatos de dados e os tipos de dados são suportados. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Obter dados pronto a utilizar no Azure Machine Learning Studio
O Machine Learning Studio foi concebido para trabalhar com dados retangular ou tabela, tais como os dados de texto delimitado por ou estruturados dados a partir de uma base de dados, embora em algumas circunstâncias não retangular dados podem ser utilizados.

É melhor se os seus dados são relativamente limpo. Ou seja, poderá ser útil para problemas tais como cadeias unquoted asseguramos antes de carregar os dados na sua experiência.

No entanto, existem módulos disponíveis no Machine Learning Studio, que permitem algumas manipulação de dados dentro da sua experimentação. Consoante os algoritmos do machine learning que irá utilizar, poderá ter de decidir como vai processar problemas estruturais de dados, como os valores em falta e dados dispersos e existem módulos que podem ajudar. Procure no **transformação de dados** secção da paleta do módulo para módulos que realizam estas funções.

Em qualquer momento na sua experimentação, pode ver ou transferir os dados que são produzidos por um módulo clicando na porta de saída. Consoante o módulo, poderá transferir diferentes opções disponíveis ou poderá visualizar os dados dentro do seu browser no Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Tipos de dados e formatos de dados suportados
Pode importar um número de tipos de dados para a sua experimentação, consoante o mecanismo de utilizar para importar os dados e onde é proveniente de:

* Texto simples (*.txt)
* Valores separados por vírgulas (CSV) com um cabeçalho (. csv) ou sem (. nh.csv)
* Separador valores separados por (TSV) com um cabeçalho (.tsv) ou sem (. nh.tsv)
* Ficheiro do Excel
* Tabela do Azure
* Tabela do Hive
* Tabela de base de dados SQL
* Valores de OData
* Dados SVMLight (.svmlight) (consulte o [SVMLight definição](http://svmlight.joachims.org/) para obter informações de formato)
* Atributo de dados de formato de ficheiro de relação (ARFF) (.arff) (consulte o [definição ARFF](http://weka.wikispaces.com/ARFF) para obter informações de formato)
* Ficheiro zip (. zip)
* Ficheiro de objeto ou a área de trabalho de R (. RData)

Se importar dados num formato como ARFF inclui metadados, o Machine Learning Studio utiliza estes metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se importar dados, tais como TSV ou CSV com o formato que não inclua estes metadados, o Machine Learning Studio infere o tipo de dados para cada coluna pelos dados de amostragem. Se os dados também não tem cabeçalhos de coluna, o Machine Learning Studio fornece nomes predefinidos.

Pode especificar explicitamente ou alterar os tipos de cabeçalhos e dados de colunas utilizando o [Editar metadados][edit-metadata].

O seguinte **tipos de dados** são reconhecidos pelo Machine Learning Studio:

* Cadeia
* Número inteiro
* duplo
* Booleano
* DateTime
* TimeSpan

O Machine Learning Studio utiliza um tipo de dados internos chamado ***dados tabela*** para transmitir dados entre módulos. Explicitamente pode converter os dados numa tabela de dados utilizando o formato de [converter para o conjunto de dados] [ convert-to-dataset] módulo.

Qualquer módulo que aceita os formatos que não seja a tabela de dados irá converter os dados de tabela de dados silenciosamente antes de passá-lo para o módulo seguinte.

Se necessário, pode converter a tabela de dados formato no CSV, TSV, ARFF, ou SVMLight utilizando outros módulos de conversão.
Procure no **conversões de formato de dados** secção da paleta do módulo para módulos que realizam estas funções.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
