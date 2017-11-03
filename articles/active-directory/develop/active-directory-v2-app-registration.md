---
title: "Registar uma aplicação com o ponto final v 2.0 do Azure AD através do portal | Microsoft Docs"
description: "Como registar uma aplicação com a Microsoft para ativar o início de sessão e aceder aos serviços da Microsoft com o ponto final v 2.0"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: e6202aa8665c906382666fe08a561421e50e0a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Como registar uma aplicação com o ponto final v 2.0
Para criar uma aplicação que aceite MSA & do Azure AD início de sessão, primeiro terá de registar uma aplicação com a Microsoft.  Neste momento, não poderá utilizar todas as aplicações existentes que poderão ter com o Azure AD ou MSA - tem de criar uma nova.

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>Visitar o portal de registo de aplicações do Microsoft
Primeiros aspetos navegam pela primeira vez - [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Esta é um nova aplicação do portal de registo onde pode gerir as aplicações da Microsoft.

Inicie sessão com um um pessoal ou conta escolar ou profissional Microsoft.  Se não tiver uma, inscreva-se numa nova conta pessoal. Avançar, não irá demorar - irá aguardaremos aqui.

Feito? Deve agora ser à procura na sua lista de aplicações da Microsoft, que é provavelmente vazio.  Vamos alterar que.

Clique em **adicionar uma aplicação**e atribua um nome.  O portal irá atribuir a sua aplicação um Id de aplicação exclusivo global que utilizará posteriormente no seu código.  Se a sua aplicação inclui um componente do lado do servidor que tem os tokens de acesso para chamadas as APIs (pensar: Office, Azure ou a suas próprias API web), poderá ser útil criar um **segredo da aplicação** também aqui.

Em seguida, adicione as plataformas que irá utilizar a sua aplicação.

* Para aplicações baseado na web, forneça um **URI de redirecionamento** onde podem ser enviadas mensagens de início de sessão.
* Para aplicações móveis, copie o uri de redirecionamento predefinido criado automaticamente para si.

Opcionalmente, pode personalizar o aspeto e funcionalidade da sua página de início de sessão na secção de perfil.  Certifique-se clicar em **guardar** antes de continuar.

> [!NOTE]
> Quando cria uma aplicação utilizando [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), a aplicação será registada no inquilino inicial da conta que utiliza para iniciar sessão no portal.  Isto significa que não pode registar uma aplicação no seu inquilino do Azure AD utilizando uma conta Microsoft pessoal.  Se pretender registar uma aplicação de um determinado inquilino explicitamente, inicie sessão com uma conta originalmente criada nesse inquilino.
> 
> 

## <a name="build-a-quick-start-app"></a>Criar uma aplicação de início rápido
Agora que tem uma aplicação da Microsoft, pode concluir um dos nossos tutoriais de início rápido de v 2.0.  Seguem-se algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

