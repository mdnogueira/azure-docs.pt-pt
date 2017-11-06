---
title: "Criar uma aplicação web Docker Python e PostgreSQL no Azure | Microsoft Docs"
description: "Saiba como obter uma aplicação de Docker Python a funcionar no Azure, com uma ligação para uma base de dados PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: fa3aa3a73338970fde2d0b0230e7b2e6ca687dc9
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Criar uma aplicação web Docker Python e PostgreSQL no Azure

Web App para contentores fornece uma serviço de alojamento na web altamente dimensionável e aplicação de patches personalizada. Este tutorial mostra como criar uma aplicação de web básica do Docker Python no Azure. Irá ligar esta aplicação a uma base de dados PostgreSQL. Quando tiver terminado, terá de uma aplicação de Python Flask em execução dentro de um contentor de Docker no [do serviço de aplicações no Linux](app-service-linux-intro.md).

![Docker Python Flask aplicação no App Service no Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Pode seguir os passos abaixo no macOS. Instruções de Linux e Windows são os mesmos na maioria dos casos, mas as diferenças não estão descritas neste tutorial.
 
## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o Python](https://www.python.org/downloads/)
1. [Instalar e executar PostgreSQL](https://www.postgresql.org/download/)
1. [Instalar a edição de Comunidade do Docker](https://www.docker.com/community-edition)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testar a instalação de PostgreSQL local e criar uma base de dados

Abra a janela de terminal e execute `psql postgres` para ligar ao seu servidor PostgreSQL local.

```bash
psql postgres
```

Se a ligação for bem-sucedida, a base de dados PostgreSQL está em execução. Se não, certifique-se de que a base de dados PostgresQL local é iniciada, seguindo os passos indicados em [transfere - PostgreSQL Core distribuição](https://www.postgresql.org/download/).

Criar uma base de dados chamado *eventregistration* e configurar um utilizador de base de dados separada denominado *manager* com palavra-passe *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Tipo *\q* para sair do cliente PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Criar uma aplicação local do Python Flask

Neste passo, configure o projeto de Python Flask local.

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Abra a janela de terminal e `CD` para um diretório de trabalho.

Execute os seguintes comandos para clonar o repositório de exemplo e vá para o *0.1 initialapp* de versão.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Este repositório de exemplo contém um [Flask](http://flask.pocoo.org/) aplicação. 

### <a name="run-the-application"></a>Executar a aplicação

> [!NOTE] 
> Num passo posterior simplifica este processo ao criar um contentor de Docker para utilizar com a base de dados de produção.

Instale os pacotes necessários e inicie a aplicação.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Quando a aplicação é totalmente carregada, verá algo semelhante a mensagem seguinte:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navegue para `http://127.0.0.1:5000` num browser. Clique em **registar!** e criar um utilizador de teste.

![Aplicação de Python Flask executar localmente](./media/tutorial-docker-python-postgresql-app/local-app.png)

A aplicação de exemplo Flask armazena os dados de utilizador na base de dados. Se for concluída com êxito no registo de um utilizador, a aplicação está a escrever dados na base de dados PostgreSQL local.

Para parar o servidor de Flask em qualquer altura, escreva Ctrl + C no terminal. 

## <a name="create-a-production-postgresql-database"></a>Criar uma base de dados de PostgreSQL de produção

Neste passo, vai criar uma base de dados PostgreSQL no Azure. Quando a aplicação é implementada no Azure, irá utilizar esta base de dados de nuvem.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Agora, se pretender utilizar o 2.0 CLI do Azure para criar os recursos necessários para alojar a aplicação de Python na aplicação Web para contentores.  Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md) com o [az group create](/cli/azure/group#create).

[!INCLUDE [Resource group intro](../../../includes/resource-group.md)]

O exemplo seguinte cria um grupo de recursos na região EUA oeste:

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Utilize o [az appservice lista-localizações](/cli/azure/appservice#list-locations) comando da CLI do Azure para localizações disponíveis da lista.

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Criar um servidor de PostgreSQL com o [az postgres servidor criar](/cli/azure/documentdb#create) comando.

O seguinte comando, substitua um nome de servidor exclusivo para o  *\<postgresql_name >* marcador de posição e um utilizador de nome para o  *\<admin_username >* marcador de posição. O nome do servidor é utilizado como parte do seu ponto final PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), por isso, o nome tem de ser exclusivos em todos os servidores no Azure. É o nome de utilizador para a conta de utilizador de administrador de base de dados inicial. Lhe for pedido para escolher uma palavra-passe para este utilizador.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>
```

Quando a base de dados do Azure para o servidor de PostgreSQL é criada, a CLI do Azure mostra as informações semelhante ao seguinte exemplo:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Criar uma regra de firewall para a base de dados do Azure para o servidor de PostgreSQL

Execute o seguinte comando da CLI do Azure, para permitir o acesso à base de dados a partir de todos os endereços IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

A CLI do Azure confirma a criação de regra de firewall com o resultado semelhante ao seguinte exemplo:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Ligar a aplicação do Python Flask a base de dados

Neste passo, ligar a aplicação de exemplo do Python Flask na base de dados do Azure para o servidor de PostgreSQL que criou.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Criar uma base de dados vazio e configurar um novo utilizador de aplicação de base de dados

Crie um utilizador de base de dados com acesso a uma base de dados apenas. Irá utilizar estas credenciais para evitar a fornecer o acesso completo da aplicação para o servidor.

Ligar à base de dados (lhe for pedida a palavra-passe de administrador).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Crie a base de dados e o utilizador a partir da CLI do PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Tipo *\q* para sair do cliente PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Testar a aplicação localmente relativamente a base de dados PostgreSQL do Azure

Retroceder agora para o *aplicação* pasta do repositório Github clonado, é possível executar a aplicação de Python Flask ao atualizar as variáveis de ambiente de base de dados.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Quando a aplicação é totalmente carregada, verá algo semelhante a mensagem seguinte:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navegue para http://127.0.0.1:5000 num browser. Clique em **registar!** e crie um registo de teste. Agora estiver a escrever dados na base de dados no Azure.

![Aplicação de Python Flask executar localmente](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Executar a aplicação a partir de um contentor de Docker

Compilar o Docker imagem do contentor.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker apresentará uma confirmação que tenha criado com êxito o contentor.

```bash
Successfully built 7548f983a36b
```

Adicionar variáveis de ambiente de base de dados para um ficheiro de variável de ambiente *db.env*. A aplicação irá ligar à base de dados de produção PostgreSQL no Azure.

```text
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Execute a aplicação a partir do contentor de Docker. O seguinte comando Especifica o ficheiro de variável de ambiente e a porta de Flask predefinida 5000 é mapeado para a porta local 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

O resultado será semelhante ao que vimos anteriormente. No entanto, a migração de base de dados inicial já não precisa de ser executada e, por conseguinte, é ignorada.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

A base de dados já contém o registo que criou anteriormente.

![Docker baseado no contentor aplicação Python Flask executar localmente](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Carregar o contentor de Docker para um registo de contentor

Neste passo, carregue o contentor de Docker um registo de contentor. Irá utilizar o registo de contentor do Azure, mas também pode utilizar outros diferentes populares, tais como o Hub de Docker.

### <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

No comando seguinte para criar um registo de contentor substituir  *\<registry_name >* com um nome de registo de contentor do Azure exclusivo à sua escolha.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Saída

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Obter as credenciais de registo para enviar e extrair imagens Docker

Para mostrar as credenciais do registo, ative o modo de administração pela primeira vez.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Pode ver duas palavras-passe. Tome nota do nome de utilizador e a palavra-passe primeiro.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Carregue o contentor do Docker no registo de contentor do Azure

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Implementar a aplicação de Docker Python Flask no Azure

Neste passo, implementar a aplicação de Python Flask de baseado no contentor do Docker App Service do Azure.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Crie um plano do Serviço de Aplicações com o comando [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](../../../includes/app-service-plan-linux.md)]

O exemplo seguinte cria um plano de serviço de aplicações baseado em Linux com o nome *myAppServicePlan* de camada utilizando o preço de S1:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando é criado o plano de serviço de aplicações, a CLI do Azure mostra informações semelhante ao seguinte exemplo:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="create-a-web-app"></a>Criar uma aplicação Web

Criar uma aplicação web no *myAppServicePlan* plano de serviço de aplicações com o [az webapp criar](/cli/azure/webapp#create) comando.

A aplicação web fornece um espaço de alojamento para implementar o código e fornece um URL para ver a aplicação implementada. Utilize para criar a aplicação web.

O seguinte comando, substitua o  *\<APP_NAME>.azurewebsites.NET >* marcador de posição com um nome de aplicação único. Este nome é parte do URL predefinido para a aplicação web, para que o nome tem de ser exclusivo em todas as aplicações no App Service do Azure.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Configurar as variáveis de ambiente de base de dados

Anteriormente no tutorial, definiu as variáveis de ambiente para ligar à base de dados PostgreSQL.

No App Service, definir variáveis de ambiente como _as definições de aplicação_ utilizando o [az webapp configuração appsettings conjunto](/cli/azure/webapp/config#set) comando.

O exemplo seguinte especifica os detalhes de ligação de base de dados como as definições de aplicação. Também utiliza o *porta* variável com o mapa de 5000 de porta do contentor de Docker para receber o tráfego HTTP na porta 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configurar a implementação do contentor de Docker

Serviço de aplicações pode transferir e executar um contentor de Docker automaticamente.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Sempre que atualizar o contentor de Docker ou alterar as definições, reinicie a aplicação. Reiniciar assegura que todas as definições são aplicadas e o contentor mais recente é solicitado a partir do registo.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Navegue para a aplicação web do Azure 

Navegue para a aplicação web implementada utilizando o seu browser. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> A aplicação web demora mais carregar porque o contentor tem de ser transferidos e iniciado depois da configuração do contentor é alterada.

Consulte anteriormente registados convidados que foram guardados na base de dados de produção do Azure no passo anterior.

![Docker baseado no contentor aplicação Python Flask executar localmente](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Parabéns!** Está a executar um Docker aplicação de Python Flask baseada no contentor no App Service do Azure.

## <a name="update-data-model-and-redeploy"></a>Modelo de dados de atualização e volte a implementar

Neste passo, adicione o número de reunião para cada registo de eventos por atualizar o modelo de convidado.

Veja o *0,2 migração* de versão com o seguinte comando do git:

```bash
git checkout tags/0.2-migration
```

Esta versão já efectuadas as alterações necessárias para vistas, controladores e modelo. Também inclui uma migração de base de dados gerada através de *alembic* (`flask db migrate`). Pode ver todas as alterações efetuadas através do seguinte comando do git:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testar as suas alterações localmente

Execute os seguintes comandos para testar as suas alterações localmente ao executar o servidor de flask.

Mac / Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Navegue para http://127.0.0.1:5000 no seu browser para ver as alterações. Crie um registo de teste.

![Docker baseado no contentor aplicação Python Flask executar localmente](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicar as alterações para Azure

Criar a nova imagem de docker, enviá-lo para o registo de contentor e reinicie a aplicação.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Navegue até à sua aplicação web do Azure e experimentar a nova funcionalidade novamente. Crie outro registo de eventos.

```bash 
http://<app_name>.azurewebsites.net 
```

![Docker Python Flask aplicação no App Service do Azure](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gerir a sua aplicação web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver a aplicação web que criou.

No menu à esquerda, clique em **Serviços Aplicacionais** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Por predefinição, o portal mostra a sua aplicação web **descrição geral** página. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as páginas de configuração diferente, que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo tutorial para saber como mapear um nome DNS personalizado à sua aplicação web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para as Aplicações Web do Azure](../app-service-web-tutorial-custom-domain.md)
