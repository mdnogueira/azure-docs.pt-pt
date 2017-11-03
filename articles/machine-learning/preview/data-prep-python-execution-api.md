---
title: "Guia aprofundado sobre como utilizar a API de execução de quaisquer preparativos de dados do Azure Machine Learning | Microsoft Docs"
description: Este documento fornece detalhes sobre como executar previamente concebidos pacotes origens de dados e preparativos de dados
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 4a0e4bd58ffa4bc7062ee4844a090be43047e8a1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Executar origens de dados e preparativos dados pacotes do Python

Para utilizar um pacote de origens de dados do Azure Machine Learning ou preparativos de dados do Azure Machine Learning no Python:

1. Vá para o **dados** separador do seu projeto.

2. Clique com botão direito a origem adequada.

3. Escolha **gerar ficheiro de código de acesso de dados.**

Esta ação cria um script de Python curto que executa o pacote e devolve um dataframe.

## <a name="data-sources"></a>Origens de Dados

O `azureml.dataprep.datasource` módulo contém uma função única para executar uma origem de dados e devolver um dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path`é o caminho para a origem de dados (ficheiro .dsource).
- `secrets`é um dicionário opcional que mapeia as chaves para segredos.
- `spark`é um booleano opcional que especifica se pretende devolver um dataframe Spark ou um dataframe Pandas. Por predefinição, o Workbench do Azure Machine Learning determina o tipo de dataframe a devolver em execução com base no contexto.

## <a name="data-preparations-packages"></a>Pacotes de preparativos de dados

O `azureml.dataprep.package` módulo contém três funções que executar um fluxo de dados a partir de um pacote de dados preparações.

### <a name="execution-functions"></a>Funções de execução

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)`submete o fluxo de dados especificada para a execução, mas não devolve um dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)`executa o fluxo de dados especificado e devolve os resultados como um dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)`executa o fluxo de dados especificada com base numa origem de dados em memória e devolve os resultados como um dataframe. O `user_config` argumento é um dicionário que mapeia o caminho absoluto de uma origem de dados (ficheiro .dsource) a uma origem de dados em memória representada como uma lista de listas.

### <a name="common-arguments"></a>Argumentos comuns

- `package_path`é o caminho para o pacote de dados preparativos (.dprep ficheiro).
- `dataflow_idx`é o índice baseado em zero do fluxo de dados que o pacote para executar. Se o fluxo de dados especificado referencia outros fluxos de dados ou origens de dados, estes são executados bem.
- `secrets`é um dicionário opcional que mapeia as chaves para segredos.
- `spark`é um booleano opcional que especifica se pretende devolver um dataframe Spark ou um dataframe Pandas. Por predefinição, o Workbench determina o tipo de dataframe a devolver em execução com base no contexto.
