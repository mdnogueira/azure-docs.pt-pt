---
title: "Gerir a expiração de conteúdo web numa rede de entrega de conteúdo do Azure | Microsoft Docs"
description: "Saiba como gerir a expiração de conteúdo de serviços de aplicações/nuvem de Web do Azure, o ASP.NET nem o IIS na CDN do Azure."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: fe519c3ad5f99899277bf005929142c52a4c4724
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Gerir a expiração de conteúdo web numa rede de entrega de conteúdo do Azure
> [!div class="op_single_selector"]
> * [Conteúdo da web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Os ficheiros a partir de qualquer servidor de web de origem acessível publicamente podem ser colocadas em cache na rede de entrega de conteúdos (CDN) do Azure até as respetivas time-to-live (TTL) decorrida. O valor de TTL é determinado pelo `Cache-Control` cabeçalho de resposta HTTP do servidor de origem. Este artigo descreve como definir `Cache-Control` cabeçalhos para a funcionalidade Web Apps do App Service do Microsoft Azure, Cloud Services do Azure, as aplicações ASP.NET e sites de serviços de informação Internet (IIS), todos os que estão configurados da mesma forma. Pode definir o `Cache-Control` cabeçalho utilizando ficheiros de configuração ou através de programação.

> [!TIP]
> Pode optar por não definir nenhum valor de TTL num ficheiro. Neste caso, o Azure CDN aplica automaticamente uma predefinição TTL de sete dias. Esta predefinição TTL só se aplica a otimizações de entrega web geral. Para otimizações de ficheiros grandes, o TTL predefinido é um dia e para o suporte de dados otimizações de transmissão em fluxo, o TTL predefinido é de um ano.
> 
> Para obter mais informações sobre como funciona o CDN do Azure para acelerar o acesso a ficheiros e outros recursos, consulte [descrição geral da rede de entrega de conteúdos de Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Definir cabeçalhos Cache-Control utilizando ficheiros de configuração
Para o conteúdo estático, como imagens e folhas de estilo, pode controlar a frequência de atualização ao modificar o **applicationHost. config** ou **Web. config** ficheiros de configuração da sua aplicação web. O `<system.webServer>/<staticContent>/<clientCache>` elemento em qualquer um dos conjuntos de ficheiros a `Cache-Control` cabeçalho para o seu conteúdo.

### <a name="using-applicationhostconfig-files"></a>A utilizar ficheiros de no applicationHost. config
O **applicationHost. config** ficheiro é o ficheiro de raiz do sistema de configuração do IIS. As definições de configuração num **applicationHost. config** ficheiro afetar todas as aplicações no site, mas são substituídas por definições de qualquer **Web. config** ficheiros existentes para uma aplicação web.

### <a name="using-webconfig-files"></a>Utilizar ficheiros Web. config
Com um **Web. config** ficheiro, pode personalizar a forma como se comporta a sua aplicação web todo ou um diretório específico na sua aplicação web. Normalmente, tem, pelo menos, um **Web. config** ficheiro na pasta raiz da aplicação web. Para cada **Web. config** ficheiros numa pasta específica, as definições de configuração afetam a tudo de nessa pasta e respetivas subpastas, a menos que estes são substituídos ao nível da subpasta por outro **Web. config**ficheiro. Por exemplo, pode definir um `<clientCache>` elemento um **Web. config** ficheiro na pasta raiz da aplicação web para colocar em cache todo o conteúdo estático na sua aplicação web de três dias. Também pode adicionar um **Web. config** ficheiro numa subpasta com o conteúdo mais variável (por exemplo, `\frequent`) e defina o `<clientCache>` elemento para colocar em cache o conteúdo a subpasta para seis horas. O resultado net é esse conteúdo em toda a web site serão colocadas em cache para três dias, exceto para qualquer conteúdo a `\frequent` diretório, que serão colocadas em cache para apenas seis horas.  

O exemplo XML seguinte mostra como definir o `<clientCache>` elemento num ficheiro de configuração para especificar uma duração máxima de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Para utilizar o **cacheControlMaxAge** atributo, tem de definir o valor da **cacheControlMode** atributo para `UseMaxAge`. Esta definição causou o cabeçalho HTTP e a diretiva, `Cache-Control: max-age=<nnn>`, para ser adicionado à resposta. O formato do valor timespan para o **cacheControlMaxAge** atributo `<days>.<hours>:<min>:<sec>`. O valor é convertido em segundos e é utilizado como o valor da `Cache-Control` `max-age` diretiva. Para obter mais informações sobre o `<clientCache>` elemento, consulte [Cache do cliente <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Definir cabeçalhos Cache-Control através de programação
Para aplicações ASP.NET, pode controlar a CDN permitir a colocação em forma programática definindo a **HttpResponse.Cache** propriedade da .NET API. Para obter informações sobre o **HttpResponse.Cache** propriedade, consulte [HttpResponse.Cache propriedade](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [HttpCachePolicy classe](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Para através de programação aplicação conteúdo em cache no ASP.NET, siga estes passos:
   1. Certifique-se de que o conteúdo está marcado como colocáveis definindo `HttpCacheability` para `Public`. 
   2. Definir um validador cache chamando um dos seguintes `HttpCachePolicy` métodos:
      - Chamar `SetLastModified` para definir um valor de carimbo para o `Last-Modified` cabeçalho.
      - Chamar `SetETag` para definir um valor para o `ETag` cabeçalho.
   3. Opcionalmente, especifique uma hora de expiração de cache ao chamar `SetExpires` para definir um valor para o `Expires` cabeçalho. Caso contrário, a heurística de cache predefinido descrita anteriormente neste documento aplicam-se.

Por exemplo, a cache de conteúdo para uma hora e adicione o seguinte código c#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>O cabeçalho de Cache-Control de teste
Pode facilmente verificar as definições de TTL do seu conteúdo web. Com o seu browser [ferramentas de programador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), teste o conteúdo da web inclui o `Cache-Control` cabeçalho de resposta. Também pode utilizar uma ferramenta como **wget**, [Postman](https://www.getpostman.com/), ou [Fiddler](http://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Passos Seguintes
* [Lê os detalhes sobre o **clientCache** elemento](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação para o **HttpResponse.Cache** propriedade](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Leia a documentação para o **HttpCachePolicy classe**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

