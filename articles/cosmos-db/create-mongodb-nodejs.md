---
title: "Ligar uma aplicação MongoDB ao Azure Cosmos DB com o Node.js | Microsoft Docs"
description: "Saiba como ligar uma aplicação MongoDB Node.js existente ao Azure Cosmos DB"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0e5657e4d110af095c934431cb6e3bf8824f791d
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB: migrar uma aplicação Web MongoDB Node.js existente 

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como utilizar uma aplicação [MongoDB](../documentdb/documentdb-protocol-mongodb.md) existente escrita em Node.js e como ligá-la à base de dados do Azure Cosmos DB, que suporta ligações de cliente da MongoDB. Por outras palavras, a aplicação Node.js sabe apenas que está a ligar a uma base de dados através das APIs MongoDB. É transparente para a aplicação onde os dados são armazenados no Azure Cosmos DB.

Quando terminar, terá uma aplicação MEAN (MongoDB, Express, AngularJS e Node.js) em execução no [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). 

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/create-mongodb-nodejs/meanjs-in-azure.png)

## <a name="prerequisites"></a>Pré-requisitos 

Antes de iniciar este guia de introdução, verifique se a [CLI do Azure está instalada](https://docs.microsoft.com/cli/azure/install-azure-cli) no computador. Além disso, precisa do [Node.js](https://nodejs.org/) e [Git](http://www.git-scm.com/downloads). Deverá executar os comandos `az`, `npm` e `git`.

Deve ter conhecimento prático do Node.js. Este guia de introdução não foi concebido para o ajudar a desenvolver aplicações Node.js em geral.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

Execute os seguintes comandos para clonar o repositório de exemplo. Este repositório de exemplo contém a aplicação [MEAN.js](http://meanjs.org/) predefinida. 

```bash
git clone https://github.com/prashanthmadi/mean
```

## <a name="run-the-application"></a>Executar a aplicação

Instale os pacotes necessários e inicie a aplicação.

```bash
cd mean
npm install
npm start
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Agora, utilize a CLI 2.0 do Azure numa janela de terminal para criar os recursos necessários para alojar a aplicação Node.js no Serviço de Aplicações do Azure.  Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã. 

```azurecli 
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Adicionar o módulo Azure Cosmos DB

Para utilizar os comandos do Azure Cosmos DB, adicione o módulo Azure Cosmos DB. 

```azurecli
az component update --add cosmosdb
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Web, bases de dados e contas de armazenamento, são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos na região Europa Ocidental. Escolha um nome exclusivo para o grupo de recursos.

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta do Azure Cosmos DB com o comando [az cosmosdb create](/cli/azure/cosmosdb#create).

No comando seguinte, substitua o nome exclusivo da conta do Azure Cosmos DB, quando vir o marcador de posição `<cosmosdb_name>`. Este nome exclusivo será utilizado como parte do ponto final do Azure Cosmos DB (`https://<cosmosdb_name>.documents.azure.com/`). Por isso, o nome tem de ser exclusivo em todas as contas do Azure Cosmos DB. 

```azurecli
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro `--kind MongoDB` permite ligações de cliente da MongoDB.

Após criar a conta do DB Cosmos Azure, a CLI do Azure mostra informações semelhantes ao exemplo seguinte. 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb_name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Ligar a aplicação Node.js à base de dados

Neste passo, vai ligar a aplicação de exemplo MEAN.js a uma base de dados do DB Cosmos Azure que acabou de criar, com uma cadeia de ligação da MongoDB. 

## <a name="retrieve-the-key"></a>Obter a chave

Para ligar a uma base de dados do DB Cosmos Azure , precisará da chave da base de dados. Utilize o comando [az documentdb list-keys](/cli/azure/documentdb#list-keys) para obter a chave primária.

```azurecli
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

A CLI do Azure apresenta informações semelhantes ao exemplo seguinte. 

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

Copie o valor de `primaryMasterKey` para um editor de texto. Estas informações são necessárias no passo seguinte.

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de ligação na aplicação Node.js

No repositório MEAN.js, abra `config/env/local-development.js`.

Substitua o conteúdo deste ficheiro pelo código seguinte. Confirme que também substitui os dois marcadores de posição `<cosmosdb_name>` pelo nome da conta do DB Cosmos Azure e o marcador de posição `<primary_master_key>` pela chave que copiou no passo anterior.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

> [!NOTE] 
> A opção `ssl=true` é importante porque o [DB Cosmos Azure requer o SSL](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
>
>

Guarde as alterações.

### <a name="run-the-application-again"></a>Execute novamente a aplicação.

Execute novamente `npm start`. 

```bash
npm start
```

Uma mensagem de consola deverá agora indicar que o ambiente de desenvolvimento está ativo e em execução. 

Navegue para `http://localhost:3000` num browser. Clique em **Inscrever-se**, no menu superior, e tente criar dois utilizadores fictícios. 

A aplicação MEAN.js de exemplo armazena os dados do utilizador na base de dados. Se o procedimento for concluído com êxito e a aplicação MEAN.js iniciar automaticamente sessão com o utilizador criado, significa que a ligação do DB Cosmos Azure está a funcionar. 

![A aplicação MEAN.js estabelece ligação com êxito ao MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Ver dados no Data Explorer

Os dados armazenados por um Azure Cosmos DB estão disponíveis para visualização, consulta e execução da lógica de negócio no portal do Azure.

Para ver, consultar e trabalhar com os dados do utilizador criados no passo anterior, inicie a sessão no [portal do Azure](https://portal.azure.com) no browser.

Na caixa de pesquisa na parte superior, escreva Azure Cosmos DB. Quando for apresentado o painel da conta do Cosmos DB, selecione a sua conta do Cosmos DB. No painel de navegação esquerdo, clique em Data Explorer. Expanda a coleção no painel Coleções e, em seguida, pode ver os documentos na coleção, consultar os dados e, ainda, criar e executar UDFs, acionadores e procedimentos armazenados. 

![Data Explorer no portal do Azure](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Implementar a aplicação Node.js no Azure

Neste passo, vai implementar a aplicação Node.js ligada a MongoDB no Azure Cosmos DB.

Poderá ter reparado que o ficheiro de configuração que alterou anteriormente se destina ao ambiente de desenvolvimento (`/config/env/local-development.js`). Ao implementar a aplicação no Serviço de Aplicações, esta será executada, por predefinição, no ambiente de produção. Por isso, agora, tem de efetuar a mesma alteração no ficheiro de configuração respetivo.

No repositório MEAN.js, abra `config/env/production.js`.

No objeto `db`, substitua o valor do `uri`, conforme apresentado no exemplo seguinte. Confirme que substitui os marcadores de posição, como indicado anteriormente.

```javascript
'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
```

No terminal, confirme todas as alterações no Git. Pode copiar os dois comandos para executá-los em simultâneo.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB e a criar uma coleção da MongoDB com o Data Explorer. Agora, pode migrar os dados da MongoDB para o Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importar dados da MongoDB para o Azure Cosmos DB](../documentdb/documentdb-mongodb-migrate.md)

