---
title: "Introdução à autenticação para Mobile Apps no app formulários de Xamarin | Microsoft Docs"
description: "Saiba como utilizar as Mobile Apps para autenticar os utilizadores da sua aplicação de formulários de Xamarin através de vários fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: syntaxc4
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: 81c731f560ed9cdc56416076cd44cba504fa614d
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Adicionar autenticação à sua aplicação de formulários de Xamarin
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como autenticar os utilizadores de uma aplicação de Mobile do serviço de aplicações da sua aplicação de cliente. Neste tutorial, é possível adicionar autenticação para o projeto de início rápido de formulários de Xamarin através de um fornecedor de identidade que é suportado pelo serviço de aplicações. Depois de com êxito a ser autenticado e autorizado através da aplicação móvel, é apresentado o valor de ID de utilizador e será capaz de aceder a dados da tabela restrito.

## <a name="prerequisites"></a>Pré-requisitos
Para obter o resultado melhor com este tutorial, recomendamos que conclua primeiro o [criar uma aplicação Xamarin Forms] [ 1] tutorial. Depois de concluir este tutorial, terá um projeto de formulários de Xamarin que é uma aplicação de TodoList de várias plataforma.

Se utilizar o projeto de início rápido transferido do servidor, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registar a aplicação para autenticação e configure serviços aplicacionais
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitido

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecionar para a sua aplicação, depois do processo de autenticação está concluído. Neste tutorial, utilizamos o esquema de URL _appname_ ao longo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento do lado do servidor:

1. No [portal do Azure][8], selecione o serviço de aplicações.

2. Clique em de **autenticação / autorização** opção do menu.

3. No **permitidos URLs de redirecionamento externos**, introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** esta cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir a especificação de URL normal para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia que escolher, terá de ajustar o código de aplicações móveis com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Adicionar autenticação para a biblioteca de classe portátil
As Mobile Apps utiliza o [LoginAsync] [ 3] método de extensão no [MobileServiceClient] [ 4] a sessão de um utilizador com a autenticação do serviço de aplicações. Este exemplo utiliza um fluxo de autenticação de servidor gerido que apresenta a interface de início de sessão do fornecedor na aplicação. Para obter mais informações, consulte [autenticação de servidor gerido][5]. Para fornecer uma melhor experiência de utilizador na sua aplicação de produção, deve considerar a utilizar em vez disso, [autenticação de cliente gerido][6].

Para autenticar com um projeto de formulários de Xamarin, definir uma **IAuthenticate** interface na biblioteca de classe portátil da aplicação. Em seguida, adicione um **início de sessão** botão para a interface de utilizador definida na biblioteca de classe portátil, que clique para iniciar a autenticação. Após a autenticação com êxito, os dados são carregados do back-end da aplicação móvel.

Implementar o **IAuthenticate** interface para cada plataforma suportada pela sua aplicação.

1. No Visual Studio ou no Xamarin Studio, abra App.cs do projeto com **portátil** no nome, o que é o projeto da biblioteca de classe portátil, em seguida, adicione a seguinte `using` instrução:

        using System.Threading.Tasks;
2. No App.cs, adicione a seguinte `IAuthenticate` imediatamente antes de definição de interface de `App` definição de classe.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Para inicializar a interface com uma implementação específica da plataforma, adicione os seguintes membros estáticos para o **aplicação** classe.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Abra TodoList.xaml do projeto da biblioteca de classes portátil, adicione o seguinte **botão** elemento o *buttonsPanel* elemento de esquema, após o botão existente:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Este botão aciona a autenticação de servidor gerido com o back-end da aplicação móvel.
5. Abrir TodoList.xaml.cs do projeto da biblioteca de classes portátil, em seguida, adicione o seguinte campo para a `TodoList` classe:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Substitua o **OnAppearing** método com o seguinte código:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Este código certifica-se de que os dados apenas são atualizados do serviço após que tenha sido autenticadas.
7. Adicione o processador seguinte para o **Clicked** evento para o **TodoList** classe:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Guardar as alterações e reconstruir o projeto da biblioteca de classes portátil verificar sem erros.

## <a name="add-authentication-to-the-android-app"></a>Adicionar autenticação para a aplicação Android
Esta secção mostra como implementar o **IAuthenticate** interface no projeto de aplicação Android. Ignore esta secção se não estiver a suportar dispositivos Android.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito do **droid** projeto, em seguida, **definir como projeto de arranque**.
2. Prima F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é desencadeada depois da aplicação for iniciada. O código 401 é produzido porque o acesso no back-end é restringido a apenas utilizadores autorizados.
3. Abra a mainactivity. CS no projeto Android e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualização do **MainActivity** classe para implementar o **IAuthenticate** interface, da seguinte forma:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Atualização do **MainActivity** classe adicionando um **MobileServiceUser** campo e um **autenticar** método, o que é necessário para o **IAuthenticate** interface, da seguinte forma:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

