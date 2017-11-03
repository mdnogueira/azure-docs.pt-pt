---
title: Como trabalhar com o servidor de back-end .NET SDK para Mobile Apps | Microsoft Docs
description: Saiba como trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure App Service.
keywords: "serviço de aplicações, serviço de aplicações do azure, aplicação móvel, serviço móvel, dimensionamento, dimensionável, implementação, azure aplicação implementação da aplicação"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 1728e1d76f075eae8f5500afa34674785f8e3848
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabalhe com o SDK do servidor de back-end .NET para Aplicações Móveis do Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este tópico mostra como utilizar o servidor de back-end de .NET SDK no principais cenários de Mobile Apps do Azure App Service. O SDK de aplicações móveis do Azure ajuda-o a trabalhar com os clientes móveis da sua aplicação ASP.NET.

> [!TIP]
> O [.NET SDK do servidor para Mobile Apps do Azure] [ 2] é open source no GitHub. O repositório contém todos os código de origem, incluindo o conjunto de aplicações de teste de unidade SDK do servidor completo e alguns projetos de exemplo.
>
>

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o servidor SDK está localizada aqui: [referência de .NET do Azure Mobile Apps][1].

## <a name="create-app"></a>Como: criar um back-end de aplicação móvel de .NET
Se estiver a iniciar um novo projeto, pode criar uma aplicação de serviço de aplicações utilizando o [portal do Azure] ou o Visual Studio. Pode executar a aplicação de serviço de aplicações localmente ou publicar o projeto para a sua aplicação móvel de serviço de aplicações baseado na nuvem.

