---
title: Transformar dados utilizando o script U-SQL - Azure | Microsoft Docs
description: "Saiba como processar ou dados de transformação através da execução de scripts U-SQL no serviço de computação do Azure Data Lake Analytics."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: ff91a3da978fd027605b3674eae14d1d74b309cd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados através da execução de scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-usql-activity.md)
> * [Versão 2 - Pré-visualização](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [atividade U-SQL no V2](../transform-data-using-data-lake-analytics.md).

Um pipeline de um Azure data factory processa dados nos serviços do storage ligadas utilizando os serviços ligados de computação. Contém uma sequência de atividades em que cada atividade executa uma operação de processamento específico. Este artigo descreve o **atividade de U-SQL de análise do Data Lake** que executa um **U-SQL** script num **Azure Data Lake Analytics** serviço ligado de computação. 

Crie uma conta do Azure Data Lake Analytics antes de criar um pipeline com uma atividade de U-SQL de análise do Data Lake. Para saber mais sobre o Azure Data Lake Analytics, consulte [introdução ao Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Reveja o [construir o seu primeiro tutorial de pipeline](data-factory-build-your-first-pipeline.md) ligado, para obter passos detalhados criar uma fábrica de dados, serviços, conjuntos de dados e um pipeline. Utilize fragmentos JSON com o Editor do Data Factory ou o Visual Studio ou o Azure PowerShell para criar entidades do Data Factory.

## <a name="supported-authentication-types"></a>Tipos de autenticação suportados
Atividade U-SQL suporta abaixo tipos de autenticação em relação a análise do Data Lake:
* Autenticação do principal de serviço
* Autenticação de credencial (OAuth) do utilizador 

Recomendamos que utilize autenticação principal de serviço, especialmente para uma execução agendada do U-SQL. Comportamento de expiração do token pode ocorrer com autenticação de credenciais do utilizador. Para detalhes de configuração, consulte o [ligado propriedades do serviço](#azure-data-lake-analytics-linked-service) secção.

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado do Azure Data Lake Analytics
Criar um **Azure Data Lake Analytics** um Azure data factory do serviço de computação de serviço ligado para ligar um Azure Data Lake Analytics. A atividade U-SQL do Data Lake Analytics no pipeline refere-se a este serviço ligado. 

A tabela seguinte fornece descrições para as propriedades genéricas utilizadas na definição JSON. Ainda pode escolher entre o principal de serviço e a autenticação de credenciais do utilizador.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| **accountName** |Nome de conta do Azure Data Lake Analytics. |Sim |
| **dataLakeAnalyticsUri** |URI do Azure Data Lake Analytics. |Não |
| **subscriptionId** |Id de subscrição do Azure |Não (se não for especificado, a subscrição do data factory é utilizada). |
| **resourceGroupName** |Nome do grupo de recursos do Azure |Não (se não for especificado, grupo de recursos do data factory é utilizado). |

### <a name="service-principal-authentication-recommended"></a>Autenticação principal de serviço (recomendada)
Para utilizar a autenticação principal de serviço, registe uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder o acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação de serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do inquilino

Utilize a autenticação principal de serviço, especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique o ID de cliente. da aplicação | Sim |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Sim |
| **inquilino** | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Sim |

**Exemplo: Autenticação principal do serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticação de credenciais de utilizador
Em alternativa, pode utilizar a autenticação de credencial de utilizador para o Data Lake Analytics especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **autorização** | Clique em de **autorizar** botão no Editor do Data Factory e introduza as credenciais que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim |
| **ID de sessão** | ID de sessão OAuth da sessão de autorização do OAuth. Cada ID de sessão é exclusivo e pode ser utilizado apenas uma vez. Esta definição é gerada automaticamente quando utiliza o Editor do Data Factory. | Sim |

**Exemplo: Autenticação de credenciais de utilizador**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização que gerou utilizando o **autorizar** botão expira após algum tempo. Consulte a tabela seguinte para os tempos de expiração para diferentes tipos de contas de utilizador. Poderá ver o seguinte erro mensagem quando a autenticação **token expira**: erro de operação de credencial: invalid_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: A concessão de acesso fornecida está expirada ou revogada. ID de rastreio: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

| Tipo de utilizador | Expira após |
|:--- |:--- |
| Contas de utilizador não são geridas pelo Azure Active Directory (@hotmail.com, @live.com, etc.) |12 horas |
| Contas de utilizadores geridas através do Azure Active Directory (AAD) |executar nos últimos 14 dias após o último setor. <br/><br/>90 dias, se um setor com base no serviço ligado com base em OAuth é executado, pelo menos, uma vez a cada 14 dias. |

Para evitar/resolva este erro, a reautorização utilizando o **autorizar** botão quando o **token expira** e reimplemente o serviço ligado. Também pode gerar valores para **sessionId** e **autorização** propriedades através de programação utilizando o código da seguinte forma:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Consulte [AzureDataLakeStoreLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), e [AuthorizationSessionGetResponse classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tópicos para obter detalhes sobre as classes de fábrica de dados utilizadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe de WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O fragmento JSON seguinte define um pipeline com uma atividade de U-SQL de análise do Data Lake. Definição da atividade tem uma referência para o serviço ligado do Azure Data Lake Analytics que criou anteriormente.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

A tabela seguinte descreve os nomes e descrições das propriedades que são específicas para esta atividade. 

| Propriedade            | Descrição                              | Necessário                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| tipo                | A propriedade de tipo tem de ser definida **DataLakeAnalyticsU SQL**. | Sim                                      |
| linkedServiceName   | Referência para o Azure Data Lake Analytics registado como um serviço ligado no Factory de dados | Sim                                      |
| ScriptPath          | Caminho para a pasta que contém o script U-SQL. Nome do ficheiro é maiúsculas e minúsculas. | Não (se for utilizar o script)                   |
| scriptLinkedService | Serviço ligado que liga o armazenamento que contém o script para a fábrica de dados | Não (se for utilizar o script)                   |
| Script              | Especifique o script inline de em vez de especificar scriptPath e scriptLinkedService. Por exemplo: `"script": "CREATE DATABASE test"`. | Não (se for utilizar scriptPath e scriptLinkedService) |
| degreeOfParallelism | O número máximo de nós em simultâneo utilizada para executar a tarefa. | Não                                       |
| prioridade            | Determina quais os que são colocados em fila trabalhos devem ser selecionados para ser executado primeiro. Menor número, maior prioridade. | Não                                       |
| parâmetros          | Parâmetros para o script U-SQL          | Não                                       |
| runtimeVersion      | Versão de tempo de execução do motor U-SQL a utilizar | Não                                       |
| compilationMode     | <p>Modo de compilação do U-SQL. Tem de ser um dos seguintes valores:</p> <ul><li>**Semântica:** só executar verificações semânticos e verificações sanity necessário.</li><li>**Completo:** efetuar a compilação completa, incluindo a verificação da sintaxe, otimização, a geração de código, etc.</li><li>**SingleBox:** efetuar a compilação completa, com a definição de TargetType para SingleBox.</li></ul><p>Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. </p> | Não                                       |

Consulte [definição de Script SearchLogProcessing.txt](#sample-u-sql-script) para a definição de script. 

## <a name="sample-input-and-output-datasets"></a>Entrada de exemplo e conjuntos de dados de saída
### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem num Azure Data Lake Store (SearchLog.tsv ficheiro na pasta datalake/entrada). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Conjunto de dados de saída
Neste exemplo, os dados de saída produzidos pelo script de U-SQL são armazenados num Azure Data Lake Store (pasta datalake/saída). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Serviço ligado de exemplo Data Lake Store
Segue-se a definição de exemplo que do Azure Data Lake Store ligado serviço utilizado pelos conjuntos de dados de entrada/saída. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Consulte [mover dados para e do Azure Data Lake Store](data-factory-azure-datalake-connector.md) artigo para obter descrições das propriedades JSON. 

## <a name="sample-u-sql-script"></a>Script de U-SQL de exemplo

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
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

Os valores para  **@in**  e  **@out**  parâmetros do script U-SQL são transmitidos dinamicamente pelo ADF utilizando a secção 'parameters'. Consulte a secção 'parameters' na definição do pipeline.

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


