---
title: "Enlace de tabela externa de funções do Azure (pré-visualização) | Microsoft Docs"
description: "Utilizar enlaces de tabela externa em funções do Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 7b226aa4ec71535aa0222389aacd74764a80021a
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-external-table-binding-preview"></a>Enlace de tabela externa de funções do Azure (pré-visualização)
Este artigo mostra como manipular dados de tabela em fornecedores de SaaS (por exemplo, o Sharepoint, o Dynamics) dentro da função com enlaces incorporadas. As funções do Azure suporta os enlaces de entrada e de saída para as tabelas externas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Ligações de API

Enlaces de tabela tirar partido das ligações externas de API para autenticar com 3rd fornecedores de SaaS de terceiros. 

Ao atribuir um enlace pode criar uma nova ligação de API ou utilizar uma ligação de API existente no mesmo grupo de recursos

### <a name="supported-api-connections-tables"></a>Ligações de API suportados (tabela) s

|Conector|Acionador|Input|Saída|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 para operações](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Folhas do Google](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 para Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Serviço de dados comuns](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x


> [!NOTE]
> Ligações de tabela externas também podem ser utilizadas em [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)

### <a name="creating-an-api-connection-step-by-step"></a>Criar uma ligação de API: passo a passo

1. Criar uma função > função personalizada ![criar uma função personalizada](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. Cenário `Experimental`  >  `ExternalTable-CSharp` modelo > crie um novo `External Table connection` 
 ![modelo de tabela de entrada escolha](./media/functions-bindings-storage-table/create-template-table.jpg)
1. Escolha o fornecedor de SaaS > Escolha/criar uma ligação ![SaaS configurar ligação](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. Selecione a ligação de API > criar a função ![criar função de tabela](./media/functions-bindings-storage-table/table-template-options.jpg)
1. Selecione`Integrate` > `External Table`
    1. Configure a ligação para utilizar a tabela de destino. Estas definições serão muito entre SaaS fornecedores. São contorno abaixo no [definições da origem de dados](#datasourcesettings)
![configurar tabela](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>Utilização

Neste exemplo estabelece ligação a uma tabela com o nome "Contacte" com o Id, FirstName e LastName, colunas. O código de lista de entidades de contacto na tabela e regista os nomes de primeiro e últimos.

### <a name="bindings"></a>Enlaces
```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```
`entityId`deve estar vazio para enlaces de tabela.

`ConnectionAppSettingsKey`identifica a definição de aplicação que armazena a cadeia de ligação de API. A definição de aplicação é criada automaticamente quando adicionar uma ligação de API a integrar IU.

Um conector tabela fornece conjuntos de dados e cada conjunto de dados contém tabelas. O nome do conjunto de dados predefinido é "predefinida". Os títulos de para um conjunto de dados e uma tabela de vários fornecedores de SaaS são listados abaixo:

|Conector|Conjunto de dados|Tabela|
|:-----|:---|:---| 
|**SharePoint**|Site|Lista do SharePoint
|**SQL**|Base de Dados|Tabela 
|**Folha do Google**|Folha de cálculo|Folha de Cálculo 
|**Excel**|Ficheiro do Excel|Folha 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>Utilização em c# #

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retreive table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
##Definições da origem de dados

### <a name="sql-server"></a>SQL Server

O script para criar e preencher a tabela de contacto é abaixo. dataSetName é "predefinida".

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets"></a>Folhas do Google
No Google Docs, crie uma folha de cálculo com uma folha de cálculo denominada `Contact`. O conector não é possível utilizar o nome a apresentar a folha de cálculo. As necessidades de nome interno (em negrito) para ser utilizado como dataSetName, por exemplo: `docs.google.com/spreadsheets/d/`  **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**  adicionar os nomes das colunas `Id`, `LastName`, `FirstName` para a primeira linha, em seguida, preencher dados em linhas subsequentes.

### <a name="salesforce"></a>Salesforce
dataSetName é "predefinida".

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