Se estiver a adicionar funcionalidades móveis para um projeto existente, consulte o [transferir e inicializar o SDK](#install-sdk) secção.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Criar um back-end de .NET no portal do Azure
Para criar um back-end móvel do serviço de aplicações, ou siga a [tutorial de início rápido] [ 3] ou siga estes passos:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Volta a *começar* painel, em **criar uma API de tabela**, escolha **c#** como o **idioma de back-end**. Clique em **transferir**, extraia os ficheiros de projeto comprimido no computador local e abra a solução no Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Criar um back-end do .NET utilizando o Visual Studio 2013 e o Visual Studio 2015
Instalar o [Azure SDK para .NET] [ 4] (versão 2.9.0 ou posterior) para criar um projeto de Mobile Apps do Azure no Visual Studio. Depois de ter instalado o SDK, crie uma aplicação ASP.NET, utilizando os seguintes passos:

1. Abra o **novo projeto** diálogo (de *ficheiro* > **novo** > **projeto …** ).
2. Expanda **modelos** > **Visual c#**e selecione **Web**.
3. Selecione **aplicação ASP.NET Web**.
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Em *ASP.NET 4.5.2 modelos*, selecione **aplicação móvel do Azure**. Verifique **anfitrião na nuvem** para criar um back-end móvel na nuvem para o qual pode publicar este projeto.
6. Clique em **OK**.

## <a name="install-sdk"></a>Como: transferir e inicializar o SDK
O SDK não está disponível na [NuGet.org]. Este pacote inclui a funcionalidade de base necessária para começar a utilizar o SDK. Para inicializar o SDK, terá de executar ações no **HttpConfiguration** objeto.

### <a name="install-the-sdk"></a>Instalar o SDK
Para instalar o SDK, clique com botão direito no projeto no Visual Studio, selecione servidor **gerir pacotes NuGet**, procure o [Microsoft.Azure.Mobile.Server] do pacote, em seguida, clique em **instalar**.

### <a name="server-project-setup"></a>Inicializar o projeto de servidor
Um projeto de servidor de back-end do .NET está inicializado semelhantes para outros projetos do ASP.NET, incluindo uma classe de startup da OWIN. Certifique-se de que tem de referenciar o pacote NuGet `Microsoft.Owin.Host.SystemWeb`. Para adicionar esta classe no Visual Studio, clique com o botão direito no seu projeto de servidor e selecione **adicionar** >
**Novo Item**, em seguida, **Web** > **geral** > **classe de Startup da OWIN**.  Uma classe é gerada com o atributo seguinte:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

No `Configuration()` método da sua classe de startup da OWIN, utilize um **HttpConfiguration** objeto para configurar o ambiente de Mobile Apps do Azure.
O exemplo seguinte inicializa o projeto de servidor com funcionalidades não foram adicionados:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para ativar funcionalidades individuais, tem de chamar os métodos de extensão no **MobileAppConfiguration** objeto antes de chamar **ApplyTo**. Por exemplo, o seguinte código adiciona as rotas predefinidas para todos os controladores de API que tem o atributo `[MobileAppController]` durante a inicialização:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

O guia de introdução do servidor de chamadas de portais do Azure **UseDefaultConfiguration()**. Este equivalente à configuração do seguinte:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Os métodos de extensão utilizados são:

* `AddMobileAppHomeController()`Fornece a predefinição Mobile Apps do Azure home page.
* `MapApiControllers()`Fornece capacidades de API personalizadas para os controladores de end WebAPI decorado com o `[MobileAppController]` atributo.
* `AddTables()`Fornece um mapeamento do `/tables` pontos finais para controladores de tabela.
* `AddTablesWithEntityFramework()`é uma abreviada para mapeamento de `/tables` pontos finais utilizando o Entity Framework com base em controladores.
* `AddPushNotifications()`Fornece um método simples de registo de dispositivos para os Notification Hubs.
* `MapLegacyCrossDomainController()`Fornece os cabeçalhos de CORS padrão para o desenvolvimento local.

### <a name="sdk-extensions"></a>Extensões do SDK
Os seguintes pacotes de extensão com base em NuGet fornecem várias funcionalidades móveis que podem ser utilizadas pela sua aplicação. Ativar extensões durante a inicialização utilizando o **MobileAppConfiguration** objeto.

* [Microsoft.Azure.Mobile.Server.Quickstart] suporta a configuração básica de aplicações móveis. Adicionado à configuração ao chamar o **UseDefaultConfiguration** método de extensão durante a inicialização. Esta extensão inclui os seguintes extensões: notificações, a autenticação, a entidade, tabelas, entre domínios e os pacotes de casa. Este pacote é utilizado pelo Guia de introdução do aplicações móveis disponíveis no portal do Azure.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementa a predefinição *esta aplicação móvel está a funcionar página* para a raiz do web site. Adicionar a configuração ao chamar o **AddMobileAppHomeController** método de extensão.
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) inclui classes para trabalhar com dados e conjuntos de cópia de segurança do pipeline de dados. Adicionar a configuração ao chamar o **AddTables** método de extensão.
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite que o Entity Framework para aceder a dados na base de dados SQL. Adicionar a configuração ao chamar o **AddTablesWithEntityFramework** método de extensão.
* [Microsoft.Azure.Mobile.Server.Authentication] permite a autenticação e o middleware OWIN utilizado para validar os tokens de segurança de conjuntos. Adicionar a configuração ao chamar o **AddAppServiceAuthentication** e **IAppBuilder**. **UseAppServiceAuthentication** métodos de extensão.
* [Microsoft.Azure.Mobile.Server.Notifications] permite notificações push e define um ponto de final do registo de push. Adicionar a configuração ao chamar o **AddPushNotifications** método de extensão.
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) cria um controlador que serve os dados para browsers web legado da sua aplicação móvel. Adicionar a configuração ao chamar o **MapLegacyCrossDomainController** método de extensão.
* [Microsoft.Azure.Mobile.Server.Login] fornece o método AppServiceLoginHandler.CreateToken(), que é um método estático utilizado durante a cenários de autenticação personalizado.

## <a name="publish-server-project"></a>Como: publicar o projeto de servidor
Esta secção mostra como publicar o seu projeto de back-end do .NET a partir do Visual Studio. Também pode implementar o projeto de back-end utilizando [Git](../app-service/app-service-deploy-local-git.md) ou qualquer uma das outra existe disponível métodos.

