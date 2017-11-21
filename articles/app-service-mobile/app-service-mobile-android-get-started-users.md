---
title: "Adicionar a autenticação no Android com aplicações móveis | Microsoft Docs"
description: "Saiba como utilizar a funcionalidade de Mobile Apps do App Service do Azure para autenticar os utilizadores da sua aplicação Android através de vários fornecedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: glenga
ms.openlocfilehash: 3d38482ebca736e4d7d0fe0b1b4b5dd0220de5b4
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="add-authentication-to-your-android-app"></a>Adicionar autenticação à sua aplicação Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo
Neste tutorial, adicionar autenticação para o projeto de início rápido todolist no Android utilizando um fornecedor de identidade suportados. Este tutorial baseia-se no [introdução às Mobile Apps] tutorial, que tem de concluir primeiro.

## <a name="register"></a>Registar a aplicação para autenticação e configurar o App Service do Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitido

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecionar para a sua aplicação, depois do processo de autenticação está concluído. Neste tutorial, utilizamos o esquema de URL _appname_ ao longo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento do lado do servidor:

1. No [portal do Azure], selecione o serviço de aplicações.

2. Clique em de **autenticação / autorização** opção do menu.

3. No **permitidos URLs de redirecionamento externos**, introduza `appname://easyauth.callback`.  O _appname_ esta cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir a especificação de URL normal para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia que escolher, terá de ajustar o código de aplicações móveis com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Android Studio, abra o projeto concluiu com o tutorial [introdução às Mobile Apps]. Do **executar** menu, clique em **executar a aplicação**e certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é desencadeada depois da aplicação for iniciada.

     Esta exceção acontece porque a aplicação tenta aceder a back-end como um utilizador não autorizado, mas o *TodoItem* tabela agora requer autenticação.

Em seguida, atualize a aplicação para autenticar os utilizadores antes de pedir a recursos de back-end das Mobile Apps.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação à aplicação
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokens de autenticação de cache no cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial de autenticação básica, considere continuar para um dos seguintes tutoriais:

* [Adicionar notificações push para a sua aplicação Android](app-service-mobile-android-get-started-push.md).
  Saiba como configurar o seu Mobile Apps de back-end para utilizar notification hubs do Azure para enviar notificações push.
* [Ativar a sincronização offline para a sua aplicação Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de aplicações móveis. Com a sincronização offline, os utilizadores poderão interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[introdução às Mobile Apps]: app-service-mobile-android-get-started.md
[portal do Azure]: https://portal.azure.com/
