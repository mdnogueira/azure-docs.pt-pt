---
title: "Criar uma aplicação web .NET Core e a SQL Database no App Service do Azure no Linux | Microsoft Docs"
description: "Saiba como obter uma aplicação .NET Core trabalhar no serviço de aplicações do Azure no Linux, com ligação para uma base de dados do SQL Server."
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
ms.openlocfilehash: ef68f64437935f08f76c29ecf15d574279cca7f1
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-app-service-on-linux"></a>Criar uma aplicação web .NET Core e a SQL Database no App Service do Azure no Linux

[Serviço de aplicações no Linux](app-service-linux-intro.md) fornece uma serviço utilizando o sistema operativo Linux de alojamento na web altamente dimensionável e aplicação de patches personalizada. Este tutorial mostra como criar uma aplicação web do .NET Core e ligue-o a uma base de dados do SQL Server. Quando tiver terminado, terá uma aplicação de MVC do .NET Core em execução no App Service no Linux.

![aplicação em execução no App Service no Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Pode saber como:

> [!div class="checklist"]
> * Criar uma base de dados do SQL Server no Azure
> * Ligar um .NET Core aplicação à base de dados do SQL Server
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Registos de diagnóstico de fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instale o Git](https://git-scm.com/).
1. [Instalar o .NET Core SDK 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-local-net-core-app"></a>Criar aplicações de .NET Core local

Neste passo, configure o projeto de .NET Core local.

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Na janela de terminal, `cd` para um diretório de trabalho.

Execute os seguintes comandos para clonar o repositório de exemplo e alterar a raiz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

O projeto de exemplo contém um através de aplicações CRUD (criar-leitura-atualizar-eliminar) básico [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Executar a aplicação

Execute os seguintes comandos para instalar os pacotes necessários, execute migrações de base de dados e iniciar a aplicação.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Navegue para `http://localhost:5000` num browser. Selecione o **criar novo** ligar e criar alguns _tarefas_ itens.

![estabelece ligação com êxito à base de dados SQL](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Para parar .NET Core em qualquer altura, prima `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Criar base de dados do SQL Server de produção

Neste passo, vai criar uma base de dados do SQL Server no Azure. Quando a aplicação for implementada para o Azure, este utiliza esta base de dados de nuvem.

Para a base de dados do SQL Server, este tutorial utiliza [SQL Database do Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico de base de dados SQL

A Shell de nuvem, criar um servidor lógico de base de dados SQL com o [do SQL Server de az criar](/cli/azure/sql/server#create) comando.

Substitua o  *\<server_name >* marcador de posição com um nome exclusivo da base de dados SQL. Este nome é utilizado como parte do ponto final da base de dados do SQL Server, `<server_name>.database.windows.net`, por isso, o nome tem de ser exclusivos em todos os servidores lógicos no Azure. O nome tem de conter apenas letras minúsculas, números e o caráter de hífen (-) e tem de ter entre 3 e 50 carateres de comprimento. Substitua, também,  *\<db_username >* e  *\<db_password >* com um nome de utilizador e palavra-passe à sua escolha. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Quando o servidor lógico de base de dados SQL é criado, a CLI do Azure mostra as informações semelhante ao seguinte exemplo:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Crie uma [regra de firewall ao nível do servidor da Base de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) com o comando [az sql server firewall create](/cli/azure/sql/server#create). Quando o IP inicial e final IP estão definidas como 0.0.0.0, a firewall apenas é aberta para outros recursos do Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Criar uma base de dados

Crie uma base de dados com um [nível de desempenho S0](../../sql-database/sql-database-service-tiers.md) no servidor com o comando [az sql db create](/cli/azure/sql/db#create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Criar cadeia de ligação

Substitua a seguinte cadeia com o  *\<server_name >*,  *\<db_username >*, e  *\<db_password >* utilizada anteriormente.

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Esta é a cadeia de ligação para a sua aplicação .NET Core. Copie-o para utilização posterior.

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Neste passo, implementar a aplicação .NET Core ligada à base de dados SQL do serviço de aplicações no Linux.

### <a name="configure-local-git-deployment"></a>Configurar a implementação do git local

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurar uma variável de ambiente

Para definir cadeias de ligação para a sua aplicação do Azure, utilize o [atualização az webapp configuração appsettings](/cli/azure/webapp/config/appsettings#update) comando na Shell de nuvem. O seguinte comando, substitua  *\<nome da aplicação >*, bem como o  *\<connection_string >* parâmetro com a cadeia de ligação que criou anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Em seguida, configure `ASPNETCORE_ENVIRONMENT` definição de aplicação para _produção_. Esta definição permite-lhe saber se estiver a executar no Azure, uma vez que utilizam SQLLite para o seu ambiente de desenvolvimento local e a base de dados do SQL Server para o seu ambiente do Azure.

O exemplo a seguir configura um `ASPNETCORE_ENVIRONMENT` definição de aplicação na sua aplicação web do Azure. Substitua o  *\<APP_NAME>.azurewebsites.NET >* marcador de posição.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Ligar à base de dados do SQL Server na produção

No seu repositório local, abra Startup.cs e localizar o seguinte código:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Substitua-o com o código seguinte, que utiliza as variáveis de ambiente que configurou anteriormente.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<DotNetCoreSqlDbContext>().Database.Migrate();
```

Se este código Deteta que está a ser executado na produção (o que indica o ambiente do Azure), em seguida, utiliza a cadeia de ligação configurado para ligar à base de dados do SQL Server.

O `Database.Migrate()` chamada ajuda-o quando for executada no Azure, porque este cria automaticamente as bases de dados que as necessidades de aplicações .NET Core, com base na respetiva configuração de migração. 

Guarde as alterações.

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Navegue para a aplicação web do Azure

Navegue para a aplicação web implementada utilizando o seu browser.

```bash
http://<app_name>.azurewebsites.net
```

Adicione alguns itens de tarefas.

![aplicação em execução no App Service no Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Parabéns!** Estiver a executar uma aplicação de .NET Core condicionada por dados no serviço de aplicações no Linux.

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e volte a implementar

Neste passo, altere o esquema de base de dados e publicá-lo no Azure.

### <a name="update-your-data-model"></a>Atualizar o modelo de dados

Abra _Models\Todo.cs_ no editor de código. Adicione a seguinte propriedade para o `ToDo` classe:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Execute migrações primeiro código localmente

Execute alguns comandos para disponibilizar as atualizações à base de dados local.

```bash
dotnet ef migrations add AddProperty
```

Atualize a base de dados local:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Utilize a nova propriedade

Efetuar algumas alterações no seu código para utilizar o `Done` propriedade. De simplicidade neste tutorial, que só vai alterar o `Index` e `Create` vistas para ver a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localizar o `Create()` método e adicione `Done` à lista de propriedades no `Bind` atributo. Quando tiver terminado, a `Create()` assinatura do método aparente ser o seguinte código:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

No código Razor, deverá ver uma `<div class="form-group">` elemento para `Description`e, em seguida, outra `<div class="form-group">` elemento para `CreatedDate`. Imediatamente a seguir estes dois elementos, adicione outro `<div class="form-group">` elemento para `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Abra _Views\Todos\Index.cshtml_.

Procure vazia `<th></th>` elemento. Apenas acima este elemento, adicione o seguinte código de Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Localizar o `<td>` elemento que contém o `asp-action` tag programas de ajuda. Apenas acima este elemento, adicione o seguinte código de Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

É tudo o que precisa para ver as alterações no `Index` e `Create` vistas.

### <a name="test-your-changes-locally"></a>Testar as suas alterações localmente

Execute a aplicação localmente.

```bash
dotnet run
```

No seu browser, navegue para `http://localhost:5000/`. Agora pode adicionar um item de tarefas e verifique **feito**. Em seguida, deve agora mostrar cópias de segurança na sua home page como um item concluído. Lembre-se de que o `Edit` vista não apresentar o `Done` campo, porque não foi alterado o `Edit` vista.

### <a name="publish-changes-to-azure"></a>Publicar as alterações para Azure

```bash

git commit -am "added done field"
git push azure master
```

Uma vez a `git push` é concluída, navegue até à sua aplicação web do Azure e experimentar a nova funcionalidade.

![Aplicação web do Azure após a migração primeiro código](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Todos os seus itens de tarefas existentes ainda são apresentados. Quando voltar a publicar a aplicação .NET Core, os dados existentes na sua base de dados do SQL Server não são perdidos. Além disso, as migrações do Entity Framework Core apenas altera o esquema de dados e mantém os dados existentes intactas.

## <a name="manage-your-azure-web-app"></a>Gerir a sua aplicação web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver a aplicação web que criou.

No menu à esquerda, clique em **Serviços Aplicacionais** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Por predefinição, o portal mostra a sua aplicação web **descrição geral** página. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as páginas de configuração diferente, que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Criar uma base de dados do SQL Server no Azure
> * Ligar um .NET Core aplicação à base de dados do SQL Server
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

Avançar para o próximo tutorial para saber como mapear um nome DNS personalizado à sua aplicação web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para as Aplicações Web do Azure](../app-service-web-tutorial-custom-domain.md)