1. No Visual Studio, reconstrua o projeto para restaurar os pacotes NuGet.
2. No Explorador de soluções, clique com botão direito no projeto, clique em **publicar**. A primeira que publicar, tem de definir um perfil de publicação. Quando já tiver um perfil definido, pode selecioná-lo e clique em **publicar**.
3. Se lhe for perguntado para selecionar um destino de publicar, clique em **App Service do Microsoft Azure** > **seguinte**, em seguida, (se necessário) início de sessão com as suas credenciais do Azure.
   Visual Studio transfere e armazena de forma segura a publicar definições diretamente a partir do Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Escolha o **subscrição**, selecione **tipo de recurso** de **vista**, expanda **aplicação móvel**e clique em back-end da aplicação móvel, em seguida, clique em **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Verifique as informações de perfil de publicação e clique em **publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Quando o back-end da aplicação móvel publicou com êxito, verá uma página de destino que indica êxito.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Como: definir um controlador de tabela
Defina um controlador de tabela para expor uma tabela SQL para os clientes móveis.  Configurar um controlador de tabela requer três passos:

1. Crie uma classe de objeto de transferência de dados (DTO).
2. Configure uma referência de tabela na classe Mobile DbContext.
3. Crie um controlador de tabela.

Um objeto de transferência de dados (DTO) é um simples c# objeto que herda de `EntityData`.  Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

