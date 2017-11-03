---
title: "Referência da API de recolha de dados do Machine Learning modelo do Azure | Microsoft Docs"
description: "Referência de API de recolha de dados do Machine Learning modelo do Azure."
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 7a0fda8a44d13bcaba84b4124d9b693c05874154
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Referência da API de recolha de dados do Machine Learning modelo do Azure

Recolha de dados do modelo permite-lhe arquivar entradas de modelo e predições de uma serviço web de aprendizagem. Consulte o [guia de procedimentos de coleção de dados de modelo](how-to-use-model-data-collection.md) para compreender como instalar `azureml.datacollector` no seu computador Windows e Linux.

Este guia de referência de API, utilizamos uma abordagem de passo a passo sobre como recolher entradas de modelo e predições de uma serviço web de aprendizagem.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Ativar a recolha de dados modelo no ambiente do Workbench do Azure ML

 Procure conda\_dependencies.yml de ficheiros no seu projeto na pasta aml_config e ter a sua conda\_dependencies.yml ficheiros incluem o módulo de azureml.datacollector na secção pip, conforme mostrado abaixo. Tenha em atenção que se trata de apenas uma subsecção de um conda completa\_dependencies.yml ficheiro:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>Atualmente, pode utilizar o módulo de recoletor de dados no Azure ML Workbench em execução no modo de docker. Modo de local não pode funcionar para todos os ambientes.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Ativar a recolha de dados modelo no ficheiro de pontuação

No ficheiro de classificação que está a ser utilizado para operationalization, importe o módulo de recoletor de dados e a classe ModelDataCollector:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Instanciação de recoletor de dados do modelo
Instanciar uma nova instância de um ModelDataCollector:

DC = ModelDataCollector (model_name, identificador = 'default' feature_names = None, model_management_account_id = 'desconhecido', webservice_name = 'desconhecido', model_id = 'desconhecido', model_version = 'desconhecido')

Consulte os detalhes da classe e o parâmetro:

### <a name="class"></a>Classe
| Nome | Descrição |
|--------------------|--------------------|
| ModelDataCollector | Uma classe azureml.datacollector espaço de nomes. Uma instância desta classe será utilizada para recolher dados do modelo. Um único ficheiro de classificação pode conter vários ModelDataCollectors. Cada instância deve ser utilizada para recolher dados numa localização discreta no ficheiro de classificação, para que o esquema dos dados recolhidos permanece consistente (ou seja, entradas e predição)|


### <a name="parameters"></a>Parâmetros

| Nome | Tipo | Descrição |
|-------------|------------|-------------------------|
| model_name | Cadeia | o nome do modelo de dados de que estão a ser recolhidos para |
| Identificador | Cadeia | a localização no código que identifica a estes dados, ou seja 'RawInput' ou 'Predição' |
| feature_names | lista de cadeias de | uma lista de nomes de funcionalidades que tornam-se o cabeçalho de csv quando fornecido |
| model_management_account_id | Cadeia | o identificador para a conta de gestão de modelo onde este modelo é armazenado. Este é preenchido automaticamente quando modelos são operacionalizados através de AML |
| webservice_name | Cadeia | o nome do webservice ao qual este modelo está atualmente implementado. Este é preenchido automaticamente quando modelos são operacionalizados através de AML |
| model_id | Cadeia | O identificador exclusivo para este modelo no contexto de uma conta de gestão de modelo. Este é preenchido automaticamente quando modelos são operacionalizados através de AML |
| model_version | Cadeia | o número de versão deste modelo no contexto de uma conta de gestão de modelo. Este é preenchido automaticamente quando modelos são operacionalizados através de AML |



 

## <a name="collecting-the-model-data"></a>Recolher os dados do modelo

É possível recolher os dados do modelo a utilizar uma instância do ModelDataCollector criado acima.

    dc.collect(input_data, user_correlation_id="")

Consulte os detalhes de método e o parâmetro:

### <a name="method"></a>Método
| Nome | Descrição |
|--------------------|--------------------|
| recolher | Utilizado para recolher os dados para uma entrada de modelo ou predição|


### <a name="parameters"></a>Parâmetros

| Nome | Tipo | Descrição |
|-------------|------------|-------------------------|
| input_data | vários tipos | os dados a serem recolhidos (atualmente aceita a lista de tipos, numpy.array, pandas. DataFrame, pyspark.sql.DataFrame). Para tipos de dataframe, se existe um cabeçalho com nomes de funcionalidade, estas informações estão incluídas de destino de dados (sem necessidade de transmita explicitamente os nomes das funcionalidades no construtor ModelDataCollector) |
| user_correlation_id | Cadeia | um id de correlação opcional, que pode ser fornecido pelo utilizador para correlacionar este predição |

