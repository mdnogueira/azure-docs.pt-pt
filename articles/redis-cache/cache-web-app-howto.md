---
title: "Como criar uma aplicação Web com a Cache de Redis | Microsoft Docs"
description: "Saiba como criar uma aplicação Web com a Cache de Redis"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 10/11/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 425729dde12d8477b7b8ecd6394b31b7c5a5e92b


---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Como criar uma aplicação Web com a Cache de Redis
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [python](cache-python-get-started.md)
> 
> 

Este tutorial mostra como criar e implementar uma aplicação Web ASP.NET numa aplicação Web no App Service do Azure com o Visual Studio 2015. A aplicação de exemplo mostra uma lista de estatísticas de equipa de uma base de dados e mostra-lhe diversas formas de utilizar a Cache de Redis do Azure para armazenar e recuperar dados da cache. Quando concluir o tutorial, terá uma aplicação Web em execução que lê e escreve numa base de dados, otimizada com a Cache de Redis do Azure e alojada no Azure.

Irá aprender:

* Como criar uma aplicação Web ASP.NET MVC 5 no Visual Studio.
* Como aceder a dados a partir de uma base de dados com o Entity Framework.
* Como melhorar o débito de dados e reduzir a carga da base de dados ao armazenar e obter dados com a Cache de Redis do Azure.
* Como utilizar um conjunto ordenado de Redis para obter as cinco melhores equipas.
* Como aprovisionar os recursos do Azure para a aplicação utilizando um modelo do Resource Manager.
* Como publicar a aplicação no Azure com o Visual Studio.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir o tutorial, deve ter os seguintes pré-requisitos.

