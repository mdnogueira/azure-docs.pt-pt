---
title: "Adicionar autenticação no Apache Cordova com aplicações móveis | Microsoft Docs"
description: "Saiba como utilizar as Mobile Apps no App Service do Azure para autenticar os utilizadores da sua aplicação Apache Cordova através de vários fornecedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: b7362b7f26859de541f792e714502851d74c98e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Adicionar autenticação à aplicação Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo
Neste tutorial, é possível adicionar autenticação para o projeto de início rápido todolist no Apache Cordova utilizando um fornecedor de identidade suportados. Este tutorial baseia-se no [introdução às Mobile Apps] tutorial, que tem de concluir primeiro.

## <a name="register"></a>Registar a aplicação para autenticação e configurar o serviço de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, pode verificar o acesso anónimo ao back-end foi desativado. No Visual Studio:

* Abra o projeto que criou quando concluir o tutorial [introdução às Mobile Apps].
* Execute a sua aplicação **emulador do Google Android**.
* Certifique-se de que uma falha inesperada de ligação é apresentada depois da aplicação for iniciada.

Em seguida, atualize a aplicação para autenticar os utilizadores antes de pedir a recursos de back-end da aplicação móvel.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
1. Abra o projeto no **Visual Studio**, em seguida, abra o `www/index.html` ficheiro para edição.
2. Localize o `Content-Security-Policy` etiqueta meta na secção principal.  Adicione o anfitrião de OAuth à lista de origens permitidas.

   | Fornecedor | Nome do fornecedor SDK | Anfitrião do OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.Facebook.com |
   | Google | Google | https://Accounts.google.com |
   | Microsoft | MicrosoftAccount | https://login.Live.com |
   | Twitter | Twitter | https://API.twitter.com |

    Segue-se um exemplo de política de segurança de conteúdo (implementado para o Azure Active Directory):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Substitua `https://login.microsoftonline.com` com o anfitrião de OAuth da tabela anterior.  Para obter mais informações sobre a etiqueta de metadados de política de segurança de conteúdo, consulte o [documentação da política de segurança de conteúdo].

    Alguns fornecedores de autenticação não necessitam de que política de segurança de conteúdo é alterado quando utilizado em dispositivos móveis adequados.  Por exemplo, sem alterações de política de segurança de conteúdo são necessárias quando utiliza a autenticação de Google num dispositivo Android.

3. Abra o `www/js/index.js` de edição no ficheiro, localize o `onDeviceReady()` método, e sob a criação de cliente código adicione o seguinte código:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Este código substitui o código existente, que cria a referência de tabela e atualiza a IU.

    O método login() começa a autenticação com o fornecedor. O método de login() é uma função async que devolva uma promessa JavaScript.  O resto da inicialização é colocado dentro de resposta promessa para que não foi executada até que o método login() seja concluída.

4. No código que acabou de adicionar, substituir `SDK_Provider_Name` com o nome do seu fornecedor de início de sessão. Por exemplo, para o Azure Active Directory, utilize `client.login('aad')`.
5. Execute o projeto.  Quando o projeto concluiu o início, a sua aplicação mostra a página de início de sessão OAuth para o fornecedor de autenticação escolhido.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais [sobre autenticação] App Service do Azure.
* Continuar o tutorial adicionando [notificações Push] à aplicação Apache Cordova.

Saiba como utilizar os SDKs.

* [SDK do Apache Cordova]
* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- URLs. -->
[introdução às Mobile Apps]: app-service-mobile-cordova-get-started.md
[documentação da política de segurança de conteúdo]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[notificações Push]: app-service-mobile-cordova-get-started-push.md
[sobre autenticação]: app-service-mobile-auth.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
