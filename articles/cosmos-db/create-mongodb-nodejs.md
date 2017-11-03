---
title: "Ligar uma aplicação MongoDB ao Azure Cosmos DB com o Node.js | Microsoft Docs"
description: "Saiba como ligar uma aplicação MongoDB Node.js existente ao Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/19/2017
ms.author: mimig
ms.openlocfilehash: 335c271f3e46d3b6b23e1fcac53e39b89ece796c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB: migrar uma aplicação Web MongoDB Node.js existente 

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como utilizar uma aplicação [MongoDB](mongodb-introduction.md) existente escrita em Node.js e como ligá-la à base de dados do Azure Cosmos DB, que suporta ligações de cliente da MongoDB. Por outras palavras, a aplicação Node.js sabe apenas que está a ligar a uma base de dados através das APIs MongoDB. É transparente para a aplicação onde os dados são armazenados no Azure Cosmos DB.

Quando tiver terminado, terá uma média da aplicação (MongoDB, Express, Angular e Node.js) em execução no [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Pré-requisitos 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Além da CLI do Azure, precisa de ter o [Node.js](https://nodejs.org/) e o [Git](http://www.git-scm.com/downloads) instalados localmente para executar os comandos `npm` e `git`.

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
A aplicação irá tentar ligar a uma origem de MongoDB falhar, avançar e sair da aplicação quando devolve o resultado "[MongoError: ligar ECONNREFUSED 127.0.0.1:27017]".

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Se estiver a utilizar uma CLI do Azure instalada, inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã. Pode ignorar esta etapa se estiver a utilizar o Azure Cloud Shell.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Adicionar o módulo Azure Cosmos DB

Se estiver a utilizar uma CLI do Azure instalada, veja se o componente `cosmosdb` já está instalado ao executar o comando `az`. Se `cosmosdb` está na lista de comandos de base, siga para o próximo comando. Pode ignorar esta etapa se estiver a utilizar o Azure Cloud Shell.

Se `cosmosdb` não está na lista de comandos de base, reinstale a [CLI do Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Web, bases de dados e contas de armazenamento, são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos na região Europa Ocidental. Escolha um nome exclusivo para o grupo de recursos.

Se estiver a utilizar o Azure Cloud Shell, clique em **Experimente**, siga as instruções no ecrã para iniciar sessão e, em seguida, copie o comando para a linha de comandos.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta do Azure Cosmos DB com o comando [az cosmosdb create](/cli/azure/cosmosdb#create).

No comando seguinte, substitua o nome exclusivo da conta do Azure Cosmos DB, quando vir o marcador de posição `<cosmosdb-name>`. Este nome exclusivo será utilizado como parte do ponto final do Azure Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`). Por isso, o nome tem de ser exclusivo em todas as contas do Azure Cosmos DB. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro `--kind MongoDB` permite ligações de cliente da MongoDB.

Após criar a conta do DB Cosmos Azure, a CLI do Azure mostra informações semelhantes ao exemplo seguinte. 

> [!NOTE]
> Este exemplo utiliza o JSON como formato de saída da CLI do Azure, que é a predefinição. Para utilizar outro formato de saída, veja [Formatos de saída para os comandos da CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Ligar a aplicação Node.js à base de dados

Neste passo, vai ligar a aplicação de exemplo MEAN.js a uma base de dados do DB Cosmos Azure que acabou de criar, com uma cadeia de ligação da MongoDB. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de ligação na aplicação Node.js

No repositório MEAN.js, abra `config/env/local-development.js`.

Substitua o conteúdo deste ficheiro pelo código seguinte. Certifique-se também de que substitui os dois marcadores de posição `<cosmosdb-name>` pelo nome da sua conta do Azure Cosmos DB.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Obter a chave

Para ligar a uma base de dados do DB Cosmos Azure , precisará da chave da base de dados. Utilize o comando [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) para obter a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

A CLI do Azure apresenta informações semelhantes ao exemplo seguinte. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Copie o valor de `primaryMasterKey`. Cole-o em `<primary_master_key>` em `local-development.js`.

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
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> A opção `ssl=true` é importante porque o [DB Cosmos Azure requer o SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

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
> [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)
