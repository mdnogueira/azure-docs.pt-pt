---
title: Copiar dados de e para o Azure Data Lake Store | Microsoft Docs
description: Saiba como copiar dados e de Data Lake Store utilizando o Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f74a953d04e8633e802b33903de603b39ac08e9b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="copy-data-to-and-from-data-lake-store-by-using-data-factory"></a>Copiar os dados e de Data Lake Store utilizando o Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-azure-datalake-connector.md)
> * [Versão 2 - Pré-visualização](../connector-azure-data-lake-store.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector do Azure Data Lake Store no V2](../connector-azure-data-lake-store.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados para e do Azure Data Lake Store. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, uma descrição geral do movimento de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Azure Data Lake Store** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **ao Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Crie uma conta de Data Lake Store antes de criar um pipeline com atividade de cópia. Para obter mais informações, consulte [introdução ao Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Tipos de autenticação suportados
O conector do Data Lake Store suporta estes tipos de autenticação:
* Autenticação do principal de serviço
* Autenticação de credencial (OAuth) do utilizador 

Recomendamos que utilize autenticação principal de serviço, especialmente para uma cópia de dados agendada. Comportamento de expiração do token pode ocorrer com autenticação de credenciais do utilizador. Para detalhes de configuração, consulte o [ligado propriedades do serviço](#linked-service-properties) secção.

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados para/de um Azure Data Lake Store utilizando APIs/ferramentas diferentes.

A forma mais fácil de criar um pipeline para copiar dados consiste em utilizar o **Assistente para copiar**. Para um tutorial sobre como criar um pipeline, utilizando o Assistente de cópia, consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para um Azure Data Lake Store, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e o Azure Data Lake store à fábrica de dados. As propriedades de serviço ligado que são específicas para o Azure Data Lake Store, consulte [ligado propriedades do serviço](#linked-service-properties) secção. 
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de blob e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a pasta e o caminho do ficheiro no arquivo Data Lake que contém os dados copiados a partir do blob storage. As propriedades do conjunto de dados que são específicas para o Azure Data Lake Store, consulte [propriedades do dataset](#dataset-properties) secção.
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. No exemplo mencionado anteriormente, utilizar BlobSource como uma origem e AzureDataLakeStoreSink como um sink para a atividade de cópia. Da mesma forma, se estiver a copiar do Azure Data Lake Store para armazenamento de Blobs do Azure, utilizar AzureDataLakeStoreSource e BlobSink na atividade de cópia. Para as propriedades da atividade de cópia que são específicas para o Azure Data Lake Store, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. Para obter detalhes sobre como utilizar um arquivo de dados como uma origem ou de um receptor de mensagens em fila, clique na ligação na secção anterior para o arquivo de dados.  

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para/de um Azure Data Lake Store, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) secção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para o Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Um serviço ligado liga um arquivo de dados para uma fábrica de dados. Criar um serviço ligado do tipo **AzureDataLakeStore** para ligar os dados de Data Lake Store à fábrica de dados. A tabela seguinte descreve os elementos JSON específicos dos serviços do Data Lake Store ligado. Pode escolher entre o principal de serviço e a autenticação de credenciais do utilizador.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **tipo** | A propriedade de tipo tem de ser definida **AzureDataLakeStore**. | Sim |
| **dataLakeStoreUri** | Informações sobre a conta do Azure Data Lake Store. Estas informações demora um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| **subscriptionId** | ID de subscrição do Azure à qual pertence a conta de Data Lake Store. | Obrigatório para sink |
| **resourceGroupName** | Nome do grupo de recursos do Azure à qual pertence a conta de Data Lake Store. | Obrigatório para sink |

### <a name="service-principal-authentication-recommended"></a>Autenticação principal de serviço (recomendada)
Para utilizar a autenticação principal de serviço, registe uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder o acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação de serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do inquilino

> [!TIP]
> Certifique-se de que conceder as serviço principal permissões adequadas no Azure Data Lake Store:
>- Se utilizar o Assistente para copiar para criar pipelines de, pelo menos conceder **leitor** função no controlo de acesso de conta (IAM). Além disso, pelo menos a conceder **leitura + executar** permissão para a raiz do Data Lake Store ("/") e os respectivos valores secundários. Caso contrário, poderá ver a mensagem "as credenciais fornecidas são inválidas."
>- Para utilizar o Data Lake Store como origem, conceder, pelo menos, **leitura + executar** permissão lista e copie o conteúdo de uma pasta de acesso de dados ou **leitura** permissão para copiar um ficheiro único. Sem requisito de controlo de conta de acesso de nível.
>- Para utilizar o Data Lake Store como sink, conceder, pelo menos, **escrever + executar** permissão para criar itens subordinados na pasta de acesso a dados. E se utilizar o Azure IR para capacitar cópia (origem e dependente são na nuvem), para permitir que o Data Factory detetar região do Data Lake Store, conceder, pelo menos, **leitor** função no controlo de acesso de conta (IAM). Se quiser evitar esta função IAM [especificar executionLocation](data-factory-data-movement-activities.md#global) com a localização do Data Lake Store na atividade de cópia.

Utilize a autenticação principal de serviço, especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique o ID de cliente. da aplicação | Sim |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Sim |
| **inquilino** | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Sim |

**Exemplo: Autenticação principal do serviço**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticação de credenciais de utilizador
Em alternativa, pode utilizar a autenticação de credenciais de utilizador para copiar de ou para o Data Lake Store, especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **autorização** | Clique em de **autorizar** botão no Editor do Data Factory e introduza as credenciais que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim |
| **ID de sessão** | ID de sessão OAuth da sessão de autorização do OAuth. Cada ID de sessão é exclusivo e pode ser utilizado apenas uma vez. Esta definição é gerada automaticamente quando utiliza o Editor do Data Factory. | Sim |

**Exemplo: Autenticação de credenciais de utilizador**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização que geram utilizando o **autorizar** botão expira após um determinado período de tempo. A seguinte mensagem de erro significa que o token de autenticação expirou:

Erro de operação de credencial: invalid_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: A concessão de acesso fornecida está expirada ou revogada. ID de rastreio: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z.

A tabela seguinte mostra os tempos de expiração de diferentes tipos de contas de utilizador:


| Tipo de utilizador | Expira após |
|:--- |:--- |
| Contas de utilizador *não* geridas pelo Azure Active Directory (por exemplo, @hotmail.com ou @live.com) |12 horas |
| Contas de utilizadores geridas pelo Azure Active Directory |executar nos últimos 14 dias após o último setor <br/><br/>90 dias, se um setor com base num serviço ligado com base em OAuth é executado, pelo menos, uma vez a cada 14 dias |

Se alterar a palavra-passe antes da data de expiração do token, o token expira imediatamente. Verá a mensagem mencionada anteriormente nesta secção.

Pode voltar a autorizar a conta ao utilizar o **autorizar** botão quando o token expira para voltar a implementar o serviço ligado. Também pode gerar valores para o **sessionId** e **autorização** propriedades através de programação utilizando o seguinte código:


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
Para obter detalhes sobre as classes de fábrica de dados utilizados no código, consulte o [AzureDataLakeStoreLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), e [ Classe de AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tópicos. Adicionar uma referência à versão `2.9.10826.1824` de `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` para o `WindowsFormsWebAuthenticationDialog` classe utilizado no código.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar os dados de entrada num arquivo Data Lake, definir o **tipo** propriedade do conjunto de dados para **AzureDataLakeStore**. Definir o **linkedServiceName** serviço ligado de propriedade do conjunto de dados com o nome do Data Lake Store. Para obter uma lista completa de secções JSON e propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Secções de um conjunto de dados em JSON, tais como **estrutura**, **disponibilidade**, e **política**, são semelhantes para todos os tipos de conjunto de dados (SQL database do Azure, BLOBs do Azure e a tabela do Azure, para exemplo). O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações como a localização e o formato dos dados no arquivo de dados. 

O **typeProperties** secção para um conjunto de dados do tipo **AzureDataLakeStore** contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **folderPath** |Caminho para o contentor e a pasta no Data Lake Store. |Sim |
| **nome de ficheiro** |Nome do ficheiro no Azure Data Lake Store. O **fileName** propriedade é opcional e maiúsculas e minúsculas. <br/><br/>Se especificar **fileName**, a atividade (incluindo cópia) funciona no ficheiro específico.<br/><br/>Quando **fileName** não for especificado, cópia inclui todos os ficheiros no **folderPath** no conjunto de dados de entrada.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída e **preserveHierarchy** não foram especificadas no receptor de atividade, o nome do ficheiro gerado está no formato de dados. _GUID_. txt '. Por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Não |
| **partitionedBy** |O **partitionedBy** propriedade é opcional. Pode utilizá-lo para especificar um caminho dinâmico e um nome de ficheiro para dados de séries de tempo. Por exemplo, **folderPath** podem ser parametrizadas para cada hora dos dados. Para obter detalhes e exemplos, consulte [a propriedade partitionedBy](#using-partitionedby-property). |Não |
| **formato** | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, e  **ParquetFormat**. Definir o **tipo** propriedade **formato** para um destes valores. Para obter mais informações, consulte o [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato ORC](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato ](data-factory-supported-file-and-compression-formats.md#parquet-format) secções no [formatos de ficheiro e compressão suportados pelo Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo. <br><br> Se pretender copiar ficheiros "como-é" entre arquivos baseados em ficheiros (cópia binário), ignore o `format` secção em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| **compressão** | Especifique o tipo e o nível de compressão de dados. Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão suportados pelo Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="the-partitionedby-property"></a>A propriedade partitionedBy
Pode especificar dinâmica **folderPath** e **fileName** propriedades para dados de séries de tempo com o **partitionedBy** propriedade, funções de Data Factory e variáveis do sistema. Para obter mais informações, consulte o [do Azure Data Factory - as funções e variáveis do sistema](data-factory-functions-variables.md) artigo.


No exemplo seguinte, `{Slice}` é substituída pelo valor da variável de sistema do Data Factory `SliceStart` no formato especificado (`yyyyMMddHH`). O nome `SliceStart` refere-se a hora de início do setor. O `folderPath` propriedade é diferente para cada setor, como em `wikidatagateway/wikisampledataout/2014100103` ou `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

No exemplo seguinte, ano, mês, dia e hora do `SliceStart` são extraídos em separado variáveis que são utilizadas pelo `folderPath` e `fileName` propriedades:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Para obter mais detalhes em conjuntos de dados de séries de tempo, agendar e setores, consulte o [conjuntos de dados no Azure Data Factory](data-factory-create-datasets.md) e [Data Factory agendamento e execução](data-factory-scheduling-and-execution.md) artigos. 


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Criar pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis no **typeProperties** secção de uma atividade variar de acordo com cada tipo de atividade. Para uma atividade de cópia, podem variam consoante os tipos de origens e sinks.

**AzureDataLakeStoreSource** suporta a propriedade seguinte no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **recursiva** |Indica se os dados é leitura recursivamente as subpastas ou apenas a pasta especificada. |TRUE (valor predefinido), False |Não |


**AzureDataLakeStoreSink** suporta as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **copyBehavior** |Especifica o comportamento de cópia. |<b>PreserveHierarchy</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem são criados no primeiro nível da pasta de destino. Os ficheiros de destino são criados com nomes gerado automaticamente.<br/><br/><b>MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro ou de blob, o nome de ficheiro intercalado é o nome especificado. Caso contrário, o nome de ficheiro é gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| Recursiva | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| VERDADEIRO |preserveHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| VERDADEIRO |flattenHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| VERDADEIRO |mergeFiles |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + ficheiro 5 é intercalado conteúdo para um ficheiro com nome de ficheiro gerado automaticamente |
| FALSO |preserveHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |
| FALSO |flattenHierarchy |Para uma pasta de origem Pasta1 com a estrutura seguinte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |
| FALSO |mergeFiles |Para uma pasta de origem Pasta1 com a estrutura seguinte:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdos são intercalados para um ficheiro com nome de ficheiro gerada automaticamente. nome gerado automaticamente para File1<br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e compressão suportados
Para obter mais informações, consulte o [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Exemplos JSON para copiar dados e de Data Lake Store
Os exemplos seguintes fornecem definições JSON de exemplo. Pode utilizar estas definições de exemplo para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados e de armazenamento do Data Lake Store e BLOBs do Azure. No entanto, os dados podem ser copiados _diretamente_ de qualquer uma das origens para qualquer um dos suportado sinks. Para obter mais informações, consulte a secção "arquivos de dados suportadas e formatos" no [mover dados utilizando a atividade de cópia](data-factory-data-movement-activities.md) artigo.  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exemplo: Copiar dados de Blob Storage do Azure ao Azure Data Lake Store
Mostra o código de exemplo nesta secção:

* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureDataLakeStore](#linked-service-properties).
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureDataLakeStore](#dataset-properties).
* A [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureDataLakeStoreSink](#copy-activity-properties).

Os exemplos mostram como séries de tempo dados a partir do Blob Storage do Azure são copiado para o Data Lake Store, a cada hora. 

**Serviço ligado do Armazenamento do Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Serviço ligado do Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Para detalhes de configuração, consulte o [ligado propriedades do serviço](#linked-service-properties) secção.
>

**Conjunto de dados de entrada do blobs do Azure**

No exemplo seguinte, dados são captados um blob de novo a cada hora (`"frequency": "Hour", "interval": 1`). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês e parte do dia da hora de início. O nome de ficheiro utiliza a parte de hora a hora de início. O `"external": true` definição informa o serviço fábrica de dados que a tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída do Azure Data Lake Store**

O exemplo seguinte copia dados ao Data Lake Store. Novos dados são copiados para o Data Lake Store a cada hora.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Atividade de cópia de um pipeline com uma origem de blob e um receptor de Data Lake Store**

No exemplo seguinte, o pipeline contém uma atividade de cópia está configurada para utilizar a entrada e saída de conjuntos de dados. A atividade de cópia está agendada para execução a cada hora. No pipeline de definição de JSON, o `source` tipo está definido como `BlobSource`e o `sink` tipo está definido como `AzureDataLakeStoreSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exemplo: Copiar dados do Azure Data Lake Store para um blob do Azure
Mostra o código de exemplo nesta secção:

* Um serviço ligado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureDataLakeStore](#dataset-properties).
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [AzureDataLakeStoreSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O código copia dados de séries de tempo do Data Lake Store para um blob do Azure a cada hora. 

**Serviço ligado do Azure Data Lake Store**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Para detalhes de configuração, consulte o [ligado propriedades do serviço](#linked-service-properties) secção.
>

**Serviço ligado do Armazenamento do Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do Azure Data Lake**

Neste exemplo, definição `"external"` para `true` informa o serviço fábrica de dados que a tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```json
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
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
        "external": true,
        "availability": {
            "frequency": "Hour",
              "interval": 1
        },
        "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
        }
      }
}
```
**Conjunto de dados de saída do blob do Azure**

No exemplo seguinte, são escritos dados no blob de novo a cada hora (`"frequency": "Hour", "interval": 1`). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e parte horas a hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia num pipeline com uma origem de Azure Data Lake Store e um receptor de blob**

No exemplo seguinte, o pipeline contém uma atividade de cópia está configurada para utilizar a entrada e saída de conjuntos de dados. A atividade de cópia está agendada para execução a cada hora. No pipeline de definição de JSON, o `source` tipo está definido como `AzureDataLakeStoreSource`e o `sink` tipo está definido como `BlobSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

Na definição da atividade de cópia, também pode mapear colunas do conjunto de dados de origem para colunas no conjunto de dados sink. Para obter mais informações, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Para saber mais sobre os fatores que afetam o desempenho de atividade de cópia e de como otimizá-lo, consulte o [guia Otimização e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md) artigo.
