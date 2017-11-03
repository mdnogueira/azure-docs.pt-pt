---
title: "Como configurar a autenticação de Account Microsoft para a sua aplicação de serviços aplicacionais"
description: "Saiba como configurar a autenticação de Account Microsoft para a sua aplicação de serviços de aplicações."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 67386b03ae4cc683fe00e11e8dad19d1442eff09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Como configurar a sua aplicação de serviço de aplicações para utilizar o início de sessão do Microsoft Account
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de aplicações do Azure para utilizar Account Microsoft como um fornecedor de autenticação. 

## <a name="register-microsoft-account"></a>Registar a sua aplicação com a conta Microsoft
1. Inicie sessão no [portal do Azure]e navegue até à sua aplicação. Copiar o **URL**, que mais tarde utilizar para configurar a sua aplicação com Account Microsoft.
2. Navegue para o [aplicações My] página no Microsoft Account Developer Center e inicie sessão com a sua conta Microsoft, se necessário.
3. Clique em **adicionar uma aplicação**, em seguida, escreva um nome de aplicação e, em **Criar aplicação**.
4. Anote o **ID da aplicação**, uma vez que irá precisar dele mais tarde. 
5. Em "Plataformas", clique em **adicionar plataforma** e selecione "Web".
6. Em "URI de redirecionamento" fornecer o ponto final para a sua aplicação, em seguida, clique em **guardar**. 
   
   > [!NOTE]
   > O URI é o URL da aplicação anexada com o caminho de redirecionamento */.auth/login/microsoftaccount/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Certifique-se de que está a utilizar o esquema de HTTPS.
   
7. Em "Segredos de aplicação", clique em **gerar a nova palavra-passe**. Tome nota do valor que é apresentada. Depois de sair da página, não será novamente apresentado.

    > [!IMPORTANT]
    > A palavra-passe é uma credencial de segurança importantes. Não partilhe a palavra-passe com ninguém e distribui-lo dentro de uma aplicação de cliente.

## <a name="secrets"></a>Informações adicionar conta Microsoft à sua aplicação de serviço de aplicações
1. Volta a [portal do Azure], navegue até à sua aplicação, clique em **definições** > **autenticação / autorização**.
2. Se a autenticação / autorização funcionalidade não está ativada, mude- **no**.
3. Clique em **conta Microsoft**. Cole os valores de ID da aplicação e a palavra-passe que obteve anteriormente e, opcionalmente ativar qualquer âmbitos que requer a sua aplicação. Em seguida, clique em **OK**.
   
    ![][1]
   
    Por predefinição, o serviço de aplicações fornece autenticação mas não restringir o acesso autorizado ao conteúdo do site e APIs. Tem de autorizar os utilizadores no seu código de aplicação.
4. (Opcional) Para restringir o acesso ao seu site apenas aos utilizadores autenticados pelo conta Microsoft, defina **ação a tomar quando o pedido não é autenticado** para **Account Microsoft**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para a conta Microsoft para autenticação.
5. Clique em **Guardar**.

Agora está pronto a utilizar o Microsoft Account para autenticação na sua aplicação.

## <a name="related-content"></a>Relacionados com o conteúdo
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[aplicações My]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portal do Azure]: https://portal.azure.com/
