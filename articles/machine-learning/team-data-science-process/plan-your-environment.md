---
title: "Cenários de identificar e planear o processo de análise - Azure | Microsoft Docs"
description: "Plano para análise avançada, ponderando uma série de questões-chave."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: f4cf702b899b285b18c09d7a5951589d2ae71b7d
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planear o processamento de dados de análises avançadas
Os recursos que deve planear a incluir quando configurar um ambiente para fazer análises avançadas de processamento num conjunto de dados? Este artigo sugere uma série de questões a colocar que ajuda a identificar as tarefas e os recursos relevantes o seu cenário. A ordem dos passos de alto nível para Análise Preditiva está destacada em [o que é o processo de ciência de dados de equipa (TDSP)?](overview.md). Cada um destes passos requer recursos específicos para as tarefas relevantes para o seu cenário específico. As questões-chave para identificar o seu cenário preocupação dados logística, características, a qualidade dos conjuntos de dados e as ferramentas e os idiomas que preferir efetuar a análise.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas logística da: localizações de dados e de movimento
As perguntas logística da concern a localização do **origem de dados**, a **destino** no Azure e os requisitos para mover os dados, incluindo a agenda, a quantidade e o recursos envolvidos. Os dados poderão ter de ser movidos a várias vezes durante o processo de análise. É um cenário comum para mover dados locais para alguma forma de armazenamento no Azure e, em seguida, no Machine Learning Studio.

1. **O que é a origem de dados?** É local ou na nuvem? Por exemplo:
   
   * Os dados são publicamente disponíveis num endereço HTTP.
   * Os dados residem numa localização de ficheiro local/rede.
   * Os dados são numa base de dados do SQL Server.
   * Os dados são armazenados no contentor de armazenamento do Azure
2. **O que é o destino do Azure?** Em que precisa de ser para processamento ou modelação? Por exemplo:
   
   * Armazenamento de Blobs do Azure
   * Bases de dados SQL Azure
   * SQL Server numa VM do Azure
   * HDInsight (Hadoop no Azure) ou as tabelas do Hive
   * Azure Machine Learning
   * Mountable Azure discos rígidos virtuais.
3. **Como vai mover os dados?** Os procedimentos e os recursos disponíveis para inserção ou carregar dados para uma variedade de armazenamento diferente e ambientes de processamento estão descritos nos seguintes artigos:
   
   * [Carregar dados para ambientes de armazenamento para a análise](ingest-data.md)
   * [Importar os dados de formação para o Azure Machine Learning Studio a partir de várias origens de dados](../studio/import-data.md).
4. **Os dados precisam de ser movidos com base numa agenda regular ou modificados durante a migração?** Considere a utilização do Azure Data Factory (ADF) quando dados tem de ser migradas continuamente, particularmente se num cenário híbrido que acede no local e recursos de nuvem está envolvida ou quando os dados são transacionados ou tem de ser modificados ou ter lógica de negócio adicionada ao -lo durante a ser migrado. Para obter mais informações, consulte [mover dados de um servidor SQL no local para o SQL Azure com o Azure Data Factory](move-sql-azure-adf.md)
5. **Quantidade de dados está a ser movidos para o Azure?** Conjuntos de dados que são extremamente grandes podem exceder a capacidade de armazenamento de determinadas ambientes. Por exemplo, consulte a discussão de limites de tamanho para o Machine Learning Studio, na secção seguinte. Nestes casos, um exemplo dos dados pode ser utilizado durante a análise. Para obter detalhes sobre como baixo-exemplo de um conjunto de dados em vários ambientes do Azure, consulte [dados no processo de ciência de dados de equipa de exemplo](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas sobre características de dados: tipo, o formato e o tamanho
Estas perguntas são fundamentais para planear o armazenamento e processamento ambientes, cada um dos quais são adequados para vários tipos de dados e cada um dos quais têm a determinados restrições.

1. **Quais são os tipos de dados?** Por exemplo:
   
   * Numérico
   * Categórico
   * Cadeias
   * Binário
2. **Como estão formatados corretamente os dados?** Por exemplo:
   
   * Separados por vírgulas (CSV) ou separador por vírgulas (TSV) simples ficheiros
   * Comprimidos ou descomprimidos
   * Blobs do Azure
   * Tabelas do Hive do Hadoop
   * Tabelas de SQL Server
3. **Como grandes são os dados?**
   
   * Breve: inferior a 2 GB
   * Média: Maior que 2 GB e inferior a 10 GB
   * Grande: Superior a 10 GB

Por exemplo de executar o ambiente do Azure Machine Learning Studio:

* Para obter uma lista dos formatos de dados e tipos suportados pelo Azure Machine Learning Studio, consulte [formatos de dados e tipos de dados suportados](../studio/import-data.md#data-formats-and-data-types-supported) secção.
* Para obter informações sobre limitações de dados do Azure Machine Learning Studio, consulte o **como grande conjunto de dados possível para os meus módulos?** secção [importar e exportar dados para o Machine Learning](../studio/faq.md#machine-learning-studio-questions)

Para obter informações sobre as limitações de outros serviços do Azure utilizados no processo de análise, consulte [subscrição do Azure e limites de serviço, Quotas e restrições](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas de qualidade de dados: exploração e pré-processamento de
1. **O que sabe sobre os dados?** Explore os dados para obter um compreender de que as características básicas. Os padrões ou tendências-exhibits, os valores atípicos tem ou quantos valores estão em falta. Este passo é importante para determinar a extensão de pré-processamento de necessário, para formulating hypotheses que foi sugerir as funcionalidades mais adequadas ou tipo de análise e para formulating planos de recolha de dados adicionais. Calcular estatísticas descritivas e visualizações de desenho são úteis técnicas de inspeção de dados. Para obter mais informações sobre como explorar um conjunto de dados em vários ambientes do Azure, consulte [explorar dados no processo de ciência de dados de equipa](explore-data.md).
2. **Os dados requerem pré-processadas ou de limpeza?**
   Pré-processadas e limpeza de dados são tarefas importantes que, normalmente, tem de ser a realização de um antes do conjunto de dados pode ser utilizado de forma eficaz para o machine learning. Dados não processados é frequentemente inúteis e pouco fiáveis e poderão estar em falta valores. Utilizar esses dados para a modelação pode produzir resultados enganosa. Para obter uma descrição, consulte [tarefas para preparar os dados para melhorado machine learning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas sobre as ferramentas e idiomas
Existem muitas opções consoante os idiomas e ambientes de desenvolvimento ou ferramentas precisa ou estão a utilizar mais conformable.

1. **Que idiomas preferir a utilizar para análise?**  
   
   * R
   * Python
   * SQL
2. **As ferramentas deve utilizar para análise de dados?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) -uma linguagem de script utilizada para administrar os recursos do Azure num idioma de script.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Análise de rotações](http://www.revolutionanalytics.com/revolution-r-open)
   * [RStudio](http://www.rstudio.com)
   * [Ferramentas do Python para Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Blocos de notas do Jupyter](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificar o seu cenário de análise avançada
Depois de ter respondeu às questões apresentadas na secção anterior, está pronto para determinar qual o cenário de melhor se adequa à sua maiúsculas e minúsculas. Os cenários de exemplo são descritos na [cenários para análise avançada no Azure Machine Learning](plan-sample-scenarios.md).