O DTO é utilizado para definir a tabela na base de dados SQL.  Para criar a entrada de base de dados, adicione um `DbSet<>` propriedade DbContext estiver a utilizar.  O modelo de projeto predefinido para Mobile Apps do Azure, é designado o DbContext `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Se tiver instalado o SDK do Azure, agora, pode criar um controlador de tabela do modelo da seguinte forma:

1. Clique com o botão direito na pasta de controladores e selecione **adicionar** > **controlador...** .
2. Selecione o **controlador de tabela do Azure Mobile Apps** opção, em seguida, clique em **adicionar**.
3. No **Adicionar controlador** diálogo:
   * No **classe de modelo** lista pendente, selecione o novo DTO.
   * No **DbContext** lista pendente, selecione a classe DbContext de serviço móvel.
   * O nome de controlador é criado para si.
4. Clique em **Adicionar**.

O projeto de servidor de início rápido contém um exemplo de uma simples **TodoItemController**.

### <a name="adjust-pagesize"></a>Como: ajustar o tamanho de paginação de tabela
Por predefinição, o Azure Mobile Apps devolve 50 registos por pedido.  A paginação assegura que o cliente não significar as respetivas thread de IU nem o servidor há demasiado tempo, garantir uma boa experiência de utilizador. Para alterar o tamanho de paginação de tabela, aumente o lado do servidor "tamanho de consulta permitido" e a página do lado do cliente tamanho lado do servidor "tamanho de consulta permitido" é ajustado através de `EnableQuery` atributo:

    [EnableQuery(PageSize = 500)]

Certifique-se de que o PageSize é igual ou maior do que o tamanho pedido pelo cliente.  Consulte o documentação de HOWTO para obter detalhes sobre como alterar o tamanho de página do cliente de cliente específico.

## <a name="how-to-define-a-custom-api-controller"></a>Como: definir um controlador de API personalizado
O controlador da API personalizado fornece a funcionalidade mais básica para o back-end da aplicação móvel da exposição de um ponto final. Pode registar um controlador de API mobile específico utilizando o atributo [MobileAppController]. O `MobileAppController` atributo regista a rota, configura o serializador de JSON de aplicações móveis e ativa a [a verificação de versão do cliente](app-service-mobile-client-and-server-versioning.md).

1. No Visual Studio, clique com o botão direito na pasta de controladores, em seguida, clique em **adicionar** > **controlador**, selecione **Web API 2 controlador&mdash;vazio** e clique em **adicionar**.
2. Forneça um **nome do controlador**, tais como `CustomController`e clique em **adicionar**.
3. No ficheiro novo para a classe de controlador, adicione o seguinte utilizando a instrução:

        using Microsoft.Azure.Mobile.Server.Config;
4. Aplicar o **[MobileAppController]** atributo para a definição de classe de controlador de API, como no exemplo seguinte:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. No ficheiro de App_Start/Startup.MobileApp.cs, adicione uma chamada para o **MapApiControllers** método de extensão, como no exemplo seguinte:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Também pode utilizar o `UseDefaultConfiguration()` método de extensão em vez de `MapApiControllers()`. Qualquer controlador que não tenha **MobileAppControllerAttribute** aplicadas ainda podem ser acedidos por clientes, mas que poderá não estar corretamente consumidos pelos próprios clientes através de qualquer cliente SDK de aplicação móvel.

## <a name="how-to-work-with-authentication"></a>Como: trabalhar com autenticação
Mobile Apps do Azure utiliza a autenticação do serviço de aplicação / autorização para proteger o seu back-end móvel.  Esta secção mostra como efetuar as seguintes tarefas relacionadas com a autenticação no seu projeto de servidor de back-end do .NET:

* [Como: Adicionar autenticação para um projeto de servidor](#add-auth)
* [Como: utilizar a autenticação personalizada para a sua aplicação](#custom-auth)
* [Como: obter autenticado informações do utilizador](#user-info)
* [Como: restringir o acesso a dados para os utilizadores autorizados](#authorize)

### <a name="add-auth"></a>Como: Adicionar autenticação para um projeto de servidor
Pode adicionar autenticação para o projeto de servidor ao alargar o **MobileAppConfiguration** objeto e configurar o OWIN middleware. Quando instala o [Microsoft.Azure.Mobile.Server.Quickstart] pacote e a chamada de **UseDefaultConfiguration** método de extensão, pode avançar para o passo 3.

1. No Visual Studio, instale o [Microsoft.Azure.Mobile.Server.Authentication] pacote.
2. No ficheiro de projeto Startup.cs, adicione a seguinte linha de código no início do **configuração** método:

        app.UseAppServiceAuthentication(config);

    Este componente de middleware OWIN valida tokens emitidos pelo gateway de aplicação de serviço associado.
3. Adicionar o `[Authorize]` atributo qualquer controlador de método ou que requer autenticação.

Para saber mais sobre como autenticar clientes ao back-end das Mobile Apps, consulte [adicionar autenticação à sua aplicação](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Como: utilizar a autenticação personalizada para a sua aplicação
Se não pretender utilizar um dos fornecedores de autenticação/autorização do App Service, pode implementar o seu próprio sistema de início de sessão. Instalar o [Microsoft.Azure.Mobile.Server.Login] pacote para melhorar a geração de token de autenticação.  Forneça o seu próprio código para validar as credenciais do utilizador. Por exemplo, pode verificar contra salted com hash palavras-passe e numa base de dados. No exemplo abaixo, o `isValidAssertion()` método (definido noutro local) é responsável por estas verificações.

A autenticação personalizada está exposta ao criar um ApiController e expor `register` e `login` ações. O cliente deve utilizar uma IU personalizada para recolher as informações do utilizador.  As informações em seguida, são submetidas para a API com uma chamada de POST de HTTP padrão. Depois do servidor valida a asserção, um token emitido utilizando o `AppServiceLoginHandler.CreateToken()` método.  O ApiController **não devem** utilizar o `[MobileAppController]` atributo.

Um exemplo `login` ação:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

No exemplo anterior, LoginResult LoginResultUser são e objetos serializáveis exposição propriedades necessárias. O cliente espera respostas de início de sessão a ser devolvido como objetos JSON com o formato:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

O `AppServiceLoginHandler.CreateToken()` método inclui um *público-alvo* e um *emissor* parâmetro. Ambos estes parâmetros são definidos para o URL de raiz da sua aplicação, utilizando o esquema de HTTPS. Da mesma forma, deve definir *secretKey* para ser o valor da sua aplicação da chave de assinatura. Não distribua a chave de assinatura num cliente de como pode ser utilizado para mint chaves e representar os utilizadores. Pode obter a chave de assinatura enquanto alojada no App Service ao consultar o *Web site\_AUTH\_assinatura\_chave* variável de ambiente. Se for necessário um contexto de depuração local, siga as instruções no [depuração Local com a autenticação](#local-debug) secção para obter a chave e armazená-lo como uma definição de aplicação.

O token emitido pode também incluir outras afirmações e uma data de expiração.  No mínimo, o token emitido tem de incluir um assunto (**sub**) de afirmação.

Pode suportar o cliente padrão `loginAsync()` método por sobrecarregar a rota de autenticação.  Se o cliente chama `client.loginAsync('custom');` para iniciar sessão, a rota tem de ser `/.auth/login/custom`.  Pode definir a rota para o controlador de autenticação personalizado utilizando `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Utilizar o `loginAsync()` abordagem garante que o token de autenticação está ligado a cada chamada subsequente para o serviço.
>
>

