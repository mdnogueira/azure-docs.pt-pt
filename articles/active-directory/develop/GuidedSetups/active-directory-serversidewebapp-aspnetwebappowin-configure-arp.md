---
title: Azure AD v2 ASP.NET Web Server obter iniciada - Config | Microsoft Docs
description: "Implementar Microsoft início de sessão numa solução ASP.NET com uma aplicação de baseadas no browser web tradicional utilizando o padrão de OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurar a sua aplicação Web do ASP.NET com as informações de registo da aplicação

Neste passo, irá configurar o seu projeto para utilizar SSL e, em seguida, utilize o URL de SSL para configurar as informações de registo da aplicação. Depois da primeira, adicionar a aplicação ' as informações de registo para a sua solução através de *Web. config*.

1.  No Explorador de soluções, selecione o projeto e observe o `Properties` janela (se não for apresentada uma janela de propriedades, prima F4)
2.  Alteração `SSL Enabled` para`True`
3.  Copie o valor de `SSL URL` acima e cole-a no `Redirect URL` campo na parte superior desta página, em seguida, clique em *atualização*:<br/><br/>![Propriedades do projeto](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  Adicione o seguinte no `web.config` ficheiro localizado na pasta da raiz, na secção `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
