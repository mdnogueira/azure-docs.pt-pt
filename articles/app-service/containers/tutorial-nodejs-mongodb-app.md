---
title: "Criar uma aplicação web Node.js e MongoDB no App Service do Azure no Linux | Microsoft Docs"
description: "Saiba como obter uma aplicação Node.js a funcionar no serviço de aplicações do Azure no Linux, com uma ligação à base de dados do Cosmos base de dados com uma cadeia de ligação do MongoDB."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a9b321fcf8a8d1234989a9433da227142d954cb4
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure-app-service-on-linux"></a>Criar uma aplicação web Node.js e MongoDB no App Service do Azure no Linux

[Serviço de aplicações no Linux](app-service-linux-intro.md) fornece uma serviço utilizando o sistema operativo Linux de alojamento na web altamente dimensionável e aplicação de patches personalizada. Este tutorial mostra como criar uma aplicação web Node.js, ligue-o localmente para uma base de dados de MongoDB, em seguida, implementar no Azure ligado a uma base de dados de CosmosDB utilizando a API do MongoDB. Quando tiver terminado, terá de uma aplicação média (MongoDB, Express, AngularJS e Node.js) em execução no App Service no Linux. Simplicidade, a aplicação de exemplo utiliza o [estrutura de web MEAN.js](http://meanjs.org/).

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Pode saber como:

> [!div class="checklist"]
> * Criar uma base de dados de CosmosDB utilizando a API do MongoDB no Azure
> * Ligar uma aplicação Node.js para MongoDB
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Registos de diagnóstico de fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instale o Git](https://git-scm.com/).
1. [Instalar Node.js v-6.0 ou posterior e NPM](https://nodejs.org/)
1. [Instalar Gulp.js](http://gulpjs.com/) (requerido pelo [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Instalar e executar a edição de Comunidade do MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>MongoDB local do teste

Abra a janela de terminal e `cd` para o `bin` diretório de instalação do MongoDB. Pode utilizar esta janela de terminal para executar todos os comandos neste tutorial.

Executar `mongo` no terminal para ligar ao servidor MongoDB local.

```bash
mongo
```

Se a ligação for bem-sucedida, em seguida, a base de dados de MongoDB já está em execução. Se não, certifique-se de que a base de dados de MongoDB local é iniciada, seguindo os passos indicados em [instalar edição Community do MongoDB](https://docs.mongodb.com/manual/administration/install-community/). Muitas vezes, MongoDB está instalado, mas continua a precisar inicie-o executando `mongod`.

Quando tiver terminado a testar a sua base de dados de MongoDB, escreva `Ctrl+C` no terminal.

## <a name="create-local-nodejs-app"></a>Criar aplicação local do Node.js

Neste passo, configure o projeto de Node.js local.

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Na janela de terminal, `cd` para um diretório de trabalho.

Execute o seguinte comando para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Este repositório de exemplo inclui uma cópia do [MEAN.js repositório](https://github.com/meanjs/mean). Este é modificado para ser executada no serviço de aplicações (para obter mais informações, consulte o repositório de MEAN.js [ficheiro Leia-me](https://github.com/Azure-Samples/meanjs/blob/master/README.md)).

### <a name="run-the-application"></a>Executar a aplicação

Execute os seguintes comandos para instalar os pacotes necessários e iniciar a aplicação.

```bash
cd meanjs
npm install
npm start
```

Ignore o aviso de config.domain. Quando a aplicação é totalmente carregada, verá algo semelhante a mensagem seguinte:

```txt
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Navegue para `http://localhost:3000` num browser. Clique em **inscrever-se** no menu superior e criar um utilizador de teste. 

A aplicação MEAN.js de exemplo armazena os dados do utilizador na base de dados. Se for concluída com êxito ao criar um utilizador e iniciar sessão, em seguida, a aplicação é escrever dados na base de dados local do MongoDB.

![A aplicação MEAN.js estabelece ligação com êxito ao MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selecione **Admin > Gerir artigos** para adicionar alguns artigos.

Para parar o Node.js em qualquer altura, prima `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Criar produção MongoDB

Neste passo, vai criar uma base de dados de MongoDB no Azure. Quando a aplicação for implementada para o Azure, este utiliza esta base de dados de nuvem.

Para o MongoDB, este tutorial utiliza [Azure Cosmos DB](/azure/documentdb/). BD do cosmos suporta ligações de cliente do MongoDB.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>Criar uma conta de base de dados do Cosmos

A Shell de nuvem, criar uma conta de base de dados do Cosmos com o [az cosmosdb criar](/cli/azure/cosmosdb#create) comando.

O seguinte comando, substitua um nome de base de dados do Cosmos exclusivo para o  *\<cosmosdb_name >* marcador de posição. Este nome é utilizado como parte do ponto final Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, por isso, o nome tem de ser exclusivo em todas as contas de base de dados do Cosmos no Azure. O nome tem de conter apenas letras minúsculas, números e o caráter de hífen (-) e tem de ter entre 3 e 50 carateres de comprimento.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

O *– kind MongoDB* parâmetro permite ligações de cliente do MongoDB.

Quando é criada a conta de base de dados do Cosmos, a CLI do Azure mostra as informações semelhante ao seguinte exemplo:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies":
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Ligar a aplicação para produção MongoDB

Neste passo, ligar a aplicação de exemplo MEAN.js na base de dados do Cosmos DB que acabou de criar, utilizar uma cadeia de ligação do MongoDB.

### <a name="retrieve-the-database-key"></a>Obter a chave de base de dados

Para ligar à base de dados do Cosmos DB, terá da chave de base de dados. Na Shell de nuvem, utilize o [az cosmosdb lista chaves](/cli/azure/cosmosdb#list-keys) comando para obter a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

A CLI do Azure, mostra as informações semelhante ao seguinte exemplo:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copie o valor de `primaryMasterKey`. Estas informações são necessárias no passo seguinte.

<a name="devconfig"></a>

### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de ligação na aplicação Node.js

No seu repositório MEAN.js local, no _config/env/_ pasta, crie um ficheiro denominado _local production.js_. _. gitignore_ está configurado para manter este ficheiro fora do repositório.

Copie o seguinte código para a mesma. Não se esqueça de substituir os dois  *\<cosmosdb_name >* marcadores de posição com a base de dados do Cosmos nome de base de dados e substitua o  *\<primary_master_key >* marcador de posição com a chave copiada no passo anterior.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

O `ssl=true` opção é necessária porque [Cosmos DB requer SSL](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements).

Guarde as alterações.

### <a name="test-the-application-in-production-mode"></a>Testar a aplicação no modo de produção

Numa janela de terminal local, execute o seguinte comando para minify e scripts para o ambiente de produção do pacote. Este processo gera os ficheiros necessários para o ambiente de produção.

```bash
gulp prod
```

Numa janela de terminal local, execute o seguinte comando para utilizar a cadeia de ligação configurada no _config/env/local-production.js_. Ignore o erro de certificado e o aviso de config.domain.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production`Define a variável de ambiente que informa o Node.js para ser executado no ambiente de produção.  `node server.js`inicia o servidor do Node.js com `server.js` na raiz do repositório. Esta é a forma como a sua aplicação Node.js será carregada no Azure.

Quando a aplicação foi carregada, certifique-se de que está em execução no ambiente de produção:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Navegue para `http://localhost:8443` num browser. Clique em **inscrever-se** no menu superior e criar um utilizador de teste. Se estiver a criar um utilizador e iniciar sessão com êxito, em seguida, a aplicação está a escrever dados na base de dados de base de dados do Cosmos no Azure.

No terminal, pare o Node.js, escrevendo `Ctrl+C`.

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Neste passo, implementar a aplicação Node.js ligados MongoDB App Service do Azure.

### <a name="configure-local-git-deployment"></a>Configurar a implementação do git local

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-linux-based-web-app"></a>Criar uma aplicação de web baseadas em linux

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurar uma variável de ambiente

Por predefinição, o projeto de MEAN.js mantém _config/env/local-production.js_ fora do repositório de Git. Por isso, para a sua aplicação web do Azure, utilize as definições de aplicação para definir a cadeia de ligação do MongoDB.

Para configurar definições de aplicação, utilize o [atualização az webapp configuração appsettings](/cli/azure/webapp/config/appsettings#update) comando na Shell de nuvem.

O exemplo a seguir configura um `MONGODB_URI` definição de aplicação na sua aplicação web do Azure. Substitua o  *\<APP_NAME>.azurewebsites.NET >*,  *\<cosmosdb_name >*, e  *\<primary_master_key >* marcadores de posição.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Código do Node.js, vai aceder a esta definição de aplicação com `process.env.MONGODB_URI`, tal como acederia a qualquer variável de ambiente. 

No seu repositório MEAN.js local, abra _config/env/production.js_ (não _config/env/local-production.js_), que tem uma configuração específica de ambiente de produção. Tenha em atenção que a aplicação de MEAN.js predefinido já está configurado para utilizar o `MONGODB_URI` variável de ambiente que criou.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

Poderá reparar que o processo de implementação é executado [Gulp](http://gulpjs.com/) depois `npm install`. Serviço de aplicações não é executado Gulp ou Grunt tarefas durante a implementação, para que este repositório de exemplo tem dois ficheiros adicionais no respetivo diretório raiz para ativá-la:

- _.Deployment_ -este ficheiro indica ao serviço de aplicações para executar `bash deploy.sh` como o script de implementação personalizada.
- _Deploy.SH_ -o script de implementação personalizada. Se analisar o ficheiro, verá que é executada `gulp prod` depois `npm install` e `bower install`.

Pode utilizar esta abordagem para adicionar qualquer passo à sua implementação do Git. Se reiniciar a aplicação web do Azure em qualquer momento, serviço de aplicações não voltar a executar estas tarefas de automatização.

### <a name="browse-to-the-azure-web-app"></a>Navegue para a aplicação web do Azure

Navegue para a aplicação web implementada utilizando o seu browser.

```bash
http://<app_name>.azurewebsites.net
```

Clique em **inscrever-se** no menu superior e criar um utilizador fictício.

Se for concluída com êxito e a aplicação automaticamente inicia sessão para o utilizador criado, em seguida, a aplicação de MEAN.js no Azure tiver conectividade à base de dados de base de dados do Cosmos API do MongoDB.

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selecione **Admin > Gerir artigos** para adicionar alguns artigos.

**Parabéns!** Estiver a executar uma aplicação para condicionada por dados Node.js no App Service do Azure no Linux.

## <a name="update-data-model-and-redeploy"></a>Modelo de dados de atualização e volte a implementar

Neste passo, altere o `article` modelo de dados e publicar a sua alteração para o Azure.

### <a name="update-the-data-model"></a>Atualizar o modelo de dados

No seu repositório MEAN.js local, abra _modules/articles/server/models/article.server.model.js_.

No `ArticleSchema`, adicione um `String` tipo denominado `comment`. Quando estiver pronto, o código de esquema deve ter o seguinte aspeto:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Atualize o código de artigos

Atualizar o resto do seu `articles` código para utilizar `comment`.

Existem cinco ficheiros tem de modificar: o controlador de servidor e as vistas de quatro cliente. 

Abra _modules/articles/server/controllers/articles.server.controller.js_.

No `update` de função, adicione uma atribuição de `article.comment`. O código seguinte mostra o concluída `update` função:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Abra _modules/articles/client/views/view-article.client.view.html_.

Apenas acima da tag de fecho `</section>` tag, adicione a seguinte linha para apresentar `comment` juntamente com o resto dos dados artigo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Abra _modules/articles/client/views/list-articles.client.view.html_.

Apenas acima da tag de fecho `</a>` tag, adicione a seguinte linha para apresentar `comment` juntamente com o resto dos dados artigo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/list-articles.client.view.html_.

Dentro do `<div class="list-group">` elemento e apenas acima da tag de fecho `</a>` tag, adicione a seguinte linha para apresentar `comment` juntamente com o resto dos dados artigo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/form-article.client.view.html_.

Localizar o `<div class="form-group">` elemento com o botão para submeter, que se parece com isto:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Apenas acima esta etiqueta, adicione outro `<div class="form-group">` elemento que permite que as pessoas editar o `comment` campo. O novo elemento deve ter o seguinte aspeto:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testar as suas alterações localmente

Guarde todas as alterações.

Na janela de terminal do local, teste novamente as alterações no modo de produção.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Lembre-se de que o _config/env/production.js_ foi revertido e o `MONGODB_URI` variável de ambiente só é definida na sua aplicação web do Azure e não no seu computador local. Se observar o ficheiro de configuração, encontrará que será assumida a configuração de produção para utilizar uma base de dados local do MongoDB. Isto certifica-se de que não touch dados de produção quando o testar localmente as alterações de código.

Navegue para `http://localhost:8443` num browser e certifique-se de que tem sessão iniciada.

Selecione **Admin > Gerir artigos**, em seguida, adicione um artigo, selecionando o  **+**  botão.

Pode ver o novo `Comment` agora a caixa de texto.

![Campo de comentário adicionado para artigos](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

No terminal, pare o Node.js, escrevendo `Ctrl+C`.

### <a name="publish-changes-to-azure"></a>Publicar as alterações para Azure

Consolidar as alterações no Git e, em seguida, emita as alterações de código para o Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Uma vez a `git push` é concluída, navegue até à sua aplicação web do Azure e experimentar a nova funcionalidade.

![As alterações de modelo e a base de dados publicados para o Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Se tiver adicionado quaisquer artigos anteriores, ainda pode vê-los. Os dados existentes na sua base de dados do Cosmos não são perdidos. Além disso, as atualizações para o esquema de dados e mantém os dados existentes intactas.

## <a name="manage-your-azure-web-app"></a>Gerir a sua aplicação web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver a aplicação web que criou.

No menu à esquerda, clique em **Serviços Aplicacionais** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Por predefinição, o portal mostra a sua aplicação web **descrição geral** página. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as páginas de configuração diferente, que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Criar uma base de dados de CosmosDB utilizando a API do MongoDB no Azure
> * Ligar uma aplicação Node.js para MongoDB
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

Avançar para o próximo tutorial para saber como mapear um nome DNS personalizado à sua aplicação web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para as Aplicações Web do Azure](../app-service-web-tutorial-custom-domain.md)
