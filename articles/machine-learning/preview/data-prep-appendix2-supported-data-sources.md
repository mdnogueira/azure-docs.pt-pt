---
title: "As origens de dados disponíveis com a preparação de dados do Azure Machine Learning suportadas | Microsoft Docs"
description: "Este documento fornece uma lista completa das origens de dados suportadas, disponível para a preparação de dados do Azure Machine Learning"
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
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>Origens de dados suportados para nesta versão 
O documento seguinte descreve a lista de origens de dados atualmente suportados na preparação de dados do Azure Machine Learning.

As origens de dados suportados para esta versão são os seguintes.

## <a name="types"></a>Tipos de 
### <a name="directory-versus-file"></a>Diretório versus ficheiro
*Os ficheiros/diretórios*: Escolha um único ficheiro e lê-lo na preparação de dados. O tipo de ficheiro é analisado para determinar os parâmetros predefinidos para a ligação de ficheiro que está no ecrã seguinte. Escolha um diretório ou o conjunto de ficheiros dentro de um diretório (o Seletor de ficheiros é MultiSelect é). Qualquer um dos resultados da abordagem em ficheiros que está a ser lidos como um fluxo de dados único com os ficheiros anexados a si (com cabeçalhos repartidos saída, se necessário).

Os tipos de ficheiro são os seguintes:
- Delimitados (. csv, .tsv,. txt e assim sucessivamente) 
- Largura fixa
- Texto simples
- Ficheiro JSON

### <a name="csv-file"></a>Ficheiro CSV
Lê um ficheiro CSV de armazenamento.

#### <a name="options"></a>Opções
- separador
- Comentário
- Cabeçalhos
- Símbolo decimal
- Codificação de ficheiro
- Linhas a ignorar

### <a name="tsv-file"></a>Ficheiro TSV
Lê um ficheiro de valores TSV do armazenamento.

#### <a name="options"></a>Opções
- Comentário
- Cabeçalhos
- Codificação de ficheiro
- Linhas a ignorar

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Lê um ficheiro do Excel, uma folha de cada vez, especificando o nome da folha ou número.

#### <a name="options"></a>Opções
- Nome do/número de folha
- Cabeçalhos
- Linhas a ignorar

### <a name="json-file"></a>Ficheiro JSON
Ler um ficheiro JSON a partir do armazenamento. Tenha em atenção que o ficheiro é "simplificado" na leitura.

#### <a name="options"></a>Opções
Nenhuma

### <a name="parquet"></a>Parquet
Ler um conjunto de dados Parquet, ou um único ficheiro ou pasta.

Parquet como um formato pode demorar várias formas no armazenamento. Para conjuntos de dados mais pequenos, um ficheiro único .parquet, por vezes, é utilizado. Vários Python bibliotecas suporte ler ou escrever .parquet único ficheiro. Atualmente, o Azure Machine Learning Workbench baseia-se da biblioteca de PyArrow Python para ler Parquet durante a utilização de interativa local. Suporta ficheiros .parquet único (desde que foram escritos como tal, não como parte de um conjunto de dados superior). Também suporta Parquet conjuntos de dados. 

Um conjunto de dados Parquet é uma coleção de mais de um ficheiro de .parquet, cada um dos quais representa uma partição mais pequena de um conjunto de dados maior. Normalmente, conjuntos de dados estão contidos numa pasta. Estes são o formato de saída de Parquet predefinido para plataformas comuns, tais como o Spark e o Hive.

>[!NOTE]
>Quando estiver a ler dados Parquet que está a ser uma pasta com vários ficheiros .parquet, é esta a selecionar o diretório para ler e a escala de **Parquet Dataset** opção. Isto torna PyArrow ler a pasta toda em vez dos ficheiros individuais. Isto garante que o suporte para ler mais complicadas formas de armazenar Parquet no disco (tais como a pasta a criação de partições.)

Execução de escalamento horizontal depende Parquet do Spark capacidades de leitura e suporta ficheiros único, bem como as pastas.

#### <a name="options"></a>Opções
*Conjunto de dados de parquet*: esta opção determina se o Azure Machine Learning Workbench utiliza o modo unticked ou o modo ticked. O modo unticked expande um determinado diretório e tenta ler individualmente cada ficheiro na mesma. O modo ticked trata o diretório como o conjunto de dados completo e permite PyArrow descobrir a melhor forma de interpretar os ficheiros.


## <a name="locations"></a>Localizações
### <a name="local"></a>Local
Disco rígido local ou localização de armazenamento de rede mapeadas.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
Requer uma subscrição do Azure.

