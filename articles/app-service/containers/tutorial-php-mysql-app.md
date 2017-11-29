---
title: "Criar uma aplicação web PHP e o MySQL no Azure | Microsoft Docs"
description: "Saiba como obter uma aplicação PHP a funcionar no Azure, com uma ligação à base de dados MySQL no Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 86ee5b02fe2a9f34db651f6446398d366b24b5d2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Criar uma aplicação web PHP e o MySQL no Azure

[Serviço de aplicações no Linux](app-service-linux-intro.md) fornece uma serviço utilizando o sistema operativo Linux de alojamento na web altamente dimensionável e aplicação de patches personalizada. Este tutorial mostra como criar uma aplicação web PHP e ligue-o a uma base de dados MySQL. Quando tiver terminado, terá um [Laravel](https://laravel.com/) aplicação em execução no serviço de aplicações no Linux.

![Aplicação PHP em execução no App Service do Azure](./media/tutorial-php-mysql-app/complete-checkbox-published.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados MySQL no Azure
> * Ligar uma aplicação PHP para MySQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Registos de diagnóstico de fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instale o Git](https://git-scm.com/).
* [Instalar o PHP 5.6.4 ou superior](http://php.net/downloads.php)
* [Instalar compositor](https://getcomposer.org/doc/00-intro.md)
* Ativar as seguintes extensões PHP Laravel necessidades: OpenSSL PDO MySQL, Mbstring, atomizador, XML
* [Instalar e iniciar o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparar o local MySQL

Neste passo, vai criar uma base de dados no seu servidor MySQL local para a utilização deste tutorial.

### <a name="connect-to-local-mysql-server"></a>Ligar ao servidor local do MySQL

Numa janela de terminal, ligue ao servidor local MySQL. Pode utilizar esta janela de terminal para executar todos os comandos neste tutorial.

```bash
mysql -u root -p
```

Se lhe for pedida uma palavra-passe, introduza a palavra-passe para o `root` conta. Se não se lembra da sua palavra-passe da conta de raiz, consulte o artigo [MySQL: como repor a palavra-passe de raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se o comando for executado com êxito, em seguida, o servidor de MySQL está em execução. Se não, certifique-se de que o servidor de MySQL local é iniciado, seguindo o [passos de pós-instalação MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Criar uma base de dados localmente

Na `mysql` solicitar, crie uma base de dados.

```sql 
CREATE DATABASE sampledb;
```

Sair da sua ligação ao servidor introduzindo `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Criar uma aplicação PHP localmente
Neste passo, obter um exemplo de aplicação Laravel, configurar a ligação de base de dados e executá-la localmente. 

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela de terminal, `cd` para um diretório de trabalho.

Execute o seguinte comando para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd`para o seu diretório clonado.
Instale os pacotes necessários.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configurar a ligação de MySQL

Na raiz do repositório, crie um ficheiro denominado *.env*. Copie as seguintes variáveis para o *.env* ficheiro. Substitua o  _&lt;root_password >_ marcador de posição pela palavra-passe do utilizador de raiz MySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Para obter informações sobre como Laravel utiliza o _.env_ de ficheiros, consulte [Laravel ambiente configuração](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>Executar o exemplo localmente

Executar [migrações de base de dados de Laravel](https://laravel.com/docs/5.4/migrations) tem de criar as tabelas a aplicação. Para ver as tabelas são criadas nas migrações, consulte o _da base de dados/migrações_ diretório no repositório de Git.

```bash
php artisan migrate
```

Gere uma nova chave de aplicação de Laravel.

```bash
php artisan key:generate
```

Execute a aplicação.

```bash
php artisan serve
```

Navegue para `http://localhost:8000` num browser. Adicione algumas tarefas na página.

![PHP liga-se com êxito ao MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Para parar o PHP, escreva `Ctrl + C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Criar o MySQL no Azure

Neste passo, vai criar uma base de dados MySQL no [MySQL (pré-visualização) na base de dados Azure](/azure/mysql). Posteriormente, deve configurar a aplicação PHP para ligar a esta base de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Criar um servidor de MySQL

Criar um servidor na base de dados do Azure para MySQL (pré-visualização) com o [az mysql servidor criar](/cli/azure/mysql/server#create) comando.

O seguinte comando, substitua o nome do servidor MySQL onde vir o  _&lt;mysql_server_name >_ marcador de posição (carateres válidos são `a-z`, `0-9`, e `-`). Este nome é parte do nome de anfitrião do servidor MySQL (`<mysql_server_name>.database.windows.net`), tem de ser globalmente exclusivo.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password MySQLAzure2017 --ssl-enforcement Disabled
```

Quando o servidor de MySQL é criado, a CLI do Azure mostra as informações semelhante ao seguinte exemplo:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurar a firewall do servidor

Criar uma regra de firewall para o servidor de MySQL permitir ligações de cliente utilizando o [az mysql servidor-regra de firewall criar](/cli/azure/mysql/server/firewall-rule#create) comando.

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Base de dados do Azure para MySQL (pré-visualização) atualmente não limita ligações apenas para os serviços do Azure. Como dinamicamente forem atribuídos a endereços IP no Azure, é melhor ativar todos os endereços IP. O serviço está em pré-visualização. Melhor métodos para proteger a sua base de dados estão a ser planeados.
>

### <a name="connect-to-production-mysql-server-locally"></a>Ligar ao servidor de MySQL de produção localmente

Na janela de terminal, ligar ao servidor MySQL no Azure. Utilize o valor que especificou anteriormente para  _&lt;mysql_server_name >_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

Quando lhe for pedido para uma palavra-passe, utilize _$tr0ngPa$ w0rd!_, que especificou quando criou a base de dados.

### <a name="create-a-production-database"></a>Criar uma base de dados de produção

Na `mysql` solicitar, crie uma base de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um utilizador com permissões

Criar um utilizador de base de dados chamado _phpappuser_ e conceda-lhe todos os privilégios `sampledb` base de dados.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Sair a ligação ao servidor introduzindo `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Ligar a aplicação para MySQL do Azure

Neste passo, ligar a aplicação PHP para a base de dados MySQL que criou na base de dados do Azure para MySQL (pré-visualização).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a ligação de base de dados

Na raiz do repositório, crie um _. env.production_ de ficheiros e copie as seguintes variáveis para a mesma. Substitua o marcador de posição  _&lt;mysql_server_name >_.

```txt
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
```
<!-- MYSQL_SSL=true -->

Guarde as alterações.

> [!TIP]
> Para proteger as suas informações de ligação do MySQL, este ficheiro já foi excluído do repositório de Git (consulte _. gitignore_ na raiz do repositório). Mais tarde, irá aprender a configurar variáveis de ambiente no App Service para ligar à base de dados na base de dados do Azure para MySQL (pré-visualização). Com variáveis de ambiente, não precisa do *.env* ficheiro no App Service.
>

<!-- ### Configure SSL certificate

By default, Azure Database for MySQL enforces SSL connections from clients. To connect to your MySQL database in Azure, you must use a _.pem_ SSL certificate.

Open _config/database.php_ and add the _sslmode_ and _options_ parameters to `connections.mysql`, as shown in the following code.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/certificate.pem', 
    ] : []
],
```

To learn how to generate this _certificate.pem_, see [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](../../mysql/howto-configure-ssl.md).

> [!TIP]
> The path _/ssl/certificate.pem_ points to an existing _certificate.pem_ file in the Git repository. This file is provided for convenience in this tutorial. For best practice, you should not commit your _.pem_ certificates into source control. 
> -->

### <a name="test-the-application-locally"></a>Testar a aplicação localmente

Execute migrações de base de dados de Laravel com _. env.production_ como o ficheiro de ambiente para criar as tabelas na base de dados MySQL na base de dados do Azure para MySQL (pré-visualização). Lembre-se de que _. env.production_ tem as informações de ligação à base de dados MySQL no Azure.

```bash
php artisan migrate --env=production --force
```

_. env.production_ ainda não tem uma chave de aplicação válida. Gere um novo para o mesmo no terminal.

```bash
php artisan key:generate --env=production --force
```

Execute a aplicação de exemplo com _. env.production_ como o ficheiro de ambiente.

```bash
php artisan serve --env=production
```

Navegue para `http://localhost:8000`. Se a página for carregada sem erros, a aplicação PHP está a ligar à base de dados MySQL no Azure.

Adicione algumas tarefas na página.

![PHP estabelece ligação com êxito à base de dados do Azure para MySQL (pré-visualização)](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Para parar o PHP, escreva `Ctrl + C` no terminal.

### <a name="commit-your-changes"></a>Consolidar as alterações

Execute os seguintes comandos de Git para consolidar as alterações:

```bash
git add .
git commit -m "database.php updates"
```

A aplicação está pronta para ser implementada.

## <a name="deploy-to-azure"></a>Implementar no Azure

Neste passo, a aplicação é implementada ligados MySQL PHP App Service do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Configurar definições de base de dados

No App Service, definir variáveis de ambiente como _as definições de aplicação_ utilizando o [az webapp configuração appsettings conjunto](/cli/azure/webapp/config/appsettings#set) comando.

O seguinte comando configura as definições de aplicação `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, e `DB_PASSWORD`. Substitua os marcadores de posição  _&lt;appname >_ e  _&lt;mysql_server_name >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```
 <!-- MYSQL_SSL="true" -->

Pode utilizar o PHP [getenv](http://www.php.net/manual/function.getenv.php) método para aceder às definições. o código de Laravel utiliza um [env](https://laravel.com/docs/5.4/helpers#method-env) wrapper através do PHP `getenv`. Por exemplo, a configuração de MySQL no _config/database.php_ parece ser o seguinte código:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configurar variáveis de ambiente de Laravel

Laravel tem uma chave de aplicação no App Service. Pode configurá-lo com as definições de aplicação.

Utilize `php artisan` para gerar uma nova chave de aplicação sem a guardar a _.env_.

```bash
php artisan key:generate --show
```

Defina a chave de aplicação no App Service aplicação web utilizando o [az webapp configuração appsettings conjunto](/cli/azure/webapp/config/appsettings#set) comando. Substitua os marcadores de posição  _&lt;appname >_ e  _&lt;outputofphpartisankey: gerar >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"`indica Laravel para devolver informações de depuração quando a aplicação web implementada encontra erros. Quando executar uma aplicação de produção, defina-o como `false`, que é mais segura.

### <a name="set-the-virtual-application-path"></a>Definir o caminho de aplicação virtual

Defina o caminho de aplicação virtual da aplicação web. Este passo é necessário porque o [ciclo de vida de aplicação de Laravel](https://laravel.com/docs/5.4/lifecycle) começa no _pública_ diretório em vez de diretório de raiz da aplicação. Outras estruturas PHP cujo ciclo de vida iniciar no diretório de raiz pode trabalhar sem configuração manual do caminho da aplicação virtual.

Definir o caminho de aplicação virtual utilizando o [atualização de recurso az](/cli/azure/resource#update) comando. Substitua o  _&lt;appname >_ marcador de posição.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Por predefinição, o App Service do Azure pontos do caminho de aplicação virtual de raiz (_/_) para o diretório de raiz dos ficheiros de aplicação implementado (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

Adicione um Azure remoto para o seu repositório do Git local.

```bash
git remote add azure <paste_copied_url_here>
```

Push para o Azure remoto para implementar a aplicação PHP. É-lhe pedida a palavra-passe que forneceu anteriormente como parte da criação do utilizador de implementação.

```bash
git push azure master
```

Durante a implementação, o App Service do Azure comunica o progresso da mesma com o Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> Poderá reparar que o processo de implementação instala [compositor](https://getcomposer.org/) pacotes no final. Serviço de aplicações não execute estes automatizações durante a implementação predefinida, para que este repositório de exemplo tem três ficheiros adicionais no respetivo diretório raiz para ativá-la:
>
> - `.deployment`-Este ficheiro indica ao serviço de aplicações para executar `bash deploy.sh` como o script de implementação personalizada.
> - `deploy.sh`-O script de implementação personalizada. Se analisar o ficheiro, verá que é executada `php composer.phar install` depois `npm install`.
> - `composer.phar`-O Gestor de pacotes do compositor.
>
> Pode utilizar esta abordagem para adicionar qualquer passo para a sua implementação baseada no Git no App Service. Para obter mais informações, consulte [Script de implementação personalizada](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-web-app"></a>Navegue para a aplicação web do Azure

Navegue até à `http://<app_name>.azurewebsites.net` e adicionar algumas tarefas à lista.

![Aplicação PHP em execução no App Service do Azure](./media/tutorial-php-mysql-app/php-mysql-in-azure.png)

Parabéns, que está a executar uma aplicação PHP condicionada por dados no App Service do Azure.

## <a name="update-model-locally-and-redeploy"></a>Atualizar o modelo localmente e volte a implementar

Neste passo, efetuar uma alteração simples para o `task` modelo de dados e a webapp e, em seguida, publicar a atualização para o Azure.

Para o cenário de tarefas, modificar a aplicação de modo a que pode marcar uma tarefa como concluídos.

### <a name="add-a-column"></a>Adicionar uma coluna

No terminal, navegue para a raiz do repositório de Git.

Gerar uma nova migração de base de dados para o `tasks` tabela:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Este comando apresenta o nome do ficheiro de migração que é gerado. Localizar este ficheiro na _da base de dados/migrações_ e abri-lo.

Substitua o `up` método com o seguinte código:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

O código anterior adiciona uma coluna booleana no `tasks` tabela denominada `complete`.

Substitua o `down` método com o seguinte código para a ação de reversão:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

No terminal, execute migrações de base de dados de Laravel para efetuar a alteração na base de dados local.

```bash
php artisan migrate
```

Com base no [Convenção de nomenclatura Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), o modelo `Task` (consulte _app/Task.php_) mapeia para o `tasks` tabela por predefinição.

### <a name="update-application-logic"></a>Lógica da aplicação de atualização

Abra o *routes/web.php* ficheiro. A aplicação define a respetiva rotas e lógica de negócio aqui.

No final do ficheiro, adicione uma rota com o seguinte código:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

O código anterior faz com que uma atualização simple para o modelo de dados por ativando ou desativando o valor de `complete`.

### <a name="update-the-view"></a>Atualize a vista

Abra o *resources/views/tasks.blade.php* ficheiro. Localizar o `<tr>` tag de início e substitua-o com:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

O código anterior altera a cor da linha, dependendo da tarefa é concluída.

Na linha seguinte, terá o seguinte código:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Substitua a toda a linha com o seguinte código:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

O código anterior adiciona o botão para submeter que faça referência a rota definida anteriormente.

### <a name="test-the-changes-locally"></a>Testar as alterações localmente

A partir do diretório raiz do repositório de Git, execute o servidor de desenvolvimento.

```bash
php artisan serve
```

Para ver o estado da tarefa alterar, navegue para `http://localhost:8000` e selecione a caixa de verificação.

![Caixa de verificação foi adicionada à tarefa](./media/tutorial-php-mysql-app/complete-checkbox.png)

Para parar o PHP, escreva `Ctrl + C` no terminal.

### <a name="publish-changes-to-azure"></a>Publicar as alterações para Azure

No terminal, execute Laravel migrações de base de dados com a cadeia de ligação de produção para efetuar a alteração na base de dados do Azure.

```bash
php artisan migrate --env=production --force
```

Consolidar todas as alterações no Git e, em seguida, emita as alterações de código para o Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Uma vez a `git push` está concluída, navegue para a aplicação web do Azure e testar a nova funcionalidade.

![As alterações de modelo e a base de dados publicados para o Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Se tiver adicionado quaisquer tarefas, estes são retidos na base de dados. Atualizações para o esquema de dados, deixe dados existentes intactas.

## <a name="manage-the-azure-web-app"></a>Gerir a aplicação Web do Azure

Aceda ao [portal do Azure](https://portal.azure.com) para gerir a aplicação Web que criou.

No menu à esquerda, clique em **Serviços de Aplicações** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/tutorial-php-mysql-app/access-portal.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode efetuar tarefas de gestão básicas, como parar, iniciar, reiniciar, procurar e eliminar.

O menu à esquerda fornece páginas para configurar a sua aplicação.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma base de dados MySQL no Azure
> * Ligar uma aplicação PHP para MySQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Registos de diagnóstico de fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

Avançar para o próximo tutorial para saber como mapear um nome DNS personalizado para uma aplicação web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para as Aplicações Web do Azure](../app-service-web-tutorial-custom-domain.md)
