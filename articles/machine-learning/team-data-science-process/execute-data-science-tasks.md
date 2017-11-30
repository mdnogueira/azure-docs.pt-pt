---
title: "Executar tarefas de ciência de dados - Azure Machine Learning | Microsoft Docs"
description: "Como um scientist de dados pode executar um projeto de ciência de dados no trackable, versão controladas e a forma de colaboração."
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
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 1ad4e8c117f93f2f085c01fae2a5ab38cdd10d2f
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelação e a implementação

As tarefas de ciência de dados típicas incluem exploração de dados, modelação e a implementação. Este artigo mostra como utilizar o **interativa de exploração de dados, análise e relatórios (IDEAR)** e **automatizada modelação e de relatórios (AMAR)** utilitários para realizar várias tarefas de ciência de dados comuns Por exemplo, a exploração de dados interativa, análise de dados, relatórios e criação de modelo. É também descreve as opções para implementar um modelo para um ambiente de produção, utilizando uma variedade de plataformas toolkits e os dados, tais como o seguinte:

- [Azure Machine Learning](../preview/index.yml)
- [SQL Server com os serviços de ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Servidor de Microsoft Machine Learning](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a>Exploração 

Um scientist de dados pode efetuar a exploração e relatórios numa variedade de formas: através da utilização de bibliotecas e pacotes disponíveis para o Python (matplotlib por exemplo) ou com R (ggplot ou lattice por exemplo). Cientistas de dados podem personalizar desse código, para se ajustar às necessidades de exploração de dados para cenários específicos. As necessidades para lidar com dados estruturados são diferentes para dados não estruturados como texto ou de imagens. 

Produtos como o Azure Machine Learning Workbench também fornecem [avançadas preparação dados](../preview/tutorial-bikeshare-dataprep.md) para dados wrangling e exploração, incluindo a criação de funcionalidade. O utilizador deve opte por utilizar as ferramentas, bibliotecas e pacotes que melhor suite as suas necessidades. 

Deliverable no final desta fase é um relatório de exploração de dados. O relatório deve fornecer uma vista razoavelmente abrangente dos dados a ser utilizado para a modelação e uma avaliação de se os dados são adequados para prosseguir para o passo de modelação. Os utilitários de processo de ciência de dados de equipa (TDSP) abordados nas secções seguintes para exploração por automatizada, modelação e relatórios também fornecem exploração de dados padronizado e modelação de relatórios. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Exploração de dados interativa, análise e relatórios utilizando o utilitário IDEAR

Este R baseado no markdown ou o utilitário baseado no bloco de notas do Python fornece uma ferramenta flexível e interativa para avaliar e explore os conjuntos de dados. Os utilizadores podem gerar rapidamente os relatórios do conjunto de dados com codificação mínima. Os utilizadores podem clicar botões para exportar os resultados de exploração na ferramenta de interativa para um relatório final, o que pode ser distribuído aos clientes ou utilizado para tomar decisões sobre as variáveis para incluir o passo de modelação subsequentes.

Neste momento, a ferramenta só funciona em pacotes de dados na memória. É necessário um ficheiro YAML para especificar os parâmetros de conjunto de dados para ser explorou. Para obter mais informações, consulte [IDEAR no utilitários de ciência de dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a>Modelação

Existem várias toolkits e pacotes para modelos de formação em várias linguagens. Cientistas de dados devem hesite utilizar aqueles which ever estiver familiarizados com a, desde que o desempenho as considerações relativas à precisão e latência são satisfeitas para as empresas e relevantes utilizam casos e cenários de produção.

A secção seguinte mostra como utilizar um utilitário TDSP baseado em R para a modelação por automatizada. Este utilitário AMAR pode ser utilizado para gerar os modelos de linha de base rapidamente, bem como os parâmetros que têm de estar otimizado para fornecer um melhor desempenho do modelo.
A secção de gestão de modelo seguinte mostra como têm um sistema para registar e gerir vários modelos.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modelo de formação: modelação e relatórios utilizando o utilitário AMAR

O [automatizada modelação e o utilitário de relatórios (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) fornece uma ferramenta personalizável por automatizadas para efetuar a criação de modelo com o parâmetro hyper varrimento e para comparar a precisão desses modelos. 

O utilitário de criação de modelo é um ficheiro de Markdown de R que pode ser executado para produzir a saída HTML autónomo com uma tabela de conteúdos para a navegação fácil através do respetivas diferentes secções. Três algoritmos são executados quando o ficheiro de Markdown é executado (knit): regressão regularized utilizando o glmnet do pacote, aleatória floresta utilizando o pacote de randomForest e os aumentos árvores utilizando o pacote de xgboost). Cada um destes algoritmos produz um modelo preparado. É comparada com a precisão destes modelos e a importância de funcionalidade relativo rastreia é reportado. Atualmente, existem dois utilitários: uma é para uma tarefa de classificação binária e um é uma tarefa de regressão. As principais diferenças entre eles é os parâmetros de controlo de forma e métricas de precisão especificadas para estas tarefas de aprendizagem. 

Um ficheiro YAML é utilizado para especificar:

- a entrada de dados (uma origem SQL ou um ficheiro de dados de R) 
- que parte dos dados é utilizada para formação e que parte para fins de teste
- que algoritmos para executar 
- a escolha de parâmetros de controlo para a otimização de modelo:
    - validação cruzada 
    - bootstrapping
    - subconjuntos de validação de validação cruzada
- os conjuntos de hyper-parâmetro de cada algoritmo. 

O número de algoritmos, o número de subconjuntos de validação para otimização, os hyper-os parâmetros e o número de conjuntos de parâmetro hyper para sweep através de também podem ser modificadas no ficheiro Yaml para executar os modelos rapidamente. Por exemplo, pode ser executadas com um número inferior de subconjuntos de validação do CV, número de conjuntos de parâmetros. Se é garantido, estes também podem ser executadas mais abrangente com um número mais alto de subconjuntos de validação CV ou um grande número de conjuntos de parâmetros.

Para obter mais informações, consulte [automatizada modelação e relatórios utilitário no utilitários de ciência de dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Gestão de modelos
Depois de tem incorporados vários modelos, normalmente, tem de ter um sistema para registar e gerir os modelos. Normalmente, precisa de uma combinação de scripts ou APIs e um sistema de base de dados ou controlo de versões de back-end. Algumas opções que pode considerar para estas tarefas de gestão são:

1. [O Azure Machine Learning - serviço de gestão de modelo](../preview/index.yml)
2. [ModelDB de MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL Server-seerver como um sistema de gestão de modelo](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Servidor de Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a>Implementação

Implementação de produção permite que um modelo de reproduzir uma função ativa numa empresa. Predições de um modelo implementada podem ser utilizadas para as decisões de negócio.

### <a name="production-platforms"></a>Plataformas de produção
Existem várias abordagens e plataformas para colocar os modelos em produção. Seguem-se algumas opções:


- [Modelo de implementação no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)
- [Implementação de um modelo no SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Servidor de Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>Nota: Antes da implementação, um tem a assegurar que a latência da classificação do modelo é baixa o suficiente para utilizar na produção.
>

Mais exemplos estão disponíveis nas instruções que demonstram todos os passos no processo de **cenários específicos**. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) artigo. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente.

Nota: Para implementação utilizando o Azure Machine Learning Studio, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>Um teste a / B
Quando vários modelos na produção, poderá ser útil efetuar [um teste a / B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Passos seguintes

[Controlar o progresso de projetos de ciência de dados](track-progress.md) mostra como um scientist dados acompanhar o progresso de um projeto de ciência de dados.
 


