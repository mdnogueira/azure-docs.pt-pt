---
title: "Gerir a expiração de conteúdo web no Azure CDN | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c207d780857a61d4b1fc0f39e6185cae67abc955
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Gerir a expiração de conteúdo de serviços de aplicações/nuvem de Web do Azure, o ASP.NET nem o IIS na CDN do Azure
> [!div class="op_single_selector"]
> * [Serviços de aplicações/nuvem de Web do Azure, o ASP.NET nem o IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Serviço de blob de armazenamento do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

Ficheiros a partir de qualquer servidor de web de origem acessível publicamente podem ser colocadas em cache na CDN do Azure depois de decorrido o time-to-live (TTL).  O valor de TTL é determinado pelo [ *Cache-Control* cabeçalho](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP do servidor de origem.  Este artigo descreve como definir `Cache-Control` cabeçalhos para Web Apps do Azure, Cloud Services do Azure, as aplicações ASP.NET e sites de serviços de informação de Internet, todos os que estão configurados da mesma forma.

> [!TIP]
> Pode optar por não definir nenhum valor de TTL num ficheiro.  Neste caso, o Azure CDN aplica automaticamente uma predefinição TTL de sete dias.
> 
> Para obter mais informações sobre como funciona o CDN do Azure para acelerar o acesso a ficheiros e outros recursos, consulte o [descrição geral da CDN do Azure](cdn-overview.md).
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>Definir cabeçalhos Cache-Control na configuração
Para o conteúdo estático, como imagens e folhas de estilo, pode controlar a frequência de atualização ao modificar o **applicationHost. config** ou **Web. config** ficheiros para a sua aplicação web.  O **system.webServer\staticContent\clientCache** elemento no ficheiro de configuração irá definir o `Cache-Control` cabeçalho para o seu conteúdo. Para **Web. config**, as definições de configuração afetará tudo na pasta e todas as subpastas, a menos que substituída ao nível da subpasta.  Por exemplo, pode predefinir uma time-to-live na raiz para que todo o conteúdo estático colocados em cache para 3 dias, mas tem uma subpasta que possui conteúdo mais variável com uma definição de cache de 6 horas.  Para **applicationHost. config**, todas as aplicações no site vai ser afetadas, mas podem ser substituídas na **Web. config** ficheiros nas aplicações.

O XML seguinte mostra um exemplo de definição **clientCache** para especificar uma duração máxima de 3 dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Especificar **UseMaxAge** adiciona um `Cache-Control: max-age=<nnn>` cabeçalho de resposta com base no valor especificado no **CacheControlMaxAge** atributo. O formato de timespan destina-se a **cacheControlMaxAge** atributo <days>.<hours>:<min>:<sec>. Para mais informações sobre o **clientCache** nós, consulte [Cache do cliente <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Definir cabeçalhos Cache-Control no código
Para aplicações ASP.NET, pode definir a CDN permitir a colocação em forma programática definindo a **HttpResponse.Cache** propriedade. Para mais informações sobre o **HttpResponse.Cache** propriedade, consulte [HttpResponse.Cache propriedade](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [HttpCachePolicy classe](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Se pretender através de programação aplicação colocar conteúdos em cache no ASP.NET, certifique-se de que o conteúdo está marcado como colocáveis definindo HttpCacheability *pública*. Além disso, certifique-se de que a validação de uma cache está definida. A validação da cache pode ser uma última modificação timestamp definir chamando SetLastModified ou um valor etag definir chamando SetETag. Opcionalmente, também pode especificar uma hora de expiração de cache ao chamar SetExpires ou pode depender heurística de cache predefinido descrita anteriormente neste documento.  

Por exemplo, a cache de conteúdo para uma hora e adicione o seguinte:  

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

