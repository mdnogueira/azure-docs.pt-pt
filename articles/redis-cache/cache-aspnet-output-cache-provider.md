---
title: "Fornecedor de cache de Cache de saída do ASP.NET"
description: "Saiba como colocar em cache de saída de página do ASP.NET com a Cache de Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
ms.openlocfilehash: 845f25637a0e48460fc76c1ee36060274b3cec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Fornecedor de Cache de saída do ASP.NET para o Azure Redis Cache
O fornecedor de Cache de saída Redis é um mecanismo de armazenamento de fora do processo para dados da cache de saída. Estes dados são especificamente para as respostas HTTP completas (página a cache de saída). O fornecedor plugs para o novo saída cache fornecedor extensibilidade ponto que foi introduzido no ASP.NET 4.

Para utilizar o fornecedor de Cache de saída Redis, primeiro de configurar a cache e, em seguida, configure a sua aplicação ASP.NET com o pacote Redis NuGet de fornecedor de Cache de saída. Este tópico fornece orientações sobre como configurar a sua aplicação para utilizar o fornecedor de Cache de saída Redis. Para obter mais informações sobre como criar e configurar uma instância da Cache de Redis do Azure, consulte [criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar a saída de páginas do ASP.NET na cache
Para configurar uma aplicação de cliente no Visual Studio com o pacote NuGet de estado de sessão Cache de Redis, clique em **Gestor de pacotes NuGet**, **consola do Gestor de pacotes** do **ferramentas** menu.

Execute o seguinte comando a partir da janela `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

O pacote Redis NuGet de fornecedor de Cache de saída tem uma dependência no pacote StrongName. Se o pacote de StrongName não está presente no projeto, está instalado. Para obter mais informações sobre o pacote de Redis NuGet de fornecedor de Cache de saída, consulte o [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) página NuGet.

>[!NOTE]
>Para além do pacote de StrongName com nome seguro, há também a versão do stackexchange. redis não-com nome seguro. Caso contrário, se o projeto estiver a utilizar a versão stackexchange. redis não-com nome seguro, que tem de o desinstalar, obter nomenclatura conflitos no seu projeto. Para obter mais informações sobre estes pacotes, consulte [clientes de cache de .NET configurar](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias e adiciona a secção seguinte para o ficheiro Web. config. Esta secção contém a configuração necessária para a sua aplicação ASP.NET utilizar o fornecedor de Cache de saída Redis.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

A secção Comentada fornece um exemplo dos atributos e as definições de exemplo para cada atributo.

Configure os atributos com os valores do painel da cache no portal do Microsoft Azure e os outros valores conforme pretendido. Para obter instruções sobre como aceder as propriedades da cache, consulte [Configure Redis cache as definições](cache-configure.md#configure-redis-cache-settings).

* **anfitrião** – especifique o ponto final da cache.
* **porta** – utilizar a porta não SSL ou a porta SSL, dependendo das definições de ssl.
* **accessKey** – utilizar a chave primária ou secundária para a sua cache.
* **SSL** – VERDADEIRO se quiser proteger as comunicações de cliente de cache/com ssl; caso contrário FALSO. Lembre-se de que especifica a porta correta.
  * Por predefinição, a porta não SSL está desativada para as novas caches. Especifica verdadeiro para esta definição utilizar a porta SSL. Para obter mais informações sobre como ativar a porta não SSL, consulte o [portas de acesso](cache-configure.md#access-ports) secção o [configurar uma cache](cache-configure.md) tópico.
* **databaseId** – especificado base de dados a utilizar para a cache de saída de dados. Se não for especificado, é utilizado o valor predefinido de 0.
* **applicationName** – chaves são armazenadas no redis como `<AppName>_<SessionId>_Data`. Este esquema de nomenclatura permite que várias aplicações partilhem a mesma chave. Este parâmetro é opcional e se não o a fornecer um valor predefinido é utilizado.
* **connectionTimeoutInMilliseconds** – esta definição permite-lhe ignorar a definição de connectTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de connectTimeout de 5000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – esta definição permite-lhe ignorar a definição de syncTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de syncTimeout de 1000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Adicione uma diretiva OutputCache para cada página para o qual pretende colocar em cache de saída.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No anterior exemplo, os dados de página em cache permanecem na cache durante 60 segundos e uma versão diferente da página é colocado em cache para cada combinação de parâmetro. Para mais informações sobre a diretiva OutputCache, consulte [ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837).

Assim que estes passos são efetuados, a aplicação está configurada para utilizar o fornecedor de Cache de saída Redis.

## <a name="next-steps"></a>Passos seguintes
Veja o [fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure](cache-aspnet-session-state-provider.md).

