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
ms.openlocfilehash: 0c627802ccfba230dcde2dafffee26cb1c895791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="create-an-application-express"></a>Criar uma aplicação (rápida)
Agora tem de registar a sua aplicação no *Portal de registo de aplicações do Microsoft*:
1. Registar a sua aplicação através do [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Introduza um nome para a aplicação e o seu correio eletrónico
3.  Certifique-se que a opção para a configuração orientado está marcada
4.  Siga as instruções para adicionar um URL de redirecionamento da aplicação

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicione as informações de registo de aplicação à sua solução (avançado)
Agora tem de registar a sua aplicação no *Portal de registo de aplicações do Microsoft*:
1. Vá para o [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registar uma aplicação
2. Introduza um nome para a aplicação e o seu correio eletrónico 
3.  Certifique-se que a opção para a configuração orientado está desmarcada
4.  Clique em `Add Platform`, em seguida, selecione`Web`
5.  Vá para o Visual Studio e, no Explorador de soluções, selecione o projeto e observe a janela de propriedades (se não for apresentada uma janela de propriedades, prima F4)
6.  Alteração SSL ativado para`True`
7.  Copie o URL de SSL e adicionar este URL à lista de URLs de redirecionamento na lista do Portal de registo de URLs de redirecionamento:<br/><br/>![Propriedades do projeto](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
8.  Adicione o seguinte no `web.config` localizado na pasta raiz na secção `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Substitua `ClientId` com o Id de aplicação que acabou de ser registado
</li>
<li>
Substitua `redirectUri` com o URL de SSL do seu projeto
</li>
</ol>
<!-- End Docs -->