### <a name="user-info"></a>Como: obter autenticado informações do utilizador
Quando um utilizador é autenticado pelo App Service, pode aceder o ID de utilizador atribuído e outras informações no seu código de back-end do .NET. As informações de utilizador podem ser utilizadas para tomar decisões de autorização no back-end. O código seguinte obtém o ID de utilizador associado a um pedido:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

O SID é derivado do ID de utilizador específica do fornecedor e é estático para um determinado utilizador e o fornecedor de início de sessão.  O SID é nulo para os tokens de autenticação inválidos.

Também o App Service permite-lhe pedir afirmações específicas do seu fornecedor de início de sessão. Cada fornecedor de identidade pode fornecer mais informações, utilizando o SDK do fornecedor de identidade.  Por exemplo, pode utilizar a Graph API do Facebook para obter informações de amigos.  Pode especificar as afirmações que são pedidas no painel do fornecedor no portal do Azure. Alguns afirmações requerem configuração adicional com o fornecedor de identidade.

O seguinte código chamadas a **GetAppServiceIdentityAsync** método de extensão ao obter o token das credenciais de início de sessão, que incluem o acesso necessário para fazer pedidos contra a Graph API do Facebook:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Adicionar um utilizando a instrução para `System.Security.Principal` para fornecer o **GetAppServiceIdentityAsync** método de extensão.

### <a name="authorize"></a>Como: restringir o acesso a dados para os utilizadores autorizados
Na secção anterior, iremos mostrou como obter o ID de utilizador de um utilizador autenticado. Pode restringir o acesso a dados e outros recursos baseados neste valor. Por exemplo, adicionar uma coluna de userId a tabelas e filtrar os resultados da consulta, o ID de utilizador são uma forma simple para limitar os dados devolvidos apenas a utilizadores autorizados. O seguinte código devolve linhas de dados apenas quando o SID corresponde ao valor na coluna na tabela TodoItem UserId:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

O `Query()` método devolve um `IQueryable` que pode ser manipulado pelo LINQ para processar a filtragem.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Como: Adicionar push notificações para um projeto de servidor
Adicionar notificações push para o projeto de servidor ao alargar o **MobileAppConfiguration** objeto e a criação de um cliente de Notification Hubs.

