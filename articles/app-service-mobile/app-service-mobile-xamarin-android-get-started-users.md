---
title: "Introdução à autenticação para Mobile Apps no Xamarin Android"
description: "Saiba como utilizar as Mobile Apps para autenticar os utilizadores da sua aplicação Xamarin Android através de vários fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: panarasi
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 8f9a1109018c708d52cdcb7b8bce43861cecd31c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adicionar autenticação à sua aplicação xamarin. Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar os utilizadores de uma aplicação móvel da sua aplicação de cliente. Neste tutorial, é possível adicionar autenticação para o projeto de início rápido, utilizando um fornecedor de identidade que é suportado pelo Mobile Apps do Azure. Após com êxito a ser autenticado e autorizado na aplicação móvel, é apresentado o valor de ID de utilizador.

Este tutorial é baseado no guia de introdução do aplicação móvel. Também primeiro tem de concluir o tutorial [criar uma aplicação xamarin. Android]. Se utilizar o projeto de início rápido transferido do servidor, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registar a aplicação para autenticação e configure serviços aplicacionais
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitido

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecionar para a sua aplicação, depois do processo de autenticação está concluído. Neste tutorial, utilizamos o esquema de URL _appname_ ao longo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento do lado do servidor:

1. No [portal do Azure], selecione o serviço de aplicações.

2. Clique em de **autenticação / autorização** opção do menu.

3. No **permitidos URLs de redirecionamento externos**, introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** esta cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir a especificação de URL normal para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia que escolher, terá de ajustar o código de aplicações móveis com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou no Xamarin Studio, execute o projeto de cliente num dispositivo ou emulador. Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é desencadeada depois da aplicação for iniciada. Isto acontece porque a aplicação tenta aceder ao seu back-end da aplicação móvel como um utilizador não autorizado. O *TodoItem* tabela agora requer autenticação.

Em seguida, atualizar a aplicação de cliente para recursos de pedido de back-end da aplicação móvel com um utilizador autenticado.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
A aplicação é atualizada para exigir que os utilizadores toque o **sessão** botão e autenticar antes de apresentar dados.

1. Adicione o seguinte código para o **TodoActivity** classe:
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Esta ação cria um novo método para autenticar um utilizador e um processador de método para um novo **sessão** botão. O utilizador com o código de exemplo acima é autenticado utilizando um início de sessão do Facebook. Uma caixa de diálogo é utilizada para apresentar o ID de utilizador, uma vez autenticado.
   
   > [!NOTE]
   > Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor transmitido ao **LoginAsync** acima para um dos seguintes: *MicrosoftAccount*, *Twitter*,  *Google*, ou *WindowsAzureActiveDirectory*.
   > 
   > 
2. No **OnCreate** método, eliminar ou comente a linha de código seguinte:
   
        OnRefreshItemsSelected ();
3. No ficheiro Activity_To_Do.axml, adicione o seguinte *LoginUser* botão definição antes do existente *AddItem* botão:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adicione o seguinte elemento no ficheiro de recursos Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Abra o ficheiro AndroidManifest.xml, adicione o seguinte código dentro `<application>` elemento XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. No Visual Studio ou no Xamarin Studio, execute o projeto de cliente num dispositivo ou emulador e inicie sessão com o fornecedor de identidade que escolheu. Quando estiver registado com êxito, a aplicação irá apresentar o ID de início de sessão e a lista de itens todo e que pode efetuar atualizações para os dados.

<!-- URLs. -->
[criar uma aplicação xamarin. Android]: app-service-mobile-xamarin-android-get-started.md