* [Conta do Azure](#azure-account)
* [Visual Studio 2015 com o Azure SDK para .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Conta do Azure
Para concluir o tutorial, precisa de uma conta do Azure. Pode:

* [Criar uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Receberá créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure.
* [Ativar os benefícios de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>Visual Studio 2015 com o Azure SDK para .NET
O tutorial foi escrito para o Visual Studio 2015 com o [Azure SDK para .NET](../dotnet-sdk.md) 2.8.2 ou posterior. [Transfira aqui o Azure SDK mais recente para o Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). O Visual Studio é instalado automaticamente com o SDK se ainda não o tiver.

Se tiver o Visual Studio 2013, pode [transferir o Azure SDK mais recente para o Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Alguns ecrãs podem parecer diferentes das ilustrações mostradas neste tutorial.

> [!NOTE]
> Dependendo do número de dependências do SDK que já tem no computador, instalar o SDK pode demorar muito tempo, de alguns minutos a meia hora ou mais.
> 
> 

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio
1. Abra o Visual Studio e clique em **Ficheiro**, **Novo**, **Projeto**.
2. Expanda o nó **Visual C#** na lista de **Modelos**, selecione **Nuvem** e clique em **Aplicação Web ASP.NET**. Verifique se o **.NET Framework 4.5.2** está selecionado.  Escreva **ContosoTeamStats** na caixa de texto **Nome** e clique em **OK**.
   
    ![Criar o projeto][cache-create-project]
3. Selecione **MVC** como tipo de projeto. Limpe a caixa de verificação **Alojar na nuvem**. Vai [aprovisionar os recursos do Azure](#provision-the-azure-resources) e [publicar a aplicação no Azure](#publish-the-application-to-azure) nos passos subsequentes do tutorial. Para obter um exemplo de aprovisionamento de uma aplicação Web do App Service a partir do Visual Studio com a opção **Alojar na nuvem** marcada, veja [Introdução às Aplicações Web no App Service do Azure com o ASP.NET e o Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).
   
    ![Selecionar o modelo de projeto][cache-select-template]
4. Clique em **OK** para criar o projeto.

## <a name="create-the-aspnet-mvc-application"></a>Criar a aplicação ASP.NET MVC
Nesta secção do tutorial, vai criar a aplicação básica para ler e apresentar estatísticas da equipa a partir de uma base de dados.

* [Adicionar o modelo](#add-the-model)
* [Adicionar o controlador](#add-the-controller)
* [Configurar as vistas](#configure-the-views)

### <a name="add-the-model"></a>Adicionar o modelo
1. Clique com o botão direito do rato em **Modelos** no **Explorador de Soluções** e escolha **Adicionar**, **Classe**. 
   
    ![Adicionar o modelo][cache-model-add-class]
2. Introduza `Team` para o nome de classe e clique em **Adicionar**.
   
    ![Adicionar a classe de modelo][cache-model-add-class-dialog]
3. Substitua as instruções `using` na parte superior do ficheiro `Team.cs` pelas seguintes instruções “using”.

        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


1. Substitua a definição da classe `Team` pelo seguinte fragmento de código que contém uma definição da classe `Team` atualizada, bem como outras classes do programa auxiliar do Entity Framework. Para obter mais informações sobre a abordagem Code First para o Entity Framework utilizado neste tutorial, veja [Code first to a new database (Code First para criar uma nova base de dados)](https://msdn.microsoft.com/data/jj193542).

        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }

            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();

                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }

        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }

            public DbSet<Team> Teams { get; set; }
        }

        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };

                Team.PlayGames(teams);

                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }

        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


1. No **Explorador de Soluções**, faça duplo clique em **web.config** para abri-lo.
   
    ![Web.config][cache-web-config]
2. Adicione a seguinte cadeia de ligação à secção `connectionStrings`. O nome da cadeia de ligação tem de corresponder ao nome da classe de contexto da base de dados do Entity Framework, que é `TeamContext`.
   
       <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />

    Depois da adição, o aspeto da secção `connectionStrings` deverá ser semelhante ao seguinte exemplo.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>Adicionar o controlador
1. Prima **F6** para criar o projeto. 
2. No **Explorador de Soluções**, clique com o botão direito do rato na pasta **Controladores** e escolha **Adicionar**, **Controlador**.
   
    ![Adicionar o controlador][cache-add-controller]
3. Escolha **Controlador MVC 5 com vistas através do Entity Framework** e clique em **Adicionar**. Se for apresentado um erro depois de clicar em **Adicionar**, confirme que criou primeiro o projeto.
   
    ![Adicionar a classe de controlador][cache-add-controller-class]
4. Selecione **Team (ContosoTeamStats.Models)** na lista pendente **Classe de modelo**. Selecione **TeamContext (ContosoTeamStats.Models)** na lista pendente **Classe de contexto de dados**. Introduza `TeamsController` na caixa de texto **Nome do controlador** (se não for automaticamente povoado). Clique em **Adicionar** para criar a classe de controlador e adicionar vistas predefinidas.
   
    ![Configurar o controlador][cache-configure-controller]
5. No **Explorador de Soluções**, expanda **Global.asax** e faça duplo clique em **Global.asax.cs** para abri-lo.
   
    ![Global.asax.cs][cache-global-asax]
6. Adicione as duas instruções “using” seguintes na parte superior do ficheiro, sob as outras declarações “using”.

        using System.Data.Entity;
        using ContosoTeamStats.Models;


1. Adicione a seguinte linha de código no final do método `Application_Start`.

        Database.SetInitializer<TeamContext>(new TeamInitializer());


1. No **Explorador de Soluções**, expanda `App_Start` e faça duplo clique em `RouteConfig.cs`.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Substitua `controller = "Home"` no seguinte código no método `RegisterRoutes` por `controller = "Teams"`, conforme mostrado no seguinte exemplo.

        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>Configurar as vistas
1. No **Explorador de Soluções**, expanda a pasta **Vistas**, em seguida, a pasta **Partilhado** e faça duplo clique em **_Layout.cshtml**. 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. Altere o conteúdo do elemento `title` e substitua `My ASP.NET Application` por `Contoso Team Stats`, conforme mostrado no exemplo seguinte.

        <title>@ViewBag.Title - Contoso Team Stats</title>


1. Na secção `body`, atualize a primeira instrução `Html.ActionLink` e substitua `Application name` por `Contoso Team Stats` e `Home` por `Teams`.
   
   * Antes: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * Depois: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Alterações do código][cache-layout-cshtml-code]
2. Prima **Ctrl+F5** para criar e executar a aplicação. Esta versão da aplicação lê os resultados diretamente na base de dados. Tenha em atenção que as ações **Criar Nova**, **Editar**, **Detalhes** e **Eliminar** foram automaticamente adicionadas à aplicação pela estrutura do **Controlador 5 MVC com vistas através do Entity Framework**. Na secção seguinte do tutorial, vai adicionar a Cache de Redis para otimizar o acesso aos dados e fornecer funcionalidades adicionais à aplicação.

![Aplicação de arranque][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurar a aplicação para utilizar a Cache de Redis
Nesta secção do tutorial, vai configurar a aplicação de exemplo para armazenar e obter as estatísticas da equipa da Contoso a partir de uma instância da Cache de Redis do Azure, com o cliente de cache [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

* [Configurar a aplicação para utilizar o StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Atualizar a classe TeamsController para devolver resultados da cache ou da base de dados](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Atualizar os métodos Criar, Editar e Eliminar para trabalhar com a cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Atualizar a vista Índice de Equipas para trabalhar com a cache](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar a aplicação para utilizar o StackExchange.Redis
1. Para configurar uma aplicação cliente no Visual Studio com o pacote NuGet StackExchange.Redis, clique com o botão direito do rato no projeto em **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**. 
   
    ![Gerir pacotes NuGet][redis-cache-manage-nuget-menu]
2. Introduza **StackExchange.Redis** na caixa de texto de pesquisa, selecione a versão pretendida apresentada nos resultados e clique em **Instalar**.
   
    ![Pacote NuGet StackExchange.Redis][redis-cache-stack-exchange-nuget]
   
    O pacote NuGet transfere e adiciona as referências de assemblagem necessárias para a sua aplicação cliente aceder à Cache de Redis do Azure com o cliente de cache StackExchange.Redis. Se preferir utilizar uma versão da biblioteca de clientes **StackExchange.Redis** com nome seguro, escolha **StackExchange.Redis.StrongName**; caso contrário, escolha **StackExchange.Redis**.
3. No **Explorador de Soluções**, expanda a pasta **Controladores** e faça duplo clique em **TeamsController.cs** para abri-la.
   
    ![Controlador de equipas][cache-teamscontroller]
4. Adicione as duas instruções “using” seguintes ao **TeamsController.cs**.
   
        using System.Configuration;
        using StackExchange.Redis;
5. Adicione as duas propriedades seguintes à classe `TeamsController`.
   
        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
   
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
6. No computador, crie um ficheiro denominado `WebAppPlusCacheAppSecrets.config` e coloque-o numa localização que não será registada com o código fonte da aplicação de exemplo, caso decida registá-lo algures. Neste exemplo, o ficheiro `AppSettingsSecrets.config` está localizado em `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Edite o ficheiro `WebAppPlusCacheAppSecrets.config` e adicione o seguinte conteúdo. Se executar a aplicação localmente, estas informações serão utilizadas para estabelecer ligação à sua instância da Cache de Redis do Azure. Mais adiante no tutorial, terá de aprovisionar uma instância da Cache de Redis do Azure e atualizar o nome e a palavra-passe da cache. Se não tiver intenção de executar a aplicação de exemplo localmente, pode ignorar a criação deste ficheiro e os passos subsequentes que mencionam o ficheiro, porque quando implementa a aplicação no Azure, esta obtém as informações de ligação da cache com base nas definições da Aplicação Web e não com base neste ficheiro. Uma vez que `WebAppPlusCacheAppSecrets.config` não é implementado no Azure com a aplicação, o ficheiro não será necessário, exceto se executar a aplicação localmente.

        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


1. No **Explorador de Soluções**, faça duplo clique em **web.config** para abri-lo.
   
    ![Web.config][cache-web-config]
2. Adicione o seguinte atributo `file` ao elemento `appSettings`. Se utilizou um nome de ficheiro ou localização diferentes, substitua estes valores pelos valores mostrados no exemplo.
   
   * Antes: `<appSettings>`
   * Depois: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
     O tempo de execução do ASP.NET une o conteúdo do ficheiro externo e a marcação no elemento `<appSettings>`. O tempo de execução ignora o atributo de ficheiro se não for possível localizar o ficheiro especificado. Os segredos (a cadeia de ligação para a cache) não são incluídos como parte do código fonte da aplicação. Quando implementa a aplicação no Azure, o ficheiro `WebAppPlusCacheAppSecrests.config` não será implementado (tal como pretendido). Existem várias formas para especificar estes segredos no Azure e, neste tutorial, são automaticamente configurados quando [aprovisiona os recursos do Azure](#provision-the-azure-resources) num passo subsequente do tutorial. Para obter mais informações sobre como trabalhar com segredos no Azure, veja [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service (As melhores práticas para implementar palavras-passe e outros dados confidenciais no ASP.NET e no App Service do Azure)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Atualizar a classe TeamsController para devolver resultados da cache ou da base de dados
Neste exemplo, as estatísticas da equipa podem ser obtidas a partir da base de dados ou da cache. As estatísticas da equipa são armazenadas na cache como uma `List<Team>` serializada e também como um conjunto ordenado que utiliza tipos de dados de Redis. Ao obter itens de um conjunto ordenado, poderá obter alguns, todos ou consultar determinados itens. Neste exemplo, vai consultar o conjunto ordenado para obter as cinco melhores equipas classificadas por número de vitórias.

> [!NOTE]
> Não é necessário armazenar as estatísticas da equipa em vários formatos na cache para poder utilizar a Cache de Redis do Azure. Este tutorial utiliza vários formatos para demonstrar as diferentes formas e os diferentes tipos de dados que pode utilizar para colocar dados em cache.
> 
> 

1. Adicione as seguintes instruções “using” na parte superior do ficheiro `TeamsController.cs`, juntamente com as outras instruções “using”.
   
        using System.Diagnostics;
        using Newtonsoft.Json;
2. Substitua o atual método `public ActionResult Index()` pela seguinte implementação.

        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;

            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;

                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;

                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }

            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();

            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;

                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;

                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;

                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }

            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();

            return View(teams);
        }


1. Adicione os três métodos seguintes à classe `TeamsController` para implementar os tipos de ação `playGames`, `clearCache` e `rebuildDB` a partir da instrução switch adicionada ao fragmento de código anterior.
   
    O método `PlayGames` atualiza as estatísticas da equipa simulando uma época de jogos, guarda os resultados na base de dados e limpa os dados agora desatualizados da cache.

        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;

            Team.PlayGames(teams);

            db.SaveChanges();

            // Clear any cached results
            ClearCachedTeams();
        }


    O método `RebuildDB` reinicializa a base de dados com o conjunto de equipas predefinido, gera as estatísticas e limpa os dados agora desatualizados da cache.

        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    O método `ClearCachedTeams` remove quaisquer estatísticas de equipa em cache da cache.


        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


1. Adicione os quatro métodos seguintes à classe `TeamsController` para implementar as várias formas de obter as estatísticas da equipa a partir da cache e da base de dados. Cada um destes métodos devolve uma `List<Team>`, que é depois apresentada pela vista.
   
    O método `GetFromDB` lê as estatísticas da equipa a partir da base de dados.
   
        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
   
            return results.ToList<Team>();
        }

    O método `GetFromList` lê as estatísticas da equipa a partir da cache, como uma `List<Team>` serializada. Se existir uma falha de acerto na cache, as estatísticas da equipa são lidas a partir da base de dados e, em seguida, armazenadas na cache para utilização posterior. Neste exemplo, estamos a utilizar a serialização JSON.NET para serializar os objetos .NET de e para a cache. Para obter mais informações, veja [How to work with .NET objects in Azure Redis Cache (Como trabalhar com objetos .NET na Cache de Redis do Azure)](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    O método `GetFromSortedSet` lê as estatísticas da equipa a partir de um conjunto ordenado em cache. Se existir uma falha de acerto na cache, as estatísticas da equipa são lidos a partir da base de dados e armazenadas na cache como um conjunto ordenado.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";

                // Read from DB
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    O método `GetFromSortedSetTop5` lê as cinco melhores equipas a partir do conjunto ordenado em cache. Começa por verificar se a chave `teamsSortedSet` existe na cache. Se esta chave não estiver presente, o método `GetFromSortedSet` é chamado para ler as estatísticas da equipa e para armazená-las na cache. Em seguida, o conjunto ordenado em cache é consultado relativamente às cinco melhores equipas que são devolvidas.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Atualizar os métodos Criar, Editar e Eliminar para trabalhar com a cache
O código da estrtura gerado como parte deste exemplo inclui métodos para adicionar, editar e eliminar equipas. Sempre que uma equipa é adicionada, editada ou removida, os dados na cache ficam desatualizados. Nesta secção, vai modificar estes três métodos para limpar as equipas em cache para que a cache não fique dessincronizada relativamente à base de dados.

1. Navegue para o método `Create(Team team)`, na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no seguinte exemplo.

        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }

            return View(team);
        }


1. Navegue para o método `Edit(Team team)`, na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no seguinte exemplo.

        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


1. Navegue para o método `DeleteConfirmed(int id)`, na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no seguinte exemplo.

        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Atualizar a vista Índice de Equipas para trabalhar com a cache
1. No **Explorador de Soluções**, expanda a pasta **Vistas**, em seguida, a pasta **Equipas** e faça duplo clique em **Index.cshtml**.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Perto da parte superior do ficheiro, procure o seguinte elemento de parágrafo.
   
    ![Tabela de ações][cache-teams-index-table]
   
    Esta é a ligação para criar uma nova equipa. Substitua o elemento de parágrafo pela seguinte tabela. Esta tabela tem ligações de ação para criar uma nova equipa, reproduzir uma nova época de jogos, limpar a cache, obter as equipas a partir da cache em vários formatos, obter as equipas a partir da base de dados e reconstruir a base de dados com novos dados de exemplo.

        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


1. Desloque-se para o fim do ficheiro **Index.cshtml** e adicione o seguinte elemento `tr`, de forma a ser a última linha na última tabela do ficheiro.
   
        <tr><td colspan="5">@ViewBag.Msg</td></tr>
   
    Esta linha apresenta o valor de `ViewBag.Msg`, que contém um relatório de estado acerca da operação atual, que é definida quando clica numa das ligações de ação do passo anterior.   
   
    ![Mensagem de estado][cache-status-message]
2. Prima **F6** para criar o projeto.

## <a name="provision-the-azure-resources"></a>Aprovisionar os recursos do Azure
Para alojar a aplicação no Azure, deve primeiro aprovisionar os serviços do Azure que a sua aplicação requer. Neste tutorial, a aplicação de exemplo utiliza os seguintes serviços do Azure.

* Cache de Redis do Azure
* Applicação Web do App Service
* SQL Database

Para implementar estes serviços num grupo de recursos novo ou existente à sua escolha, clique no botão **Implementar no Azure** seguinte.

[![Implementar no Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

O botão **Implementar no Azure** utiliza o modelo [Criar uma Aplicação Web, Cache de Redis, SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) – [Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates) para aprovisionar estes serviços e definir a cadeia de ligação para a SQL Database e a definição da aplicação para a cadeia de ligação da Cache de Redis do Azure.

> [!NOTE]
> Se não tiver uma conta do Azure, pode [criar uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) em apenas alguns minutos.
> 
> 

Ao clicar no botão **Implementar no Azure**, será encaminhado para o Portal do Azure e iniciará o processo de criação dos recursos descritos pelo modelo.

![Implementar no Azure][cache-deploy-to-azure-step-1]

1. No painel **Implementação personalizada**, selecione a subscrição do Azure a utilizar, selecione um grupo de recursos existente ou crie um novo e especifique a localização do grupo de recursos.
2. No painel **Parâmetros**, especifique um nome de conta de administrador (**ADMINISTRATORLOGIN** – não utilize **admin**), a palavra-passe de início de sessão de administrador (**ADMINISTRATORLOGINPASSWORD**) e o nome da base de dados (**DATABASENAME**). Os outros parâmetros estão configurados para um plano de alojamento gratuito do App Service e para as opções de custo mais reduzido da SQL Database e da Cache de Redis do Azure, que não são fornecidos com o escalão gratuito.
3. Altere qualquer das outras definições, se assim o desejar, ou mantenha as predefinições e clique em **OK**.

![Implementar no Azure][cache-deploy-to-azure-step-2]

1. Clique em **Consultar termos legais**.
2. Leia os termos no painel **Comprar** e clique em **Comprar**.
3. Para começar a aprovisionar os recursos, clique em **Criar** no painel **Implementação personalizada**.

Para ver o progresso da implementação, clique no ícone de notificação e em **Implementação iniciada**.

![Implementação iniciada][cache-deployment-started]

Pode ver o estado da implementação no painel **Microsoft.Template**.

![Implementar no Azure][cache-deploy-to-azure-step-3]

Quando o aprovisionamento estiver concluído, pode publicar a aplicação no Azure a partir do Visual Studio.

> [!NOTE]
> Quaisquer erros que possam ocorrer durante o processo de aprovisionamento são apresentados no painel **Microsoft.Template**. Os erros comuns prendem-se a existência de demasiados SQL Servers ou demasiados planos de alojamento do App Service Gratuito por subscrição. Resolva os eventuais erros e reinicie o processo clicando em **Reimplementar** no painel **Microsoft.Template** ou no botão **Implementar no Azure** deste tutorial.
> 
> 

## <a name="publish-the-application-to-azure"></a>Publicar a aplicação no Azure
Neste passo do tutorial, vai publicar a aplicação no Azure e executá-la na nuvem.

1. Clique com o botão direito do rato no projeto **ContosoTeamStats** do Visual Studio e escolha **Publicar**.
   
    ![Publicar][cache-publish-app]
2. Clique em **App Service do Microsoft Azure**.
   
    ![Publicar][cache-publish-to-app-service]
3. Selecione a subscrição utilizada durante a criação dos recursos do Azure, expanda o grupo de recursos que contém os recursos, selecione a Aplicação Web pretendida e clique em **OK**. Se utilizou o botão **Implementar no Azure**, o nome da Aplicação Web começará por **webSite**, seguido por alguns carateres adicionais.
   
    ![Selecionar Aplicação Web][cache-select-web-app]
4. Clique em **Validar Ligação** para verificar as suas definições e, em seguida, clique em **Publicar**.
   
    ![Publicar][cache-publish]
   
    O processo de publicação será concluído após alguns instantes e um browser será iniciado com a aplicação de exemplo em execução. Se for apresentado um erro de DNS aquando da validação ou da publicação e se o processo de aprovisionamento dos recursos do Azure da aplicação tiver sido concluído recentemente, aguarde alguns instantes e tente novamente.
   
    ![Cache adicionada][cache-added-to-application]

A tabela seguinte descreve cada ligação de ação da aplicação de exemplo.

| Ação | Descrição |
| --- | --- |
| Criar Nova |Crie uma nova Equipa. |
| Reproduzir Época |Reproduza uma época de jogos, atualize as estatísticas da equipa e limpe quaisquer dados da equipa desatualizados da cache. |
| Limpar Cache |Limpe as estatísticas da equipa na cache. |
| Lista na Cache |Obtenha as estatísticas da equipa a partir da cache. Se existir uma falha de acerto na cache, carregue as estatísticas da base de dados e guarde-as na cache para a próxima utilização. |
| Conjunto Ordenado na Cache |Obtenha as estatísticas da equipa a partir da cache com um conjunto ordenado. Se existir uma falha de acerto na cache, carregue as estatísticas da base de dados e guarde-as na cache utilizando um conjunto ordenado. |
| Cinco Melhores Equipas na Cache |Obtenha as cinco melhores equipas a partir da cache com um conjunto ordenado. Se existir uma falha de acerto na cache, carregue as estatísticas da base de dados e guarde-as na cache utilizando um conjunto ordenado. |
| Carregar da Base de Dados |Obtenha as estatísticas da equipa a partir da base de dados. |
| Recriar Base de Dados |Recrie a base de dados e volte a carregá-la com os dados da equipa de exemplo. |
| Editar/Detalhes/Eliminar |Edite uma equipa, veja os detalhes de uma equipa, elimine uma equipa. |

Clique em algumas das ações e experimente obter os dados a partir de diferentes fontes. Repare nas diferenças existentes no tempo de obtenção dos dados a partir da base de dados e da cache.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Eliminar os recursos quando terminar a aplicação
Quando tiver terminado o tutorial da aplicação de exemplo, poderá eliminar os recursos do Azure utilizados para conservar os custos e os recursos. Se utilizar o botão **Implementar no Azure** na secção [Aprovisionar os recursos do Azure](#provision-the-azure-resources) e se todos os seus recursos estiverem no mesmo grupo de recursos, poderá eliminá-los numa única operação ao eliminar o grupo de recursos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.
2. Escreva o nome do seu grupo de recursos na caixa de texto **Filtrar itens…**
3. Clique em **...** à direita do grupo de recursos.
4. Clique em **Eliminar**.
   
    ![Eliminar][cache-delete-resource-group]
5. Escreva o nome do grupo de recursos e clique em **Eliminar**.
   
    ![Confirmar eliminação][cache-delete-confirm]

Após alguns instantes, o grupo de recursos e todos os recursos nele contidos serão eliminados.

> [!IMPORTANT]
> Tenha em atenção que a eliminação de um grupo de recursos é irreversível e que o grupo de recursos e todos os recursos nele contidos serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente, pode eliminar cada recurso individualmente nos respetivos painéis.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Executar a aplicação de exemplo no computador local
Para executar a aplicação localmente no computador, precisa de uma instância da Cache de Redis do Azure para colocar os dados em cache. 

* Se tiver publicado a aplicação no Azure conforme descrito na secção anterior, pode utilizar a instância da Cache de Redis do Azure aprovisionada durante esse passo.
* Se tiver outra instância da Cache de Redis do Azure, poderá utilizá-la para executar este exemplo localmente.
* Se tiver de criar uma instância da Cache de Redis do Azure, pode seguir os passos descritos em [Criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Assim que tiver selecionado ou criado a cache a utilizar, navegue para a cache no Portal do Azure e obtenha o [nome de anfitrião](cache-configure.md#properties) e as [chaves de acesso](cache-configure.md#access-keys) para a sua cache. Para obter as instruções, veja [Configure Redis cache settings (Configurar as definições da cache de Redis)](cache-configure.md#configure-redis-cache-settings).

1. Abra o ficheiro `WebAppPlusCacheAppSecrets.config` que configurou durante o passo [Configurar a aplicação para utilizar a Cache de Redis](#configure-the-application-to-use-redis-cache) deste tutorial com um editor à sua escolha.
2. Edite o atributo `value` e substitua `MyCache.redis.cache.windows.net` pelo [nome de anfitrião](cache-configure.md#properties) da cache e especifique a [chave primária ou secundária](cache-configure.md#access-keys) da cache, como a palavra-passe.

        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


1. Prima **Ctrl+F5** para executar a aplicação.

> [!NOTE]
> Tenha em atenção que, uma vez que a aplicação, incluindo a base de dados, é executada localmente e a Cache de Redis está alojada no Azure, a cache poderá apresentar um desempenho inferior da base de dados. Para um melhor desempenho, a aplicação cliente e a instância da Cache de Redis do Azure devem estar na mesma localização. 
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações, veja [Getting Started with ASP.NET MVC 5 (Introdução ao ASP.NET MVC 5)](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) no site [ASP.NET](http://asp.net/).
* Para obter mais exemplos de criação de uma Aplicação Web ASP.NET no Serviço de Aplicações, veja [Create and deploy an ASP.NET web app in Azure App Service (Criar e implementar uma aplicação Web ASP.NET no Serviço de Aplicações do Azure)](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) da [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect.
  * Para obter mais inícios rápidos da demonstração de HealthClinic.biz, veja [Azure Developer Tools Quickstarts (Inícios Rápidos de Ferramentas de Programador do Azure)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
* Saiba mais sobre a abordagem [Code first to a new database (Primeiro código para uma nova base de dados)](https://msdn.microsoft.com/data/jj193542) para o Entity Framework utilizado neste tutorial.
* Saiba mais sobre as [Aplicações Web do App Service do Azure](../app-service-web/app-service-web-overview.md).
* Saiba como [monitorizar](cache-how-to-monitor.md) a sua cache no Portal do Azure.
* Explorar as funcionalidades da Cache de Redis do Azure Premium
  
  * [Como configurar a persistência para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md)
  * [Como configurar o clustering para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md)
  * [Como configurar o suporte da Rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md)
  * Veja as [Azure Redis Cache FAQ (FAQ sobre a Cache de Redis do Azure)](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, o débito e a largura de banda com caches Premium.

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png




<!--HONumber=Nov16_HO2-->


