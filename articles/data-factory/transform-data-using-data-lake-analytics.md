---
title: Transformar dados utilizando o script U-SQL - Azure | Microsoft Docs
description: "Saiba como processar ou dados de transformação através da execução de scripts U-SQL no serviço de computação do Azure Data Lake Analytics."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.openlocfilehash: 5e54464ceabfe1fea2af80d63e538bea6a0a50a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados através da execução de scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-usql-activity.md)
> * [Versão 2 - Pré-visualização](transform-data-using-data-lake-analytics.md)

Um pipeline de um Azure data factory processa dados nos serviços do storage ligadas utilizando os serviços ligados de computação. Contém uma sequência de atividades em que cada atividade executa uma operação de processamento específico. Este artigo descreve o **atividade de U-SQL de análise do Data Lake** que executa um **U-SQL** script num **Azure Data Lake Analytics** serviço ligado de computação. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [USQL atividade no V1](v1/data-factory-usql-activity.md).

Crie uma conta do Azure Data Lake Analytics antes de criar um pipeline com uma atividade de U-SQL de análise do Data Lake. Para saber mais sobre o Azure Data Lake Analytics, consulte [introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Serviço do Azure Data Lake Analytics ligado
Criar um **Azure Data Lake Analytics** um Azure data factory do serviço de computação de serviço ligado para ligar um Azure Data Lake Analytics. A atividade U-SQL do Data Lake Analytics no pipeline refere-se a este serviço ligado. 

A tabela seguinte fornece descrições para as propriedades genéricas utilizadas na definição JSON. 

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **tipo**                 | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| **accountName**          | Nome de conta do Azure Data Lake Analytics.  | Sim                                      |
| **dataLakeAnalyticsUri** | URI do Azure Data Lake Analytics.           | Não                                       |
| **subscriptionId**       | ID de subscrição do Azure                    | Não (se não for especificado, a subscrição do data factory é utilizada). |
| **resourceGroupName**    | Nome do grupo de recursos do Azure                | Não (se não for especificado, grupo de recursos do data factory é utilizado). |

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço
O serviço ligado do Azure Data Lake Analytics necessita de uma autenticação principal de serviço para ligar ao serviço Azure Data Lake Analytics. Para utilizar a autenticação principal de serviço, registe uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder o acesso ao Data Lake Analytics e Data Lake Store utiliza. Para obter passos detalhados, consulte [autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do inquilino

Utilize a autenticação principal de serviço, especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique o ID de cliente. da aplicação     | Sim      |
| **servicePrincipalKey** | Especifique a chave da aplicação.           | Sim      |
| **inquilino**              | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Sim      |

**Exemplo: Autenticação principal do serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Para saber mais sobre o serviço ligado, consulte o artigo [serviços ligados de computação](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O fragmento JSON seguinte define um pipeline com uma atividade de U-SQL de análise do Data Lake. Definição da atividade tem uma referência para o serviço ligado do Azure Data Lake Analytics que criou anteriormente. Para executar um script de U-SQL do Data Lake Analytics, o Data Factory submete o script especificado para o Data Lake Analytics e o necessárias entradas e saídas está definido o script do Data Lake Analytics obter e de saída. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "AzureDataLakeAnalyticsLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "LinkedServiceofAzureBlobStorageforscriptPath",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

A tabela seguinte descreve os nomes e descrições das propriedades que são específicas para esta atividade. 

| Propriedade            | Descrição                              | Necessário |
| :------------------ | :--------------------------------------- | :------- |
| nome                | Nome da atividade no pipeline     | Sim      |
| descrição         | Texto que descreve o que faz a atividade.  | Não       |
| tipo                | Atividade U-SQL do Data Lake Analytics, o tipo de atividade é **DataLakeAnalyticsU SQL**. | Sim      |
| linkedServiceName   | Serviço ligado ao Azure Data Lake Analytics. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo.  |Sim       |
| ScriptPath          | Caminho para a pasta que contém o script U-SQL. Nome do ficheiro é maiúsculas e minúsculas. | Sim      |
| scriptLinkedService | Serviço ligado que liga o armazenamento que contém o script para a fábrica de dados | Sim      |
| degreeOfParallelism | O número máximo de nós em simultâneo utilizada para executar a tarefa. | Não       |
| prioridade            | Determina quais os que são colocados em fila trabalhos devem ser selecionados para ser executado primeiro. Menor número, maior prioridade. | Não       |
| parâmetros          | Parâmetros para o script U-SQL          | Não       |
| runtimeVersion      | Versão de tempo de execução do motor U-SQL a utilizar | Não       |
| compilationMode     | <p>Modo de compilação do U-SQL. Tem de ser um dos seguintes valores: **Semantic:** só executar verificações semânticos e verificações sanity necessários, **completa:** efetuar a compilação completa, incluindo a verificação da sintaxe, otimização, a geração de código, etc., **SingleBox:** efetuar a compilação completa, com a definição de TargetType para SingleBox. Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. | Não |

Fábrica de dados envia o consulte [definição de Script SearchLogProcessing.txt](#sample-u-sql-script) para a definição de script. 

## <a name="sample-u-sql-script"></a>Script de U-SQL de exemplo

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

No acima exemplo de script, a entrada e saída para o script está definido no  **@in**  e  **@out**  parâmetros. Os valores para  **@in**  e  **@out**  parâmetros do script U-SQL são transmitidos dinamicamente pela fábrica de dados utilizando a secção 'parameters'. 

Pode especificar outras propriedades, tais como degreeOfParallelism e a prioridade, bem como na sua definição de pipeline para as tarefas que são executados no serviço do Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
Na definição do pipeline de exemplo, e terminar parâmetros recebem com valores impostos. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

É possível utilizar em vez disso, os parâmetros dinâmicos. Por exemplo: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Neste caso, os ficheiros de entrada são ainda captados na pasta /datalake/input e ficheiros de saída são gerados na pasta /datalake/output. Os nomes de ficheiro são dinâmicos, com base na hora de início do setor.  

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes que explicam como transformar dados de outras formas: 

* [Atividade do ramo de registo](transform-data-using-hadoop-hive.md)
* [Atividade do PIg](transform-data-using-hadoop-pig.md)
* [Atividade de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
