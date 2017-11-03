---
title: "Azure AD v2 JS SPA orientado configuração - configurar (ARP) | Microsoft Docs"
description: "Como aplicações de JavaScript SPA podem chamar uma API que necessitam de tokens de acesso ao ponto final do Azure Active Directory v2 (ARP)"
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
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: 708f4ff606d79639de979918a9cacd4ed75db311
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar informações de registo da aplicação para a sua aplicação

Neste passo, terá de configurar o URL de redirecionamento das suas informações de registo de aplicação e, em seguida, adicione o Id da aplicação para a aplicação do JavaScript SPA.

### <a name="configure-redirect-url"></a>Configurar o URL de redirecionamento

Configurar o `Redirect URL` campo acima com o URL para a sua página de index.html com base no seu servidor web, em seguida, clique em *atualização*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio instruções para obter o URL de redirecionamento
> Para obter o seu URL de redirecionamento, siga as instruções abaixo:
> 1.    No *Explorador de soluções*, selecione o projeto e observe o `Properties` janela (se não for apresentada uma janela de propriedades, prima `F4`)
> 2.    Copie o valor de `URL` para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Colar o valor como um `Redirect URL` na parte superior desta página, em seguida, clique em`Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>URL de redirecionamento de definição para Python
> Para o Python, pode definir web porta do servidor através de linha de comandos. Esta configuração orientada utiliza a porta 8080 para referência, mas quanto utilizar qualquer porta disponível. Em qualquer caso, siga as instruções abaixo para configurar um URL de redirecionamento, as informações de registo de aplicação:<br/>
> Definir `http://localhost:8080/` como um `Redirect URL` na parte superior desta página ou utilize `http://localhost:[port]/` se estiver a utilizar uma porta TCP personalizada (onde *[porta]* é o número de porta TCP personalizado) e, em seguida, clique em 'Atualizar'

### <a name="configure-your-javascript-spa-application"></a>Configurar a sua aplicação JavaScript SPA

1.  Crie um ficheiro denominado `msalconfig.js` que contém as informações de registo de aplicação. Se estiver a utilizar o Visual Studio, selecione o projeto (pasta raiz do projeto), rato e selecione: `Add`  >  `New Item`  >  `JavaScript File`. Nome`msalconfig.js`
2.  Adicione o seguinte código no seu `msalconfig.js` ficheiro:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
