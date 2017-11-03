---
title: "Introdução à autenticação para Mobile Apps no Xamarin iOS"
description: "Saiba como utilizar as Mobile Apps para autenticar os utilizadores da sua aplicação Xamarin iOS através de vários fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: glenga
ms.openlocfilehash: 454b2df5a9bf8cfba93befea54370957ab044d95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Adicionar autenticação à sua aplicação Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar os utilizadores de uma aplicação de Mobile do serviço de aplicações da sua aplicação de cliente. Neste tutorial, é possível adicionar autenticação para o projeto de início rápido do xamarin. IOS utilizando um fornecedor de identidade que é suportado pelo serviço de aplicações. Depois de com êxito a ser autenticado e autorizado através da aplicação móvel, o valor de ID de utilizador é apresentado e poderá aceder aos dados da tabela restrito.

Primeiro tem de concluir o tutorial [criar uma aplicação xamarin. IOS]. Se utilizar o projeto de início rápido transferido do servidor, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registar a aplicação para autenticação e configure serviços aplicacionais
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitido

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecionar para a sua aplicação, depois do processo de autenticação está concluído. Neste tutorial, utilizamos o esquema de URL _appname_ ao longo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento do lado do servidor:

1. No [portal do Azure], selecione o serviço de aplicações.

2. Clique em de **autenticação / autorização** opção do menu.

3. No **permitidos URLs de redirecionamento externos**, introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** esta cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir a especificação de URL normal para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia que escolher, terá de ajustar o código de aplicações móveis com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. No Visual Studio ou no Xamarin Studio, execute o projeto de cliente num dispositivo ou emulador. Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é desencadeada depois da aplicação for iniciada. A falha é registada para a consola do depurador. Por isso, no Visual Studio, deverá ver a falha na janela de saída.

&nbsp;&nbsp;Esta falha não autorizada acontece porque a aplicação tenta aceder ao seu back-end da aplicação móvel como um utilizador não autorizado. O *TodoItem* tabela agora requer autenticação.

Em seguida, atualizar a aplicação de cliente para recursos de pedido de back-end da aplicação móvel com um utilizador autenticado.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação à aplicação
Nesta secção, irá modificar a aplicação para apresentar um ecrã de início de sessão antes de apresentar dados. Quando a aplicação for iniciada, não não conseguirá estabelecer ligação ao seu serviço de aplicações e não apresentará quaisquer dados. Após a primeira vez que o utilizador efetua o gesto de atualização, será apresentado o ecrã de início de sessão; após o início de sessão com êxito a lista de itens todo será apresentada.

1. No projeto de cliente, abra o ficheiro **QSTodoService.cs** e adicione o seguinte utilizando a instrução e `MobileServiceUser` com acessor para a classe de QSTodoService:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Adicionar novo método de chamada **autenticar** para **QSTodoService** com a definição dos seguinte:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Se estiver a utilizar um fornecedor de identidade que não seja um Facebook, altere o valor transmitido ao **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_,  _Google_, ou _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**. Modificar a definição de método de **ViewDidLoad** a chamada para a remover **RefreshAsync()** perto do fim:
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();
   
            RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync();
            }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync();
        }
4. Modifique o método **RefreshAsync** para se autenticar caso o **utilizador** propriedade é nula. Adicione o seguinte código na parte superior da definição de método:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Abra **appdelegate. cs**, adicione o método seguinte:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Abra **Info. plist** de ficheiros, navegue para **URL tipos** no **avançadas** secção. Configurar agora o **identificador** e **esquemas de URL** do tipo de URL e clique em **Adicionar tipo de URL**. **Esquemas de URL** deve ser o mesmo que o {url_scheme_of_your_app}.
7. No Visual Studio ou no Xamarin Studio ligado ao anfitrião de compilação do Xamarin no Mac, execute o projeto de cliente, direcionado para um dispositivo ou emulador. Certifique-se de que a aplicação apresenta sem dados.
   
    Execute o gesto de atualização ao extrair baixo a lista de itens, o que fará com que o ecrã início de sessão. Depois de introduzir credenciais válidas com êxito, a aplicação irá apresentar a lista de itens todo e que pode efetuar atualizações para os dados.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[criar uma aplicação xamarin. IOS]: app-service-mobile-xamarin-ios-get-started.md