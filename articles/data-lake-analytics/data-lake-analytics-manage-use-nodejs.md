---
title: Gerir a Análise do Azure Data Lake com o Azure SDK para Node.js | Microsoft Docs
description: Saiba como gerir contas de Data Lake Analytics, origens de dados, tarefas e os utilizadores que utilizam o Azure SDK para Node.js
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Gerir a Análise do Azure Data Lake com o Azure SDK para Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

O Azure SDK para Node.js pode ser utilizado para gerir contas, tarefas e catálogos de Análise do Azure Data Lake. Para ver o tópico de gestão sobre a utilização de outras ferramentas, clique para selecionar o separador acima.

Atualmente, suporta:

* **Versão Node.js: 0.10.0 ou superior**
* **Versão de API REST para a Conta: 2015-10-01-preview**
* **Versão de API REST para o Catálogo: 2015-10-01-preview**
* **Versão de API REST para a Tarefa: 2016-03-20-preview**

## Funcionalidades
* Gestão de contas: criar, obter, listar, atualizar e eliminar.
* Gestão de tarefas: submeter, obter, listar, cancelar.
* Gestão de catálogos: obter, listar, criar (segredos), atualizar (segredos), eliminar (segredos).

## Como instalar
```bash
npm install azure-arm-datalake-analytics
```

## Autenticar com o Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Criar o cliente de Data Lake Analytics
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## Criar uma conta de Data Lake Analytics
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Obter uma lista de tarefas
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Obter uma lista de bases de dados no Catálogo de Data Lake Analytics
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Consultar também
* [Microsoft Azure SDK para Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK para Node.js - Gestão do Data Lake Store](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

<!--HONumber=Sep16_HO3-->


