---
title: Estado da sessão com a cache de Redis do Azure no App Service do Azure
description: Saiba como utilizar o Serviço Azure Cache para suportar a colocação em cache do estado de sessão do ASP.NET.
services: app-service\web
documentationcenter: .net
author: Rick-Anderson
manager: wpickett
editor: none

ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: riande

---
# Estado da sessão com a cache de Redis do Azure no App Service do Azure
Este tópico explica como utilizar o Serviço Cache de Redis do Azure para o estado da sessão.

Se a sua aplicação Web do ASP.NET utiliza o estado de sessão, terá de configurar um fornecedor de estado de sessão externo (o Serviço Cache de Redis ou um fornecedor de estado de sessão do SQL Server). Se utilizar o estado de sessão e não utilizar um fornecedor externo, vai estar limitado a uma instância da sua aplicação Web. O Serviço Cache de Redis é o mais rápido e mais simples para ativar.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a id="createcache"></a>Criar a Cache
Siga [estas indicações](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para criar a cache.

## <a id="configureproject"></a>Adicionar o pacote RedisSessionStateProvider NuGet à sua aplicação Web
Instale o pacote NuGet `RedisSessionStateProvider`.  Utilize o seguinte comando para instalar a partir da consola do gestor de pacotes (**Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`

Para instalar a partir de **Ferramentas** > **Gestor de Pacotes NuGet** > **Gerir Pacotes NugGet para a Solução**, pesquise `RedisSessionStateProvider`.

Para mais informações, consulte a [página NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) e [Configurar o cliente de cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

## <a id="configurewebconfig"></a>Modificar o ficheiro Web.Config
Para além de fazer referências de assemblagem para a Cache, o pacote NuGet adiciona entradas stub ao ficheiro *web.config*. 

1. Abra o *web.config* e localize o elemento **sessionState**.
2. Introduza os valores para `host`, `accessKey`, `port` (a porta SSL deve ser 6380) e defina `SSL` para `true`. Estes valores podem ser obtidos a partir do painel do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para a instância da cache. Para obter mais informações, consulte [Ligar à cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Tenha em atenção que a porta não SSL está desativada por predefinição para novas caches. Para obter mais informações sobre como ativar a porta não SSL, consulte a secção [Portas de Acesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) no tópico [Configurar uma cache na Cache de Redis do Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). A marcação seguinte mostra as alterações ao ficheiro *web.config*, especificamente, as alterações à *porta*, *anfitrião*, accessKey* e *ssl*.
   
          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;

## <a id="usesessionobject"></a> Utilizar o Objeto de Sessão no Código
O passo final consiste em começar a utilizar o Objeto de Sessão no seu código ASP.NET. Adicione objetos ao estado de sessão através do método **Session.Add**. Este método utiliza pares chave-valor para armazenar itens na cache do estado de sessão.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

O código seguinte obtém este valor do estado de sessão.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

Também pode utilizar a Cache de Redis para colocar objetos na cache na sua aplicação Web. Para obter mais informações, consulte [Aplicação de filmes do MVC com a Cache de Redis do Azure em 15 minutos](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para obter mais detalhes sobre como utilizar o estado de sessão do ASP.NET, consulte o artigo [Descrição Geral do Estado de Sessão do ASP.NET][Descrição Geral do Estado de Sessão do ASP.NET].

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

## O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte: [App Service do Azure e o Respetivo Impacto nos Serviços do Azure Existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
  
  *De [Rick Anderson](https://twitter.com/RickAndMSFT)*

[instalou a versão mais recente]: http://www.windowsazure.com/downloads/?sdk=net  
[Descrição Geral do Estado de Sessão do ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

[NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
[NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
[CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
[NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
[OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
[CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
[EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
[ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png




<!--HONumber=Aug16_HO1-->


