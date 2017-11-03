---
title: "Como configurar a autenticação do Facebook para a sua aplicação de serviços aplicacionais"
description: "Saiba como configurar a autenticação do Facebook para a sua aplicação de serviços de aplicações."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: c1b4c91d384c56c4f55bf8d31ced250f51c0d837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Como configurar a sua aplicação do Serviço de Aplicações para utilizar o início de sessão do Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de aplicações do Azure para utilizar o Facebook como um fornecedor de autenticação.

Para concluir o procedimento deste tópico, tem de ter uma conta do Facebook que tem um endereço de correio eletrónico verificado e um número de telemóvel. Para criar uma nova conta do Facebook, aceda a [facebook.com].

## <a name="register"></a>Registar a aplicação com o Facebook
1. Inicie sessão no [portal do Azure]e navegue até à sua aplicação. Copiar o **URL**. Irá utilizá-lo para configurar a sua aplicação do Facebook.
2. Na outra janela do browser, navegue para o [Facebook programadores] credenciais de contas do Web site e inicie sessão com o Facebook.
3. (Opcional) Se ainda não registou, clique em **aplicações** > **registar como um programador**, em seguida, aceite a política e siga os passos de registo.
4. Clique em **as minhas aplicações** > **adicionar uma nova aplicação** > **Web site** > **ignorar e criar o ID da aplicação**. 
5. No **nome a apresentar**, escreva um nome exclusivo para a sua aplicação, o tipo do **correio eletrónico de contacto**, escolha um **categoria** para a sua aplicação, em seguida, clique em **criar ID da aplicação**e concluir a verificação de segurança. Isto leva-o para o dashboard de programador para a nova aplicação do Facebook.
6. Em "Iniciar sessão Facebook", clique em **começar**. Adicionar a sua aplicação **URI de redirecionamento** para **URIs de redirecionamento OAuth válido**, em seguida, clique em **guardar alterações**. 
   
   > [!NOTE]
   > O URI é o URL da aplicação anexada com o caminho de redirecionamento */.auth/login/facebook/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Certifique-se de que está a utilizar o esquema de HTTPS.
   > 
   > 
7. No painel de navegação esquerdo, clique em **definições**. No **segredo da aplicação** campo, clique em **mostrar**, forneça a palavra-passe, se o pedido, em seguida, tome nota dos valores de **ID da aplicação** e **segredo da aplicação** . Pode utilizá-las mais tarde para configurar a sua aplicação no Azure.
   
   > [!IMPORTANT]
   > O segredo de aplicação é uma credencial de segurança importantes. Não partilhe este segredo com ninguém e distribui-lo dentro de uma aplicação de cliente.
   > 
   > 
8. A conta do Facebook que foi utilizada para registar a aplicação é um administrador da aplicação. Neste momento, apenas os administradores podem iniciar sessão para esta aplicação. Para autenticar a outras contas do Facebook, clique em **revisão da aplicação** e ativar **disponibilizar < nome da aplicação-> público** para ativar o acesso de público geral através da autenticação do Facebook.

## <a name="secrets"></a>Facebook adicionar informações a sua aplicação
1. Volta a [portal do Azure], navegue até à sua aplicação. Clique em **definições** > **autenticação / autorização**e certifique-se de que **autenticação do serviço de aplicações** é **no**.
2. Clique em **Facebook**, cole os valores de ID de aplicação e o segredo de aplicação que obteve anteriormente, opcionalmente ativar qualquer âmbitos necessários para a sua aplicação, em seguida, clique em **OK**.
   
    ![][0]
   
    Por predefinição, o serviço de aplicações fornece autenticação mas não restringir o acesso autorizado ao conteúdo do site e APIs. Tem de autorizar os utilizadores no seu código de aplicação.
3. (Opcional) Para restringir o acesso ao seu site apenas aos utilizadores autenticados pelo Facebook, defina **ação a tomar quando o pedido não é autenticado** para **Facebook**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para o Facebook para autenticação.
4. Quando terminar de configurar a autenticação, clique em **guardar**.

Agora está pronto a utilizar o Facebook para autenticação na sua aplicação.

## <a name="related-content"></a>Relacionados com o conteúdo
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook programadores]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[portal do Azure]: https://portal.azure.com/
