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
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Gerir a expiração de conteúdo web numa rede de entrega de conteúdo do Azure
 na CDN do Azure
> [!div class="op_single_selector"]
> * [Serviços de aplicações/nuvem de Web do Azure, o ASP.NET nem o IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Ficheiros a partir de qualquer servidor de web de origem acessível publicamente podem ser colocadas em cache na rede de entrega de conteúdos (CDN) do Azure até as respetivas time-to-live (TTL) decorrida. O valor de TTL é determinado pelo [ `Cache-Control` cabeçalho](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP do servidor de origem. Este artigo descreve como definir `Cache-Control` cabeçalhos para a funcionalidade Web Apps do App Service do Microsoft Azure, Cloud Services do Azure, as aplicações ASP.NET e sites de serviços de informação de Internet, todos os que estão configurados da mesma forma.

> [!TIP]
> Pode optar por não definir nenhum valor de TTL num ficheiro. Neste caso, o Azure CDN aplica automaticamente uma predefinição TTL de sete dias.
> 
> Para obter mais informações sobre como funciona o CDN do Azure para acelerar o acesso a ficheiros e outros recursos, consulte [descrição geral da rede de entrega de conteúdos de Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>Definir cabeçalhos Cache-Control nos ficheiros de configuração
Para o conteúdo estático, como imagens e folhas de estilo, pode controlar a frequência de atualização ao modificar o **applicationHost. config** ou **Web. config** ficheiros para a sua aplicação web. O **system.webServer\staticContent\clientCache** elemento os conjuntos de ficheiros de configuração a `Cache-Control` cabeçalho para o seu conteúdo. Para **Web. config** ficheiros, as definições de configuração afetam tudo na pasta e respetivas subpastas, a menos que estes são substituídos ao nível da subpasta. Por exemplo, pode configurar uma definição de TTL de predefinição na pasta raiz para colocar em cache todo o conteúdo estático de três dias e defina uma subpasta com mais variável conteúdo em cache o conteúdo apenas seis horas. Embora **applicationHost. config** definições ficheiros afetam todas as aplicações no site, são substituídas por definições de qualquer existente **Web. config** ficheiros nas aplicações.

O exemplo XML seguinte mostra como definir **clientCache** para especificar uma duração máxima de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Especificar **UseMaxAge** faz com que um `Cache-Control: max-age=<nnn>` cabeçalho para ser adicionado à resposta com base no valor especificado no **CacheControlMaxAge** atributo. O formato do período de tempo para o **cacheControlMaxAge** atributo `<days>.<hours>:<min>:<sec>`. Para obter mais informações sobre o **clientCache** nós, consulte [Cache do cliente <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Definir cabeçalhos Cache-Control no código
Para aplicações ASP.NET, pode controlar a CDN permitir a colocação em forma programática definindo a **HttpResponse.Cache** propriedade. Para obter mais informações sobre o **HttpResponse.Cache** propriedade, consulte [HttpResponse.Cache propriedade](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [HttpCachePolicy classe](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Para através de programação aplicação conteúdo em cache no ASP.NET, siga estes passos:
   1. Certifique-se de que o conteúdo está marcado como colocáveis definindo `HttpCacheability` para *pública*. 
   2. Defina um validador cache chamando um dos seguintes métodos:
      - Chamar `SetLastModified` para definir um timestamp LastModified.
      - Chamar `SetETag` para definir um `ETag` valor.
   3. Opcionalmente, especifique uma hora de expiração de cache ao chamar `SetExpires`. Caso contrário, a heurística de cache predefinido descrita anteriormente neste documento aplicam-se.

Por exemplo, a cache de conteúdo para uma hora e adicione o seguinte código c#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Passos Seguintes
* [Lê os detalhes sobre o **clientCache** elemento](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação para o **HttpResponse.Cache** propriedade](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Leia a documentação para o **HttpCachePolicy classe**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

