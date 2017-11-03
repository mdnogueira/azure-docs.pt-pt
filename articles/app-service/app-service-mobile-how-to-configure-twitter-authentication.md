---
title: "Como configurar a autenticação do Twitter para a sua aplicação de serviços aplicacionais"
description: "Saiba como configurar a autenticação do Twitter para a sua aplicação de serviços de aplicações."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: afde020b7817dc58ecea24eb4a09cf93d0986eb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Como configurar a sua aplicação de serviço de aplicações para utilizar o início de sessão do Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de aplicações do Azure para utilizar o Twitter como um fornecedor de autenticação.

Para concluir o procedimento deste tópico, tem de ter uma conta do Twitter que tem um número de telefone e endereço de correio eletrónico verificado. Para criar uma nova conta do Twitter, aceda a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"></a>Registar a aplicação com o Twitter
1. Inicie sessão no [portal do Azure]e navegue até à sua aplicação. Copiar o **URL**. Irá utilizá-lo para configurar a sua aplicação Twitter.
2. Navegue para o [Twitter programadores] Web site, inicie sessão com as credenciais de conta do Twitter e clique em **criar nova aplicação**.
3. Escreva o **nome** e um **Descrição** para a nova aplicação. Colar na sua aplicação **URL** para o **Web site** valor. Em seguida, para o **URL de chamada de retorno**, cole o **URL de chamada de retorno** que copiou anteriormente. Este é o gateway de aplicação móvel anexado com o caminho, */.auth/login/twitter/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de que está a utilizar o esquema de HTTPS.
4. Na parte inferior da página, leia e aceite os termos de licenciamento. Em seguida, clique em **criar a sua aplicação Twitter**. Este procedimento regista a aplicação apresenta os detalhes da aplicação.
5. Clique no **definições** separador, verificação **permitem que esta aplicação a ser utilizado para iniciar sessão com o Twitter**, em seguida, clique em **definições de atualização**.
6. Selecione o **chaves e os Tokens de acesso** separador. Tome nota dos valores de **consumidor chave (chave de API)** e **segredo de consumidor (API segredo)**.
   
   > [!NOTE]
   > O segredo de consumidor é uma credencial de segurança importantes. Não partilhe este segredo com ninguém e distribui-lo com a sua aplicação.
   > 
   > 

## <a name="secrets"></a>Adicionar Twitter informações da sua aplicação
1. Volta a [portal do Azure], navegue até à sua aplicação. Clique em **definições**e, em seguida, **autenticação / autorização**.
2. Se a autenticação / autorização funcionalidade não está ativada, ative o comutador **no**.
3. Clique em **Twitter**. Colar o ID da aplicação e o segredo de aplicação valores que obteve anteriormente. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por predefinição, o serviço de aplicações fornece autenticação mas não restringir o acesso autorizado ao conteúdo do site e APIs. Tem de autorizar os utilizadores no seu código de aplicação.
4. (Opcional) Para restringir o acesso ao seu site apenas aos utilizadores autenticados pelo Twitter, defina **ação a tomar quando o pedido não é autenticado** para **Twitter**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para Twitter para autenticação.
5. Clique em **Guardar**.

Agora está pronto a utilizar o Twitter para autenticação na sua aplicação.

## <a name="related-content"></a>Relacionados com o conteúdo
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter programadores]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
