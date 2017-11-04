---
title: Azure AD v2 Windows ambiente de trabalho ao obter iniciada - Config | Microsoft Docs
description: "Como uma aplicação .NET de ambiente de trabalho do Windows (XAML) pode obter um token de acesso e chamar uma API protegida pelo ponto final do Azure Active Directory v2. | Microsoft Azure | Microsoft Azure"
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
ms.openlocfilehash: 1dfaa7ade664e43dcb9aa788b0197ca17e6ec4cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="create-an-application-express"></a>Criar uma aplicação (rápida)
Agora tem de registar a sua aplicação no *Portal de registo de aplicações do Microsoft*:
1. Registar a sua aplicação através do [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)
2.  Introduza um nome para a aplicação e o seu correio eletrónico
3.  Certifique-se que a opção para a configuração orientado está marcada
4.  Siga as instruções para obter o ID de aplicação e cole-o para o seu código

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicione as informações de registo de aplicação à sua solução (avançado)
Agora tem de registar a sua aplicação no *Portal de registo de aplicações do Microsoft*:
1. Vá para o [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registar uma aplicação
2. Introduza um nome para a aplicação e o seu correio eletrónico 
3. Certifique-se que a opção para a configuração orientado está desmarcada
4. Clique em `Add Platform`, em seguida, selecione `Native Application` e clique em Guardar
5. Copie o GUID no ID da aplicação, volte atrás para Visual Studio, abra `App.xaml.cs` e substitua `your_client_id_here` com o ID de aplicação que acabou de ser registado:

```csharp
private static string ClientId = "your_application_id_here";
```
