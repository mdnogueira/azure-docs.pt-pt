---
title: "Atividade de cópia de Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre a atividade de cópia no Azure Data Factory que pode utilizar para copiar dados de um arquivo de dados de origem suportada para um arquivo de dados suportados sink."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 36443ec86c15edce27bdc4f50cabcaf2e14936bc
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia numa fábrica de dados do Azure

## <a name="overview"></a>Descrição geral

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-data-movement-activities.md)
> * [Versão 2 - Pré-visualização](copy-activity-overview.md)

No Azure Data Factory, pode utilizar a atividade de cópia para copiar dados entre dados arquivos localizada no local e na nuvem. Depois dos dados são copiados, este pode ser mais transformado e analisado. Também pode utilizar a atividade de cópia de publicação de transformação e resultados de análise para intelligence de negócio (BI) e o consumo de aplicação.

![Função de atividade de cópia](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade de cópia no V1](v1/data-factory-data-movement-activities.md).

Atividade de cópia é executada num [integração Runtime](concepts-integration-runtime.md). Para o cenário de cópia de dados diferentes, pode ser aproveitada versão diferente do tempo de execução de integração:

* Quando copiar dados entre dados armazena que são acessíveis publicamente, atividade de cópia pode ser dado o poder por **Runtime de integração do Azure**, que é seguro, fiável e escalável, e [globalmente disponível](concepts-integration-runtime.md#integration-runtime-location).
* Quando copiar dados de/para os arquivos de dados localizados no local ou numa rede com o controlo de acesso (por exemplo, rede Virtual do Azure), terá de configurar um **autoalojado Runtime integrada** atribuir a cópia de dados.

Tempo de execução de integração tem de estar associado a cada arquivo de dados de origem e dependente. Saiba mais detalhes sobre como atividade de cópia [determina qual IR para utilizar](concepts-integration-runtime.md#determining-which-ir-to-use).

Atividade de cópia realiza as seguintes fases para copiar dados a partir de uma origem para um receptor de. O serviço que está na base de atividade de cópia:

1. Lê os dados de um arquivo de dados de origem.
2. Efetua a serialização/desserialização, compressão/descompressão, mapeamento de colunas, etc. Faz estas operações com base nas configurações do conjunto de dados de entrada, o conjunto de dados de saída e a atividade de cópia.
3. Escreve dados para o arquivo de dados de destino/sink.

![Descrição Geral da Atividade de Cópia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Arquivos de dados suportadas e formatos

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de ficheiros suportados

Pode utilizar a atividade de cópia para **copiar ficheiros como-é** entre dois arquivos de dados baseada em ficheiros, na qual caso os dados é copiado eficientemente sem qualquer serialização/desserialização.

Atividade de cópia também suporta a leitura a partir do e escrever em ficheiros em formatos especificados: **texto, JSON, Avro, ORC e Parquet**e codec de compressão **GZip, Deflate, BZip2 e ZipDeflate** são suportados. Consulte [suportado os formatos de ficheiro e compressão](supported-file-formats-and-compression-codecs.md) com detalhes.

Por exemplo, pode efetuar as seguintes atividades de cópia:

* Copiar os dados no SQL Server no local e escrita ao Azure Data Lake Store no formato ORC.
* Copiar os ficheiros no formato de texto (CSV) do sistema de ficheiros no local e escrita aos BLOBs do Azure no formato Avro.
* Copiar ficheiros zipped de sistema de ficheiros no local e, em seguida, descomprimir telefone ao Azure Data Lake Store.
* Copiar os dados no formato de texto comprimidos (CSV) de GZip Blob do Azure e de escrita para a SQL Database do Azure.

## <a name="supported-regions"></a>Regiões suportadas

O serviço que está na base de atividade de cópia está disponível globalmente nas regiões e localizações geográficas listados em [localizações de Runtime de integração do Azure](concepts-integration-runtime.md#integration-runtime-location). A topologia globalmente disponível garante o movimento de dados eficiente que normalmente evita saltos por várias regiões. Consulte [serviços por região](https://azure.microsoft.com/regions/#services) para disponibilidade da fábrica de dados e o movimento de dados numa região.

## <a name="configuration"></a>Configuração

Para utilizar a atividade de cópia no Azure Data Factory, tem de:

1. **Crie serviços ligados para o arquivo de dados de origem e de arquivo de dados do sink.** Consulte "Propriedades do serviço ligado" secção o artigo de conector sobre como configurar e as propriedades suportadas. Pode encontrar a lista de conector suportados no [arquivos de dados e formatos suportados](#supported-data-stores-and-formats) secção.
2. **Crie conjuntos de dados de origem e dependente.** Consulte a origem e o sink secção de "Propriedades do Dataset" dos artigos do conector sobre como configurar e as propriedades suportadas.
3. **Crie um pipeline com atividade de cópia.** A secção seguinte fornece um exemplo.  

### <a name="syntax"></a>Sintaxe

O modelo seguinte de uma atividade de cópia contém uma lista exaustiva de propriedades suportadas. Especifique as que se adeque ao seu cenário.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Detalhes de sintaxe

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de uma atividade de cópia tem de ser definida: **cópia** | Sim |
| Entradas | Especifique o conjunto de dados que criou que aponta para a origem de dados. Atividade de cópia suporta apenas uma única entrada. | Sim |
| saídas | Especifique o conjunto de dados que criou os pontos de dados sink. Atividade de cópia suporta apenas um único resultado. | Sim |
| typeProperties | Um grupo de propriedades para configurar a atividade de cópia. | Sim |
| origem | Especifique o tipo de origem de cópia e as propriedades correspondentes sobre como obter dados.<br/><br/>Saiba os detalhes da secção "Copiar propriedades da atividade" no artigo de conector indicado no [arquivos de dados e formatos suportados](#supported-data-stores-and-formats). | Sim |
| sink | Especifique o tipo de sink de cópia e as propriedades correspondentes como escrever dados.<br/><br/>Saiba os detalhes da secção "Copiar propriedades da atividade" no artigo de conector indicado no [arquivos de dados e formatos suportados](#supported-data-stores-and-formats). | Sim |
| Tradutor | Especificar mapeamentos de colunas explícita de origem para sink. Aplica-se de que quando o comportamento de cópia predefinido não é possível satisfazer satisfaça as suas necessidades.<br/><br/>Saiba os detalhes da [mapeamento do tipo de esquema e dados](copy-activity-schema-and-type-mapping.md). | Não |
| cloudDataMovementUnits | Especifique o powerfulness de [Runtime de integração do Azure](concepts-integration-runtime.md) atribuir a cópia de dados.<br/><br/>Saiba os detalhes da [unidades de movimento de dados de nuvem](copy-activity-performance.md). | Não |
| parallelCopies | Especifique o paralelismo que pretende que a atividade de cópia para utilizar durante a leitura de dados de origem e de escrita de dados para sink.<br/><br/>Saiba os detalhes da [paralela cópia](copy-activity-performance.md#parallel-copy). | Não |
| enableStaging<br/>stagingSettings | Escolha esta opção testar os dados no armazenamento de BLOBs de aa em vez de copiar diretamente os dados de origem para sink intermédio.<br/><br/>Saiba os cenários útil e detalhes de configuração da [testado cópia](copy-activity-performance.md#staged-copy). | Não |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Escolha como lidar com linhas incompatíveis ao copiar dados de origem para sink.<br/><br/>Saiba os detalhes da [tolerância a falhas](copy-activity-fault-tolerance.md). | Não |

## <a name="monitoring"></a>Monitorização

Detalhes de execução da atividade de cópia e as características de desempenho são devolvidas em resultado da execução de atividade de cópia -> a secção de saída. Segue-se uma lista de esgotados. Saiba como monitorizar a atividade executar a partir de [início rápido secção monitorização](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run). Pode comparar o desempenho e a configuração do seu cenário para a atividade de cópia [referência de desempenho](copy-activity-performance.md#performance-reference) testar internamente.

| Nome da propriedade  | Descrição | Unidade |
|:--- |:--- |:--- |
| DataRead | Tamanho dos dados de leitura de origem | Valor Int64 em bytes |
| DataWritten | Tamanho dos dados escrito sink | Valor Int64 em bytes |
| rowsCopied | Número de linhas que está a ser copiados (não aplicável a cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsSkipped | Número de linhas incompatíveis que está a ser ignorado. Pode ativar a funcionalidade pelo conjunto "enableSkipIncompatibleRow" como verdadeiro. | Valor Int64 (nenhuma unidade) |
| Débito | Rácio que são transferidos dados | Número de vírgula flutuante no KB/s |
| copyDuration | A duração da cópia | Valor Int32 em segundos |
| sqlDwPolyBase | Se o PolyBase é utilizado quando copiar dados para o SQL Data Warehouse. | Booleano |
| redshiftUnload | Se descarregamento é utilizado quando copiar dados de Redshift. | Booleano |
| hdfsDistcp | Se for utilizado o DistCp ao copiar dados a partir do HDFS. | Booleano |
| effectiveIntegrationRuntime | Mostrar que Runtime(s) de integração é utilizada para atribuir a atividade executar, no formato de `<IR name> (<region if it's Azure IR>)`. | Texto (cadeia) |
| usedCloudDataMovementUnits | Unidades movimento de nuvem eficaz dados durante a cópia. | Valor Int32 |
| redirectRowPath | Caminho para o registo de ignorados linhas incompatíveis no armazenamento de BLOBs que configurou em "redirectIncompatibleRowSettings". Consulte o exemplo abaixo. | Texto (cadeia) |
| billedDuration | A duração a ser cobrada movimento de dados. | Valor Int32 em segundos |

```json
"output": {
    "dataRead": 1024,
    "dataWritten": 2048,
    "rowsCopies": 100,
    "rowsSkipped": 2,
    "throughput": 1024.0,
    "copyDuration": 3600,
    "redirectRowPath": "https://<account>.blob.core.windows.net/<path>/<pipelineRunID>/",
    "redshiftUnload": true,
    "sqlDwPolyBase": true,
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 8,
    "billedDuration": 28800
}
```

## <a name="schema-and-data-type-mapping"></a>Esquema e o mapeamento do tipo de dados

Consulte o [mapeamento do tipo de esquema e dados](copy-activity-schema-and-type-mapping.md), que descreve como a atividade de cópia mapeia os dados de origem para sink.

## <a name="fault-tolerance"></a>Tolerância a falhas

Por predefinição, o atividade de cópia para copiar dados e devolve falha quando encontra dados incompatíveis entre a origem e dependente. Pode configurar explicitamente para ignorar e registar as linhas incompatíveis e só copiar esses dados compatíveis para fazer a cópia foi concluída com êxito. Consulte o [tolerância a falhas de atividade de cópia](copy-activity-fault-tolerance.md) em obter mais detalhes.

## <a name="performance-and-tuning"></a>Desempenho e otimização

Consulte o [guia Otimização e de desempenho de atividade de cópia](copy-activity-performance.md), que descreve os principais fatores que afetam o desempenho do movimento de dados (atividade de cópia) no Azure Data Factory. Também apresenta uma lista de desempenho durante os testes internos e descreve as várias formas para otimizar o desempenho de atividade de cópia.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes inícios rápidos, tutoriais e exemplos:

- [Copiar dados de uma localização para outra localização no mesmo armazenamento de Blobs do Azure](quickstart-create-data-factory-dot-net.md)
- [Copiar dados do Blob Storage do Azure para a SQL Database do Azure](tutorial-copy-data-dot-net.md)
- [Copiar dados do SQL Server no local para Azure](tutorial-hybrid-copy-powershell.md)
