---
title: "Cliente e o servidor SDK controlo de versões em aplicações móveis e dos Mobile Services | Microsoft Docs"
description: "Lista de SDKs de cliente e a compatibilidade com versões do SDK do Mobile Services e as Mobile Apps do Azure"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: f79e819b1547f81498ea213858faf3c75e374782
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controlo de versões de cliente e servidor de aplicações móveis e dos Mobile Services
A versão mais recente dos Mobile Services do Azure é a **Mobile Apps** funcionalidade do App Service do Azure.

Os SDKs de cliente e servidor de aplicações móveis são originalmente com base nos Mobile Services, mas são *não* compatíveis entre si.
Ou seja, tem de utilizar um *Mobile Apps* SDK cliente com um *Mobile Apps* SDK do servidor e da mesma forma para *Mobile Services*. Este contrato é imposto através de um valor de cabeçalho especial utilizado pelo cliente e servidor SDKs, `ZUMO-API-VERSION`.

Nota: sempre que este documento refere-se a um *Mobile Services* back-end, não necessariamente necessita ser alojada numa Mobile Services. Agora é possível migrar um serviço móvel para ser executada no serviço de aplicações sem quaisquer alterações de código, mas o serviço ainda estariam a utilizar *Mobile Services* versões do SDK.

Para obter mais informações sobre migrar para o serviço de aplicações sem quaisquer alterações de código, consulte o artigo [migrar um serviço móvel para o App Service do Azure].

## <a name="header-specification"></a>Especificação de cabeçalho
A chave `ZUMO-API-VERSION` pode ser especificado no cabeçalho de HTTP ou a cadeia de consulta. O valor é uma cadeia de versão no formato **x.y.z**.

Por exemplo:

OBTER https://service.azurewebsites.net/tables/TodoItem

CABEÇALHOS: ZUMO-API-VERSION: 2.0.0

PUBLIQUE https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Aceitar fora de verificação da versão
Pode ativamente a verificação, definindo um valor de versão **verdadeiro** para a definição de aplicação **MS_SkipVersionCheck**. Especifique isto no seu Web. config ou na secção definições da aplicação do portal do Azure.

> [!NOTE]
> Existem várias das alterações de comportamento entre os Mobile Services e as Mobile Apps, especialmente nas áreas de sincronização offline, a autenticação e notificações push. Só deve ativamente versão verificação depois de concluir o teste para se certificar de que estas alterações comportamentais não interromper a funcionalidade da aplicação.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Resumo de compatibilidade para todas as versões
A tabela abaixo mostra a compatibilidade entre todos os tipos de cliente e servidor. Um back-end é classificado como qualquer uma das Mobile **serviços** ou Mobile **aplicações** com base no servidor de SDK que utiliza.

|  | **Serviços móveis** Node.js ou .NET | **As aplicações móveis** Node.js ou .NET |
| --- | --- | --- |
| [Clientes de Mobile Services] |OK |Erro\* |
| [Clientes de aplicações móveis] |Erro\* |OK |

\*Isto pode ser controlado através da especificação **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Cliente de Mobile Services e o servidor
O SDKs do cliente na tabela abaixo são compatíveis com **Mobile Services**.

Nota: o cliente de Mobile Services SDKs *não* enviar um valor de cabeçalho `ZUMO-API-VERSION`. Se o serviço receber este cabeçalho ou o valor da cadeia de consulta, será devolvido um erro, a menos que explicitamente optou por terminar, tal como descrito acima.

### <a name="MobileServicesClients"></a>Mobile *serviços* SDKs de cliente
| Plataforma de cliente | Versão | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerido (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |n/d |
| iOS |[2.2.2](http://aka.ms/gc6fex) |n/d |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |n/d |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |n/d |

### <a name="mobile-services-server-sdks"></a>Mobile *serviços* SDKs do servidor
| Plataforma de servidor | Versão | Cabeçalho de versão aceite |
| --- | --- | --- |
| .NET |[Versão WindowsAzure.MobileServices.Backend.* 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |* * Nenhum cabeçalho de versão * * |
| Node.js |(brevemente) |**Não existe um cabeçalho de versão** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportamento de back-ends de Mobile Services
| VERSÃO DA API DE ZUMO | Valor de MS_SkipVersionCheck | Resposta |
| --- | --- | --- |
| Não foi especificado |Qualquer |200 - OK |
| Qualquer valor |Verdadeiro |200 - OK |
| Qualquer valor |FALSO/não especificado |400 - pedido de incorreto |

## <a name="2.0.0"></a>Cliente de Mobile Apps do Azure e do servidor
### <a name="MobileAppsClients"></a>Mobile *aplicações* SDKs de cliente
A verificar a versão foi introduzida iniciar com as seguintes versões do cliente SDK para **Mobile Apps do Azure**:

| Plataforma de cliente | Versão | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerido (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobile *aplicações* SDKs do servidor
A verificar a versão está incluída nos seguintes versões do SDK de servidor:

| Plataforma de servidor | SDK | Cabeçalho de versão aceite |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[Azure--aplicações móveis)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento de back-ends de Mobile Apps
| VERSÃO DA API DE ZUMO | Valor de MS_SkipVersionCheck | Resposta |
| --- | --- | --- |
| x.y.z ou nulo |Verdadeiro |200 - OK |
| Valor nulo |FALSO/não especificado |400 - pedido de incorreto |
| 1.x.y |FALSO/não especificado |400 - pedido de incorreto |
| 2.0.0-2.x.y |FALSO/não especificado |200 - OK |
| 3.0.0-3.x.y |FALSO/não especificado |400 - pedido de incorreto |

## <a name="next-steps"></a>Passos Seguintes
* [migrar um serviço móvel para o App Service do Azure]

[Clientes de Mobile Services]: #MobileServicesClients
[Clientes de aplicações móveis]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[migrar um serviço móvel para o App Service do Azure]: app-service-mobile-migrating-from-mobile-services.md
