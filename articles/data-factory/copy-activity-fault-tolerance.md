---
title: "Tolerância de atividade de cópia no Azure Data Factory a falhas | Microsoft Docs"
description: "Saiba mais sobre como adicionar tolerância a falhas para a atividade de cópia no Azure Data Factory por ignorar as linhas incompatíveis."
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
ms.date: 09/05/2017
ms.author: jingwang
ms.openlocfilehash: 5b2658cecba80ef871cc38b930b0e52bc3952530
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerância a falhas de atividade de cópia no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versão 2 - Pré-visualização](copy-activity-fault-tolerance.md)

A atividade de cópia no Azure Data Factory oferece duas formas de lidar com linhas incompatíveis ao copiar dados entre os arquivos de dados de origem e dependente:

- Pode abortar e efetuar a cópia atividade quando os dados incompatíveis encontrado (comportamento predefinido).
- Pode continuar copiar todos os dados ao adicionar a tolerância a falhas e a ignorar as linhas de dados incompatíveis. Além disso, pode iniciar as linhas incompatíveis no Blob storage do Azure. Em seguida, pode examinar o registo para obter a causa da falha, corrija os dados na origem de dados e repita a atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [copiar tolerância a falhas de atividade V1](v1/data-factory-copy-activity-fault-tolerance.md).


 ## <a name="supported-scenarios"></a>Cenários suportados
Atividade de cópia suporta três cenários para detetar, ignorar, dados e registo incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo sink**. <br/><br/> Por exemplo: copiar dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema contém três colunas do tipo INT. As linhas do ficheiro CSV que contêm dados numéricos, tais como 123,456,789 são copiadas com êxito para o arquivo de sink. No entanto, as linhas que contêm valores não numéricos, tais como 123,456, abc são detetados como incompatíveis e são ignorados.
- **Erro de correspondência no número de colunas entre a origem e o sink**. <br/><br/> Por exemplo: copiar dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema que contenha seis colunas. As linhas do ficheiro CSV que contenha seis colunas são copiadas com êxito para o arquivo de sink. As linhas do ficheiro CSV que contenha colunas mais ou menos de seis são detetadas como incompatíveis e são ignoradas.
- **Violação da chave primária ao escrever para uma base de dados relacional**.<br/><br/> Por exemplo: copiar dados do SQL server para uma base de dados do SQL Server. É definida uma chave primária na base de dados do SQL Server sink, mas essa nenhuma chave primária está definido no servidor SQL de origem. As linhas duplicadas que existe na origem não não possível copiar o sink. Atividade de cópia copia apenas a primeira linha dos dados de origem para o sink. As linhas subsequentes de origem que contêm o valor de chave primária duplicado são detetadas como incompatíveis e são ignoradas.

## <a name="configuration"></a>Configuração
O exemplo seguinte fornece uma definição de JSON para configurar a ignorar as linhas incompatíveis na atividade de cópia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "AzureBlobLinkedService",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```
Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Especifica se pretende ignorar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>FALSE (predefinição) | Não
redirectIncompatibleRowSettings | Um grupo de propriedades que podem ser especificados quando pretender registar as linhas incompatíveis. | &nbsp; | Não
linkedServiceName | O serviço ligado do Storage do Azure para armazenar o registo que contém as linhas ignoradas. | O nome de um AzureStorage ou AzureStorageSas ligado serviço, que se refere à instância de armazenamento que pretende utilizar para armazenar o ficheiro de registo. | Não
Caminho | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho de armazenamento de BLOBs que pretende utilizar para registar os dados incompatíveis. Se não fornecer um caminho, o serviço cria um contentor para si. | Não

## <a name="monitor-skipped-rows"></a>Monitorizar linhas ignoradas
Após a conclusão da execução da atividade de cópia, pode ver o número de linhas ignorados na saída da atividade de cópia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Se configurar para registar as linhas incompatíveis, pode encontrar o ficheiro de registo neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Os ficheiros de registo só podem ser os ficheiros csv. Os dados originais ignorados serão registados com vírgulas como delimitador de coluna se for necessário. Iremos adicionar mais duas colunas "ErrorCode" e "ErrorMessage" no adicionais aos dados de origem original no ficheiro de registo, onde pode ver a raiz causa de incompatibilidade. O código de erro e ErrorMessage vai estar escritos entre aspas por aspas duplas. 

Um exemplo do conteúdo do ficheiro de registo é o seguinte:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho de atividade de cópia](copy-activity-performance.md)


