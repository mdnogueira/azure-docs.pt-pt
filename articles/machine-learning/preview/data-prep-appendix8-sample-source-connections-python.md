---
title: "Ligações de dados de origem adicionais de exemplo possíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de ligações de dados de origem que são possíveis com a preparação de dados do Azure Machine Learning"
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
ms.date: 09/11/2017
ms.openlocfilehash: 9aaf4329b25cb189146949afed89cf15619ba592
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-custom-source-connections-python"></a>Exemplo de ligações à origem personalizada (Python) 
Antes de ler este anexo, leia [descrição geral de extensibilidade do Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Carregar dados do data.world

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="register-yourself-at-dataworld"></a>Registar-se em data.world
Precisa de um token de API do Web site data.world.

#### <a name="install-dataworld-library"></a>Instalar data.world biblioteca

Abrir a interface de linha de comandos do Azure Machine Learning Workbench selecionando **ficheiro** > **interface de linha de comandos aberta**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Em seguida, execute `dw configure` na linha de comandos, que lhe solicita o token. Quando introduzir o seu token, um .dw / é criado um diretório no seu diretório raiz e o token é armazenado não existe.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Agora, deve conseguir importar data.world bibliotecas.

#### <a name="load-data-into-data-preparation"></a>Carregar dados para a preparação de dados

Crie um novo fluxo de dados baseados em script. Em seguida, utilize o seguinte script para carregar os dados de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Carregar dados de base de dados do Azure Cosmos para preparação de dados

Criar um novo fluxo de dados baseados em script e, em seguida, utilize o seguinte script para carregar os dados da base de dados do Azure Cosmos. (As bibliotecas tem de ser instalado primeiro. Para obter mais informações, consulte o documento de referência anterior que criamos uma ligação para.)

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
