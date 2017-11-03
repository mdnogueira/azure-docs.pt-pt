---
title: "Criar uma aplicação ASP.NET no Azure com a base de dados SQL | Microsoft Docs"
description: "Saiba como obter uma aplicação ASP.NET a funcionar no Azure, com uma ligação para uma base de dados do SQL Server."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/09/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: db3be8068ef9e560614daa0e7f0dcf62467fd338
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="build-an-aspnet-app-in-azure-with-sql-database"></a>Criar uma aplicação ASP.NET no Azure com a base de dados SQL

[As Aplicações Web do Azure](app-service-web-overview.md) fornecem um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como implementar uma aplicação de web ASP.NET condicionada por dados no Azure e ligá-lo para [SQL Database do Azure](../sql-database/sql-database-technical-overview.md). Quando tiver terminado, que tem uma aplicação ASP.NET em execução no Azure e ligado à SQL Database.

![Aplicação de ASP.NET publicada na aplicação web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados do SQL Server no Azure
> * Ligar uma aplicação ASP.NET para a base de dados SQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
  - **Desenvolvimento do ASP.NET e Web**
  - **Desenvolvimento do Azure**

  ![Desenvolvimento do ASP.NET e Web e desenvolvimento do Azure (na Web e na nuvem)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

[Transferir o projeto de exemplo](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

Extrair (deszipe) a *dotnet-sqldb-tutorial-master.zip* ficheiro.

O projeto de exemplo contém um basic [ASP.NET MVC](https://www.asp.net/mvc) CRUD (criar-leitura-atualizar-eliminar) utilizando a aplicação [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Executar a aplicação

Abra o *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* ficheiro no Visual Studio. 

Tipo `Ctrl+F5` para executar a aplicação sem a depuração. A aplicação é apresentada no browser predefinido. Selecione o **criar novo** ligar e criar alguns *tarefas* itens. 

![Caixa de diálogo Novo Projeto ASP.NET](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Teste o **editar**, **detalhes**, e **eliminar** ligações.

A aplicação utiliza um contexto de base de dados para estabelecer ligação com a base de dados. Neste exemplo, o contexto de base de dados utiliza uma cadeia de ligação com o nome `MyDbConnection`. A cadeia de ligação é definida *Web. config* de ficheiros e referenciado no *Models/MyDatabaseContext.cs* ficheiro. O nome da cadeia de ligação é utilizado mais tarde no tutorial para ligar a aplicação web do Azure para uma base de dados do SQL do Azure. 

## <a name="publish-to-azure-with-sql-database"></a>Publicar no Azure com a base de dados SQL

No **Explorador de soluções**, clique com botão direito do **DotNetAppSqlDb** projeto e selecione **publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Certifique-se de que o **Serviço de Aplicações do Microsoft Azure** está selecionado e clique em **Publicar**.

![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Abre-se a publicação de **criar App Service** caixa de diálogo, que ajuda a criar todos os recursos do Azure tem de executar a aplicação web do ASP.NET no Azure.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na caixa de diálogo **Criar Serviço de Aplicações**, clique em **Adicionar uma conta** e inicie sessão na sua subscrição do Azure. Se já tiver sessão iniciada numa conta Microsoft, confirme que esta inclui a sua subscrição do Azure. Se a subscrição do Azure não estiver na conta Microsoft em que tem sessão iniciada, clique nesta para adicionar a conta correta.
   
![Iniciar sessão no Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

Depois de iniciar sessão, está pronto para criar todos os recursos de que precisa para a sua aplicação Web do Azure nesta caixa de diálogo.

### <a name="configure-the-web-app-name"></a>Configurar o nome da aplicação web

Pode manter o nome da aplicação web gerado ou alterá-la para outro nome exclusivo (carateres válidos são `a-z`, `0-9`, e `-`). O nome da aplicação web é utilizado como parte do URL predefinido para a sua aplicação (`<app_name>.azurewebsites.net`, onde `<app_name>` é o nome da aplicação web). O nome da aplicação web tem de ser exclusivo em todas as aplicações no Azure. 

![Criar caixa de diálogo do app service](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

> [!NOTE]
> Não clique em **criar**. Terá primeiro de configurar uma base de dados do SQL Server num passo posterior.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Junto a **Grupo de recursos**, clique em **Novo**.

![Junto ao grupo de recursos, clique em novo.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

O grupo de recursos de nome **myResourceGroup**.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Junto a **Plano do Serviço de Aplicações**, clique em **Novo**. 

Na caixa de diálogo **Configurar o Plano do Serviço de Aplicações**, configure o plano novo com as definições seguintes:

![Criar plano do App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Definição  | Valor sugerido | Para obter mais informações |
| ----------------- | ------------ | ----|
|**Plano do App Service**| myAppServicePlan | [Planos do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |
|**Localização**| Europa Ocidental | [Regiões do Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
|**Tamanho**| Gratuito | [Escalões de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-sql-server-instance"></a>Criar uma instância do SQL Server

Antes de criar uma base de dados, é necessário um [servidor lógico da SQL Database do Azure](../sql-database/sql-database-features.md). Um servidor lógico contém um grupo de bases de dados geridas como um grupo.

Selecione **explorar serviços Azure adicionais**.

![Configurar o nome da aplicação Web](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

No **serviços** separador, clique em de  **+**  ícone junto a **base de dados SQL**. 

![No separador de serviços, clique em de + ícone junto a base de dados SQL.](media/app-service-web-tutorial-dotnet-sqldatabase/sql.png)

No **configurar a base de dados do SQL** caixa de diálogo, clique em **novo** junto a **do SQL Server**. 

É gerado um nome de servidor único. Este nome é utilizado como parte do URL predefinido para o servidor lógico, `<server_name>.database.windows.net`. Tem de ser exclusivo em todas as instâncias de servidor lógico no Azure. Pode alterar o nome do servidor, mas para este tutorial, mantenha o valor gerado.

Adicione um nome de utilizador administrador e a palavra-passe. Para requisitos de complexidade de palavra-passe, consulte [política de palavra-passe](/sql/relational-databases/security/password-policy).

Lembre-se de que este nome de utilizador e palavra-passe. É necessário para gerir a instância de servidor lógico mais tarde.

![Criar a instância do SQL Server](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

Clique em **OK**. Não feche o **configurar a base de dados do SQL** ainda a caixa de diálogo.

### <a name="create-a-sql-database"></a>Criar uma Base de Dados SQL

No **configurar a base de dados do SQL** diálogo: 

* Mantenha a predefinição gerada **nome de base de dados**.
* No **nome da cadeia de ligação**, tipo *MyDbConnection*. Este nome tem de coincidir com a cadeia de ligação é referenciada em *Models/MyDatabaseContext.cs*.
* Selecione **OK**.

![Configurar a base de dados SQL](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

O **criar App Service** caixa de diálogo mostra os recursos que criou. Clique em **Criar**. 

![os recursos que criou](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

Depois de concluído o assistente criar os recursos do Azure, publica a aplicação ASP.NET para o Azure. O browser predefinido é iniciado com o URL para a aplicação implementada. 

Adicione alguns itens de tarefas.

![Aplicação de ASP.NET publicada na aplicação web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Parabéns! A aplicação de ASP.NET condicionada por dados está em execução no App Service do Azure.

## <a name="access-the-sql-database-locally"></a>Acesso a base de dados do SQL localmente

Visual Studio permite-lhe explorar e gerir a sua nova base de dados SQL em facilmente o **SQL Server Object Explorer**.

### <a name="create-a-database-connection"></a>Criar uma ligação de base de dados

Do **vista** menu, selecione **SQL Server Object Explorer**.

Na parte superior do **SQL Server Object Explorer**, clique em de **adicionar SQL Server** botão.

### <a name="configure-the-database-connection"></a>Configurar a ligação de base de dados

No **Connect** caixa de diálogo, expanda o **Azure** nós. Todas as instâncias de base de dados SQL no Azure estão listadas aqui.

Selecione a base de dados do SQL Server que criou anteriormente. A ligação que criou anteriormente é preenchida automaticamente na parte inferior.

Escreva a palavra-passe da administrador de base de dados que criou anteriormente e clique em **Connect**.

![Configure a ligação de base de dados do Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Permitir a ligação de cliente do computador

O **criar uma nova regra de firewall** abrir a caixa de diálogo. Por predefinição, a instância de base de dados SQL só permite ligações a partir de serviços do Azure, tais como a sua aplicação web do Azure. Para ligar à base de dados, crie uma regra de firewall na instância da base de dados SQL. A regra de firewall que permite que o endereço IP público do seu computador local.

A caixa de diálogo já é preenchida com endereço IP público do seu computador.

Certifique-se de que **Adicionar IP do cliente** está selecionada e clique em **OK**. 

![Configurar a firewall para a instância de base de dados SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Depois do Visual Studio acaba de criar a definição de firewall para a instância de base de dados do SQL Server, a ligação que aparecem no **SQL Server Object Explorer**.

Aqui, pode realizar as mais comuns operações de base de dados, tais como executadas consultas, criar vistas e procedimentos armazenados e muito mais. 

Expanda a sua ligação de > **bases de dados** > **&lt;a base de dados >** > **tabelas**. Clique com o botão direito no `Todoes` tabela e selecione **ver dados**. 

![Explorar os objetos de base de dados SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Atualizar a aplicação com código de migrações primeiro

Pode utilizar as ferramentas familiares no Visual Studio para atualizar a base de dados e a aplicação web no Azure. Neste passo, utiliza migrações primeiro código em Entity Framework para efetuar uma alteração do esquema de base de dados e publicá-lo no Azure.

Para obter mais informações sobre como utilizar o Entity Framework Code primeiro migrações, consulte [introdução Entity Framework 6 Code First utilizando MVC 5](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="update-your-data-model"></a>Atualizar o modelo de dados

Abra _Models\Todo.cs_ no editor de código. Adicione a seguinte propriedade para o `ToDo` classe:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Execute migrações primeiro código localmente

Execute alguns comandos para disponibilizar as atualizações à base de dados local. 

Do **ferramentas** menu, clique em **Gestor de pacotes NuGet** > **consola do Gestor de pacotes**.

Na janela da consola do Gestor de pacotes, ative migrações primeiro código:

```PowerShell
Enable-Migrations
```

Adicione uma migração:

```PowerShell
Add-Migration AddProperty
```

Atualize a base de dados local:

```PowerShell
Update-Database
```

Tipo `Ctrl+F5` para executar a aplicação. Testar a edição, mais detalhes e criar ligações.

Se a aplicação carrega sem erros, em seguida, migrações primeiro código foi bem sucedida. No entanto, a página ainda procura o mesmo porque a lógica de aplicação não está a utilizar esta propriedade novo com ainda. 

### <a name="use-the-new-property"></a>Utilize a nova propriedade

Efetuar algumas alterações no seu código para utilizar o `Done` propriedade. De simplicidade neste tutorial, que só vai alterar o `Index` e `Create` vistas para ver a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localizar o `Create()` método na linha 52 e adicionar `Done` à lista de propriedades no `Bind` atributo. Quando tiver terminado, a `Create()` assinatura do método aparente ser o seguinte código:

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

No código Razor, deverá ver uma `<div class="form-group">` elemento que utiliza `model.Description`e, em seguida, outra `<div class="form-group">` elemento que utiliza `model.CreatedDate`. Imediatamente a seguir estes dois elementos, adicione outro `<div class="form-group">` elemento que utiliza `model.Done`:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
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

Localizar o `<td>` elemento que contém o `Html.ActionLink()` métodos de programa auxiliar. _Acima_ isto `<td>`, adicione outro `<td>` elemento com o seguinte código Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

É tudo o que precisa para ver as alterações no `Index` e `Create` vistas. 

Tipo `Ctrl+F5` para executar a aplicação.

Agora pode adicionar um item de tarefas e verifique **feito**. Em seguida, deve agora mostrar cópias de segurança na sua home page como um item concluído. Lembre-se de que o `Edit` vista não apresentar o `Done` campo, porque não foi alterado o `Edit` vista.

### <a name="enable-code-first-migrations-in-azure"></a>Ativar migrações primeiro do código no Azure

Agora que o seu código alterar funciona, incluindo a migração de base de dados, publicá-lo à sua aplicação web do Azure e atualizar a base de dados do SQL Server com o código de migrações primeiro demasiado.

Tal como anteriormente, clique no seu projeto e selecione **publicar**.

Clique em **definições** para abrir o Assistente de publicação.

![Definições de publicação aberta](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

No assistente, clique em **seguinte**.

Certifique-se de que a cadeia de ligação da base de dados SQL é preenchida no **MyDatabaseContext (MyDbConnection)**. Poderá ter de selecionar o **myToDoAppDb** base de dados na lista pendente. 

Selecione **executar código primeiro migrações (em execução no início da aplicação)**, em seguida, clique em **guardar**.

![Ativar migrações primeiro código na aplicação web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publicar as suas alterações

Agora que ativou migrações primeiro código na sua aplicação web do Azure, publique as suas alterações de código.

Na página de publicação, clique em **Publicar**.

Tente novamente adicionar itens de tarefas e selecione **feito**, e estes deverão aparecer na sua home page como um item concluído.

![Aplicação web do Azure após a migração primeiro código](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Todos os seus itens de tarefas existentes ainda são apresentados. Quando voltar a publicar a aplicação ASP.NET, os dados existentes na sua base de dados do SQL Server não são perdidos. Além disso, migrações primeiro código apenas altera o esquema de dados e mantém os dados existentes intactas.


## <a name="stream-application-logs"></a>Registos de aplicações de fluxo

Pode transmitir mensagens de rastreio diretamente a partir da sua aplicação web do Azure para Visual Studio.

Abra _Controllers\TodosController.cs_.

Cada ação começa com um `Trace.WriteLine()` método. Este código é adicionado ao mostrar-lhe como adicionar mensagens de rastreio à sua aplicação web do Azure.

### <a name="open-server-explorer"></a>Explorador de servidores aberta

Do **vista** menu, selecione **Explorador de servidores**. Pode configurar o registo para a sua aplicação web do Azure no **Explorador de servidores**. 

### <a name="enable-log-streaming"></a>Ativar o registo de transmissão em fluxo

No **Explorador de servidores**, expanda **Azure** > **do serviço de aplicações**.

Expanda o **myResourceGroup** grupo de recursos que criou quando criou a aplicação web do Azure.

A aplicação web do Azure com o botão direito e selecione **ver registos de transmissão em fluxo**.

![Ativar o registo de transmissão em fluxo](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Os registos são agora transmissão em fluxo para o **saída** janela. 

![Registo de transmissão em fluxo numa janela de saída](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

No entanto, não vir qualquer uma das mensagens de rastreio ainda. Da porque quando primeiro selecione **ver registos de transmissão em fluxo**, a aplicação web do Azure define o rastreio para `Error`, que apenas os registos de eventos de erro (com o `Trace.TraceError()` método).

### <a name="change-trace-levels"></a>Níveis de rastreio de alterações

Para alterar os níveis de rastreio para outras mensagens de rastreio de saída, volte atrás para **Explorador de servidores**.

Faça duplo clique novamente a aplicação web do Azure e selecione **ver definições**.

No **registo na aplicação (sistema de ficheiros)** lista pendente, selecione **verboso**. Clique em **Guardar**.

![Alterar o nível de rastreio verboso](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Pode experimentar com níveis de rastreio diferentes para ver os tipos de mensagens são apresentados para cada nível. Por exemplo, o **informações** nível inclui todos os registos criados pela `Trace.TraceInformation()`, `Trace.TraceWarning()`, e `Trace.TraceError()`, mas não os registos criados pela `Trace.WriteLine()`.
>
>

No seu browser, navegue para a sua aplicação web novamente em *http://&lt;o nome da aplicação >. azurewebsites.net*, em seguida, tente clicar em torno da aplicação de lista de tarefas no Azure. As mensagens de rastreio são agora transmissão em fluxo para o **saída** janela no Visual Studio.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>Parar o registo de transmissão em fluxo

Para parar o serviço de registo para transmissão em fluxo, clique em de **parar a monitorização** clique no botão no **saída** janela.

![Parar o registo de transmissão em fluxo](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Gerir a sua aplicação web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver a aplicação web que criou. 



No menu à esquerda, clique em **Serviço de Aplicações** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Atingiu na página da aplicação web. 

Por predefinição, o portal mostra o **descrição geral** página. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as páginas de configuração diferente, que pode abrir. 

![Página Serviço de Aplicações no portal do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma base de dados do SQL Server no Azure
> * Ligar uma aplicação ASP.NET para a base de dados SQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e volte a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

Avançar para o próximo tutorial para saber como mapear um nome DNS personalizado para a aplicação web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para as Aplicações Web do Azure](app-service-web-tutorial-custom-domain.md)
