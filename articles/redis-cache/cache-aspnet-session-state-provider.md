---
title: "Fornecedor de estado da sessão ASP.NET cache | Microsoft Docs"
description: "Saiba como armazenar o estado de sessão do ASP.NET com a Cache de Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: sdanie
ms.openlocfilehash: 0f3683939ac9646565a0669e19b4c82811d621fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Fornecedor de Estado da Sessão ASP.NET para a Cache de Redis do Azure
Cache de Redis do Azure fornece um fornecedor de estado de sessão que pode utilizar para armazenar o estado da sessão cache em vez de na memória ou numa base de dados do SQL Server. Para utilizar o colocação em cache no fornecedor de estado de sessão, primeiro de configurar a cache e, em seguida, configure a sua aplicação ASP.NET para cache com o pacote NuGet de estado de sessão Cache de Redis.

Muitas vezes, não é prático numa aplicação de nuvem do mundo real, para evitar a armazenar alguma forma de estado para uma sessão de utilizador, mas alguns abordagens impacto no desempenho e escalabilidade mais do que outras pessoas. Se tiver de armazenar o estado, é a melhor solução manter a quantidade de estado pequeno e armazená-las em cookies. Se que não é exequível, é a melhor solução seguinte utilizar o estado da sessão ASP.NET com um fornecedor de cache distribuída, dentro da memória. A solução pior de um desempenho e o ponto de vista da escalabilidade está a utilizar uma base de dados de fornecedor de estado de sessão de segurança. Este tópico fornece orientações sobre como utilizar o fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure. Para obter informações sobre outras opções de estado de sessão, consulte [opções de estado da sessão ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão do ASP.NET na cache
Para configurar uma aplicação de cliente no Visual Studio com o pacote NuGet de estado de sessão Cache de Redis, clique em **Gestor de pacotes NuGet**, **consola do Gestor de pacotes** do **ferramentas** menu.

Execute o seguinte comando a partir da janela `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Se estiver a utilizar a funcionalidade de clustering do escalão premium, tem de utilizar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou de uma exceção é emitida. Mover para 2.0.1 ou superior é uma alteração inovadora; Para obter mais informações, consulte [v2.0.0 interrompendo detalhes de alteração](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). No momento da atualização deste artigo, a versão atual deste pacote é 2.2.3.
> 
> 

O pacote NuGet de fornecedor do Estado de sessão de Redis tem uma dependência no pacote StrongName. Se o pacote de StrongName não está presente no projeto, está instalado.

>[!NOTE]
>Para além do pacote de StrongName com nome seguro, há também a versão do stackexchange. redis não-com nome seguro. Caso contrário, se o projeto estiver a utilizar a versão stackexchange. redis não-com nome seguro, que tem de o desinstalar, obter nomenclatura conflitos no seu projeto. Para obter mais informações sobre estes pacotes, consulte [clientes de cache de .NET configurar](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias e adiciona a secção seguinte para o ficheiro Web. config. Esta secção contém a configuração necessária para a sua aplicação ASP.NET utilizar o fornecedor estado da sessão de Cache de Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

A secção Comentada fornece um exemplo dos atributos e as definições de exemplo para cada atributo.

Configure os atributos com os valores do painel da cache no portal do Microsoft Azure e os outros valores conforme pretendido. Para obter instruções sobre como aceder as propriedades da cache, consulte [Configure Redis cache as definições](cache-configure.md#configure-redis-cache-settings).

* **anfitrião** – especifique o ponto final da cache.
* **porta** – utilizar a porta não SSL ou a porta SSL, dependendo das definições de ssl.
* **accessKey** – utilizar a chave primária ou secundária para a sua cache.
* **SSL** – VERDADEIRO se quiser proteger as comunicações de cliente de cache/com ssl; caso contrário FALSO. Lembre-se de que especifica a porta correta.
  * Por predefinição, a porta não SSL está desativada para as novas caches. Especifica verdadeiro para esta definição utilizar a porta SSL. Para obter mais informações sobre como ativar a porta não SSL, consulte o [portas de acesso](cache-configure.md#access-ports) secção o [configurar uma cache](cache-configure.md) tópico.
* **throwOnError** – VERDADEIRO se quiser uma exceção ao ser emitida se houver uma falha, ou FALSO se pretende que a operação falhar de forma silenciosa. Pode verificar a existência de uma falha ao verificar a propriedade Microsoft.Web.Redis.RedisSessionStateProvider.LastException estática. A predefinição é verdadeira.
* **retryTimeoutInMilliseconds** – operações que não são repetidas durante este intervalo, especificado em milissegundos. A primeira tentativa ocorre após 20 milissegundos e, em seguida, as repetições ocorrem a cada segundo até que o intervalo de retryTimeoutInMilliseconds expira. Imediatamente após este intervalo, a operação é repetida uma vez final. Se a operação continuar a falhar, a exceção é acionada novamente para o autor da chamada, consoante a definição de throwOnError. O valor predefinido é 0, o que não significa nenhuma tentativas.
* **databaseId** – Especifica a base de dados a utilizar para dados de saída da cache. Se não for especificado, é utilizado o valor predefinido de 0.
* **applicationName** – chaves são armazenadas no redis como `{<Application Name>_<Session ID>}_Data`. Este esquema de nomenclatura permite que várias aplicações partilham a mesma instância de Redis. Este parâmetro é opcional e se não o a fornecer um valor predefinido é utilizado.
* **connectionTimeoutInMilliseconds** – esta definição permite-lhe ignorar a definição de connectTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de connectTimeout de 5000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – esta definição permite-lhe ignorar a definição de syncTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de syncTimeout de 1000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Para obter mais informações sobre estas propriedades, consulte o anúncio de mensagem de blogue original em [anunciar ASP.NET sessão fornecedor de estado para Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Não se esqueça de remover o comentário secção de fornecedor de estado de sessão de padrão InProc no seu Web. config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Assim que estes passos são efetuados, a aplicação está configurada para utilizar o fornecedor estado da sessão de Cache de Redis. Quando utilizar o estado da sessão na sua aplicação, é armazenada numa instância da Cache de Redis do Azure.

> [!IMPORTANT]
> Os dados armazenados na cache tem de ser serializáveis, ao contrário dos dados que podem ser armazenados no predefinido fornecedor de estado de sessão do ASP.NET na memória. Quando é utilizado o fornecedor de estado de sessão de Redis, lembre-se de que os tipos de dados que estão a ser armazenados no estado da sessão são serializáveis.
> 
> 

## <a name="aspnet-session-state-options"></a>Opções de estado de sessão do ASP.NET
* No fornecedor de estado da sessão de memória - este fornecedor armazena o estado da sessão na memória. A vantagem de utilizar este fornecedor é é simples e rápida. No entanto não pode dimensionar as suas aplicações Web se estiver a utilizar no fornecedor de memória, uma vez que não é distribuída.
* Fornecedor de estado da sessão do SQL Server - este fornecedor armazena o estado da sessão no Sql Server. Utilize este fornecedor, se pretender armazenar o estado da sessão no armazenamento persistente. Pode dimensionar a sua aplicação Web, mas utilizar o Sql Server para a sessão tem um impacto no desempenho na sua aplicação Web.
* Distribuída na memória sessão fornecedor de estado como Redis Cache sessão fornecedor de estado - este fornecedor dá-lhe o melhor de dois mundos. A aplicação Web pode ter um fornecedor de estado de sessão simples, rápidas e dimensionáveis. Porque este fornecedor armazena o estado da sessão, cache, a aplicação tem de ter em consideração todas as características associadas quando se fala para uma Cache distribuída na memória, tais como falhas de rede transitórios. Para melhores práticas em Cache a utilizar, consulte [orientações sobre a colocação em cache](../best-practices-caching.md) da Microsoft Patterns & práticas [Design da aplicação em nuvem do Azure e as diretrizes de implementação](https://github.com/mspnp/azure-guidance).

Para obter mais informações sobre o estado da sessão e outras melhores práticas, consulte [Web desenvolvimento melhores práticas (compilar aplicações de nuvem de mundo Real com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Passos seguintes
Veja o [fornecedor de Cache de saída do ASP.NET para a Cache de Redis do Azure](cache-aspnet-output-cache-provider.md).

