---
title: "A predição Churn cliente utilizando o Azure Machine Learning | Microsoft Docs"
description: "Como efetuar análises de fluxo através do Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 2ee7ec91700c66d5bedd917d0203a726b5c5e300
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Predição de volume de alterações de cliente utilizando o Azure Machine Learning

Em média, a manter os clientes existentes é cinco vezes mais barata que o custo de recruiting novos. Como resultado, os executivos de marketing, muitas vezes, localizam próprios tentar estimar a probabilidade de volume de alterações do cliente e localizar as ações necessárias para minimizar a taxa de rotatividade.

O objetivo desta solução é para demonstrar a Análise Preditiva volume de alterações ao utilizar o Azure Machine Learning Workbench. Esta solução fornece um modelo para desenvolver pipelines de dados preditiva do volume de alterações para retalhistas fáceis de utilizar. O modelo pode ser utilizado com diferentes conjuntos de dados e definições diferentes do volume de alterações. O objetivo do exemplo prática é:

1. Compreenda as ferramentas de preparação de dados do Workbench do Azure Machine Learning para limpar e inserir dados de relações de cliente para a análise de volume de alterações.

2. Efetue a transformação de funcionalidade para processar dados heterogéneos inúteis.

3. Integrar as bibliotecas de terceiros (como `scikit-learn` e `azureml`) para desenvolver Bayesiano e baseados em árvore classificadores para prever o volume de alterações.

4. Implemente.

## <a name="link-of-the-gallery-github-repository"></a>Ligação do repositório GitHub da Galeria
Segue-se a ligação para o repositório do GitHub público onde está alojado o todo o código:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Descrição geral de cenário de utilização
As empresas necessitam de uma estratégia eficaz para gerir o volume de alterações do cliente. Volume de alterações do cliente inclui os clientes a parar a utilização de um serviço, mudar para um serviço competitor, mudar para uma experiência de nível inferior de serviço ou reduzir o engagement com o serviço.

Neste caso de utilização, vamos ver dados da empresa de telecom francês laranja para identificar os clientes que se prevê a churn a breve prazo para melhorar o serviço e criar campanhas outreach personalizado que o ajudam a manter os clientes.

As empresas de Telecom enfrentam um mercado competitivos. Os operadores muitos perdem receitas de clientes postpaid devido à churn. Assim, a capacidade de identificar com precisão o volume de alterações do cliente pode ser uma grande vantagem competitiva.

Alguns dos fatores que contribuem para o volume de alterações do telecom cliente incluem:

* Interrupções ao serviço frequentes percetível
* Serviço de cliente fraco experiências nos arquivos de revenda/online
* Ofertas de outros concorrentes os operadores (melhor plano família, o plano de dados, etc.).

Nesta solução, utilizamos um exemplo de criação de um cliente preditivo modelo de volume para empresas telecom concreto.

## <a name="prerequisites"></a>Pré-requisitos

* Um [conta do Azure](https://azure.microsoft.com/free/) (gratuitas estão disponíveis)

* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguintes o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar uma área de trabalho

* Para operationalization, é melhor se tiver o motor de Docker instalado e em execução localmente. Caso contrário, pode utilizar a opção de cluster, mas tenha em atenção que pode ser dispendiosa executar um serviço de contentor do Azure (ACS).

* Esta solução assume que está a executar Workbench do Azure Machine Learning no Windows 10 com o motor de Docker instalada localmente. Se estiver a utilizar macOS as instruções em grande parte é o mesmo.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto do Workbench

Crie um novo projeto com este exemplo como um modelo:
1.  Abra máquina do Azure Learning Workbench
2.  No **projetos** página, clique em de  **+**  iniciar sessão e selecionar **novo projeto**
3.  No **criar novo projeto** painel, preencha as informações para o novo projeto
4.  No **modelos de projeto de pesquisa** caixa de pesquisa, escreva "Cliente Churn predição" e selecione o modelo
5.  Clique em **Criar**.

## <a name="data-description"></a>Descrição de dados

O conjunto de dados utilizado na solução é a partir de concorrência SIDKDD 2009. É denominado `CATelcoCustomerChurnTrainingSample.csv` e está localizado no [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) pasta. O conjunto de dados é composta pelos dados inúteis heterogéneos (variáveis numérica/categórico) de empresa francês Telecom laranja e é são anónimos.

As variáveis de capturar informações demográficas de cliente, as estatísticas de chamada (por exemplo, a duração da chamada de média, taxa de falhas de chamada, etc.), de contrato de informações, estatísticas em conformidade. Variável de fluxo é binário (0 - não foi possível churn, 1 - churn).

## <a name="scenario-structure"></a>Estrutura do cenário

A estrutura de pastas é disposta da seguinte forma:

__dados__: contém o conjunto de dados utilizado na solução  

__Docs__: contém todos os laboratórios prática

A ordem dos Hands-on laboratórios para realizar a solução é o seguinte:
1. Preparação de dados: O ficheiro principal relacionadas com a preparação de dados na pasta de dados está`CATelcoCustomerChurnTrainingSample.csv`
2. Modelação e avaliação: é o ficheiro principal relacionadas com a modelação e avaliação na pasta raiz`CATelcoCustomerChurnModeling.py`
3. Modelação e avaliação no Docker: é o ficheiro principal para esta tarefa na pasta raiz`CATelcoCustomerChurnModelingDocker.py`
4. Operationalization: Os ficheiros de principais para deloyment são o modelo (`model.pkl`) e`churn_schema_gen.py`

| Ordem| Nome de ficheiro | Ficheiros de Realted |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | 'data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | 'CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationDocker.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationDocker.md) | 'CATelcoCustomerChurnModelingDocker.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | 'model.pkl'<br>'churn_schema_gen.py' |

Siga os laboratórios da forma sequenciais descrito acima.

## <a name="conclusion"></a>Conclusão
Isto passa cenário demonstrado como realizar uma predição de volume de alterações com o Azure Machine Learning Workbench. Primeiro efetuámos dados de limpeza para processar os dados inúteis e heterogéneos, seguidos de engenharia da funcionalidade ferramentas de preparação de dados a utilizar. Vamos, em seguida, utilizar ferramentas de aprendizagem de código aberto para criar e avaliar um modelo de classificação, em seguida, utilizado o contentor de local docker para implementar o modelo tornando pronta para produção.