6. Atualização do **AndroidManifest.xml** ficheiro adicionando o seguinte XML dentro de `<application>` elemento:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Substitua `{url_scheme_of_your_app}` com a esquema de URL.
7. Adicione o seguinte código para o **OnCreate** método o **MainActivity** classe antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Este código garante que o autenticador é inicializado antes das cargas de aplicação.
8. Reconstruir a aplicação, execute-o e, em seguida, inicie sessão com o fornecedor de autenticação que escolheu e certifique-se de que é capazes de aceder a dados como um utilizador autenticado.

## <a name="add-authentication-to-the-ios-app"></a>Adicionar autenticação à aplicação iOS
Esta secção mostra como implementar o **IAuthenticate** interface no projeto de aplicação iOS. Ignore esta secção se não estiver a suportar dispositivos iOS.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito do **iOS** projeto, em seguida, **definir como projeto de arranque**.
2. Prima F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é desencadeada depois da aplicação for iniciada. A resposta 401 é produzido porque o acesso no back-end é restringido a apenas utilizadores autorizados.
3. Abra appdelegate. CS no projeto iOS e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualização do **AppDelegate** classe para implementar o **IAuthenticate** interface, da seguinte forma:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Atualização do **AppDelegate** classe adicionando um **MobileServiceUser** campo e um **autenticar** método, o que é necessário para o **IAuthenticate** interface, da seguinte forma:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].
    
6. Atualização do **AppDelegate** classe adicionando o **OpenUrl** método de sobrecarga, da seguinte forma:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Adicione a seguinte linha de código para o **FinishedLaunching** método antes da chamada para `LoadApplication()`:

        App.Init(this);

    Este código garante que o autenticador é inicializado antes da aplicação foi carregada.

8. Abra o ficheiro info. plist e adicione um **tipo URL**. Definir o **identificador** para um nome à sua escolha, o **esquemas de URL** para o esquema de URL para a sua aplicação e o **função** como None.

9. Reconstruir a aplicação, execute-o e, em seguida, inicie sessão com o fornecedor de autenticação que escolheu e certifique-se de que é capazes de aceder a dados como um utilizador autenticado.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Adicionar autenticação para projetos de aplicação do Windows 10 (incluindo o telefone)
Esta secção mostra como implementar o **IAuthenticate** interface em projetos de aplicação do Windows 10. Aplicam os mesmos passos para projetos de plataforma Universal do Windows (UWP), mas a utilizar o **UWP** projeto (com alterações apontou). Ignore esta secção se não estiver a suportar dispositivos Windows.

1. No Visual Studio, clique com botão direito do **UWP** projeto, em seguida, **definir como projeto de arranque**.
2. Prima F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é desencadeada depois da aplicação for iniciada. A resposta 401 acontece porque o acesso no back-end é restringido a apenas utilizadores autorizados.
3. Abra MainPage.xaml.cs para o projeto de aplicação do Windows e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Substitua `<your_Portable_Class_Library_namespace>` com o espaço de nomes para a sua biblioteca de classe portátil.
4. Atualização do **MainPage** classe para implementar o **IAuthenticate** interface, da seguinte forma:

        public sealed partial class MainPage : IAuthenticate
5. Atualização do **MainPage** classe adicionando um **MobileServiceUser** campo e um **autenticar** método, o que é necessário para o **IAuthenticate** interface, da seguinte forma:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

1. Adicione a seguinte linha de código no construtor para o **MainPage** classe antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Substitua `<your_Portable_Class_Library_namespace>` com o espaço de nomes para a sua biblioteca de classe portátil.

3. Se estiver a utilizar **UWP**, adicione o seguinte **OnActivated** método de substituição para o **aplicação** classe:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Abra o Package. appxmanifest e adicione um **protocolo** declaração. Definir o **nome a apresentar** para um nome à sua escolha e o **nome** para o esquema de URL para a aplicação.

4. Reconstruir a aplicação, execute-o e, em seguida, inicie sessão com o fornecedor de autenticação que escolheu e certifique-se de que é capazes de aceder a dados como um utilizador autenticado.

## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial de autenticação básica, considere continuar para um dos seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-xamarin-forms-get-started-push.md)

  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Saiba como adicionar suporte offline à aplicação utilizando um back-end de aplicação móvel. Sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel - visualizar, adicionar ou modificar dados -, mesmo quando não existe nenhuma ligação de rede.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
