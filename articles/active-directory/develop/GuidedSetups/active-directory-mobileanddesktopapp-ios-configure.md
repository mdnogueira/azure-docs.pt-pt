---
title: "Introdução do iOS de v2 do Azure AD - configurar | Microsoft Docs"
description: "Como as aplicações do iOS (Swift) podem chamar uma API que necessitam de tokens de acesso ao ponto final do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 0ebca65585fc87bd4a85ba092cd423fce9540f58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="create-an-application-express"></a>Criar uma aplicação (rápida)
Agora tem de registar a sua aplicação no *Portal de registo de aplicações do Microsoft*:
1. Registar a sua aplicação através do [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Introduza um nome para a aplicação e o seu correio eletrónico
3.  Certifique-se que a opção para a configuração orientado está marcada
4.  Siga as instruções para obter o ID de aplicação e cole-o para o seu código

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicione as informações de registo de aplicação à sua solução (avançado)

1.  Aceda a [Portal de registo de aplicações da Microsoft](https://apps.dev.microsoft.com/portal/register-app)
2.  Introduza um nome para a aplicação e o seu correio eletrónico
3.  Certifique-se que a opção para a configuração orientado está desmarcada
4.  Clique em `Add Platform`, em seguida, selecione `Native Application` e clique em`Save`
5.  Volte ao Xcode. No `ViewController.swift`, substitua a linha que começa com '`let kClientID`' com o ID de aplicação que acabou de ser registado:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Controlar + clique <code>Info.plist</code> trazer o menu de nível contextual das e, em seguida, clique em: <code>Open As</code>> <code>Source Code</code>
</li>
<li>
Sob o <code>dict</code> nó raiz, adicione o seguinte:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Substitua <i> <code>[Your_Application_Id_Here]</code> </i> com o Id de aplicação que acabou de ser registado
</li>
</ol>