1. No Visual Studio, clique com o botão direito no projeto de servidor e clique em **gerir pacotes NuGet**, procure `Microsoft.Azure.Mobile.Server.Notifications`, em seguida, clique em **instalar**.
2. Repita este passo para instalar o `Microsoft.Azure.NotificationHubs` pacote, que inclui a biblioteca de clientes de Notification Hubs.
3. No App_Start/Startup.MobileApp.cs e adicione uma chamada para o **AddPushNotifications()** método de extensão durante a inicialização:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adicione o seguinte código que cria um cliente de Hubs de notificação:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Agora, pode utilizar o cliente de Notification Hubs para enviar notificações push para dispositivos registados. Para obter mais informações, consulte [adicionar notificações push à sua aplicação](app-service-mobile-ios-get-started-push.md). Para saber mais sobre os Notification Hubs, consulte [descrição geral dos Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Como: Ativar direcionados push utilizando etiquetas
Hubs de notificação permite-lhe enviar notificações de destino para registos específicos utilizando etiquetas. Várias etiquetas são automaticamente criadas:

* O ID de instalação identifica um dispositivo específico.
* O Id de utilizador com base num SID autenticado identifica um utilizador específico.

A instalação ID pode ser acedido a partir de **installationId** propriedade o **MobileServiceClient**.  O exemplo seguinte mostra como utilizar um ID de instalação para adicionar uma etiqueta para uma instalação específica no Hubs de notificação:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

As etiquetas fornecidas pelo cliente durante o registo da notificação push são ignoradas pelo back-end ao criar a instalação. Para ativar um cliente adicionar etiquetas à instalação, tem de criar uma API personalizada que adiciona as etiquetas a utilizar o padrão anterior.

Consulte [etiquetas de notificação push de cliente adicionado] [ 5] no exemplo de início rápido concluída Mobile Apps do App Service para obter um exemplo.

## <a name="push-user"></a>Como: enviar notificações de push para um utilizador autenticado
Quando um utilizador autenticado regista para notificações push, uma tag de ID de utilizador é adicionada automaticamente para o registo. Ao utilizar esta etiqueta, pode enviar notificações push para todos os dispositivos registados por essa pessoa. O código seguinte obtém o SID do utilizador que efetua o pedido e envia uma notificação push de modelo para cada registo de dispositivos para essa pessoa:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Quando a registar notificações push a partir de um cliente autenticado, certifique-se de que a autenticação é concluída antes de repetir o registo. Para obter mais informações, consulte [Push a utilizadores] [ 6] no exemplo de início rápido concluída Mobile Apps do App Service para o back-end .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Como: depurar e resolver problemas relacionados com o SDK do servidor .NET
App Service do Azure fornece várias depuração e resolução de problemas de técnicas para aplicações ASP.NET:

* [Monitorizar um serviço de aplicações do Azure](../app-service/web-sites-monitor.md)
* [Ativar o registo de diagnóstico no App Service do Azure](../app-service/web-sites-enable-diagnostic-log.md)
* [Resolver problemas de um serviço de aplicações do Azure no Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Registo
Pode escrever os registos de diagnóstico do serviço de aplicações utilizando a escrita de rastreio ASP.NET padrão. Antes de poder escrever nos registos, tem de ativar o diagnóstico no back-end da aplicação móvel.

Para ativar o diagnóstico e escrever nos registos:

1. Siga os passos no [como ativar o diagnóstico](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Adicione o seguinte utilizando a instrução no seu ficheiro de código:

        using System.Web.Http.Tracing;
3. Crie um escritor de rastreio para escrever a partir de back-end do .NET para os registos de diagnóstico, da seguinte forma:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Voltar a publicar o projeto de servidor e o back-end da aplicação móvel para executar o caminho de código com o registo de acesso.
5. Transferir e avaliar os registos, conforme descrito em [como: transferir registos](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Local de depuração com autenticação
Pode executar a aplicação localmente para testar as alterações antes de publicá-los para a nuvem. Para a maioria das back-ends de Mobile Apps do Azure, prima *F5* enquanto estiver no Visual Studio. No entanto, existem algumas considerações adicionais ao utilizar a autenticação.

Tem de ter uma aplicação móvel baseado na nuvem com aplicação serviço de autenticação/autorização configurada e o cliente tem de ter o ponto final da nuvem especificado como o anfitrião de início de sessão alternativo. Consulte a documentação para a sua plataforma de cliente para os passos específicos necessários.

Certifique-se de que o seu back-end móvel tem [Microsoft.Azure.Mobile.Server.Authentication] instalado. Em seguida, na classe de startup da OWIN da sua aplicação, adicione o seguinte, após `MobileAppConfiguration` foi aplicada ao seu `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

No exemplo anterior, deve configurar o *authAudience* e *authIssuer* definições da aplicação no seu Web. config de ficheiros para cada ser o URL de raiz da sua aplicação, utilizando o esquema de HTTPS. Da mesma forma, deve definir *authSigningKey* para ser o valor da sua aplicação da chave de assinatura.
Para obter a chave de assinatura:

1. Navegue para a sua aplicação dentro de [portal do Azure]
2. Clique em **ferramentas**, **Kudu**, **aceda**.
3. No site de gestão do Kudu, clique em **ambiente**.
4. Localize o valor para *Web site\_AUTH\_assinatura\_chave*.

Utilize a chave de assinatura para o *authSigningKey* parâmetro na sua configuração de aplicação local.  Agora, o back-end móvel está equipado para validar os tokens ao executar localmente, o cliente obtém o token do ponto final baseado na nuvem.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[portal do Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
