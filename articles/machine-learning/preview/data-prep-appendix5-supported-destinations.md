---
title: "Suportado destinos de dados e saídas disponíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa de destinos suportados e produz disponível para a preparação de dados do Azure Machine Learning"
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
ms.date: 09/07/2017
ms.openlocfilehash: bbbee61d6cd67dd437e4fbcd7260d2f378531912
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="supported-data-exports-for-this-preview"></a>Suportado exportações de dados para esta pré-visualização 
É possível exportar para vários formatos diferentes. Pode utilizar estes formatos de manter os resultados intermédios de preparação de dados antes dos resultados integra o resto do fluxo de trabalho do Machine Learning.

## <a name="types"></a>Tipos de 
### <a name="csv-file"></a>Ficheiro CSV 
Escreva um ficheiro de valores separados por vírgulas para o armazenamento.

#### <a name="options"></a>Opções
- Endings de linha
- Substitua os valores NULL com
- Substituir erros com 
- separador


### <a name="parquet"></a>Parquet 
Escreva um conjunto de dados para o armazenamento como Parquet.

Parquet como um formato pode demorar várias formas no armazenamento. Para conjuntos de dados mais pequenos, um ficheiro único .parquet, por vezes, é utilizado. As bibliotecas de Python vários suportar leitura e escrita aos ficheiros de .parquet único. 

Atualmente, o Azure Machine Learning Workbench baseia-se na biblioteca de escrita de Parquet durante a utilização de interativa local PyArrow Python. Isto significa que Parquet de ficheiro único está, atualmente, o formato de saída Parquet único que é suportado durante a utilização de interativa local.

Durante a execução de escalamento horizontal (no Spark), do Azure Machine Learning Workbench depende Parquet do Spark leitura e escrita capacidades. Formato de saída predefinido do Spark para Parquet (atualmente a única suportada) é semelhante em termos de estrutura para um conjunto de dados do Hive. Isto significa que uma pasta contém muitas .parquet ficheiros cada uma partição mais pequena de um conjunto de dados maior. 

#### <a name="caveats"></a>Avisos 
Parquet como um formato é relativamente mais novo / e tenha algumas inconsistências de implementação em bibliotecas diferentes. Por exemplo, o Spark coloca restrições em que carateres são válidos nos nomes de colunas ao escrever a para Parquet. PyArrow não efetuar este procedimento. Os seguintes carateres não podem ser um nome de coluna: 
- ,
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Para garantir a compatibilidade com o Spark, sempre que escreve dados Parquet, as ocorrências estes carateres em nomes de coluna são substituídas por e caráter de sublinhado (_).
>- Para assegurar a consistência entre locais e de escalamento horizontal é executada, quaisquer dados escritos Parquet, através da aplicação, Python ou Spark tem os nomes de coluna sanitized para garantir a compatibilidade de Spark. Para garantir que os nomes das colunas esperado ao escrever de carateres Parquet Spark, remova inválido definido de colunas antes de as escrever.



## <a name="locations"></a>Localizações 
### <a name="local"></a>Local 
Disco rígido local ou localização de armazenamento de rede mapeadas.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
Armazenamento de Blobs do Azure requer uma subscrição do Azure.

