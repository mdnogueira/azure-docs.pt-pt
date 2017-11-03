---
title: "Adicionar autenticação à sua aplicação plataforma Universal do Windows (UWP) | Microsoft Docs"
description: "Saiba como utilizar Mobile Apps do Azure App Service para autenticar os utilizadores da sua aplicação plataforma Universal do Windows (UWP) utilizando uma variedade de fornecedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: windows
author: ggailey777
manager: panarasi
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 47da343d4ec956ec2e669757f56e853675f887a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-windows-app"></a>Adicionar autenticação à sua aplicação do Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como adicionar autenticação baseada na nuvem a sua aplicação móvel. Neste tutorial, adicionar autenticação para o projeto de início rápido da plataforma Universal do Windows (UWP) para Mobile Apps utilizando um fornecedor de identidade que é suportado pelo App Service do Azure. Após com êxito a ser autenticado e autorizado pelo seu back-end de aplicação móvel, é apresentado o valor de ID de utilizador.

Este tutorial baseia o guia de introdução de aplicações móveis. Primeiro tem de concluir o tutorial [introdução às Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registar a aplicação para autenticação e configurar o serviço de aplicações
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

Agora, pode verificar o acesso anónimo ao back-end foi desativado. Com o projeto de aplicação UWP definido como o projeto de arranque, implementar e executar a aplicação; Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é desencadeada depois da aplicação for iniciada. Isto acontece porque a aplicação tenta aceder ao seu código de aplicação móvel como um utilizador não autorizado, mas o *TodoItem* tabela agora requer autenticação.

Em seguida, irá atualizar a aplicação para autenticar os utilizadores antes de solicitar recursos a partir do seu serviço de aplicações.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
1. O UWP aplicação projeto ficheiro MainPage.xaml.cs, adicione o seguinte fragmento de código:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Este código autentica o utilizador com um início de sessão do Facebook. Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor de **MobileServiceAuthenticationProvider** acima para o valor para o fornecedor.
2. Substitua o **OnNavigatedTo()** método no MainPage.xaml.cs. Em seguida, irá adicionar um **sessão** botão para a aplicação que aciona a autenticação.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Adicione o seguinte fragmento de código para MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Abra o ficheiro de projeto MainPage.xaml, localize o elemento que define o **guardar** botão e substitua-o com o seguinte código:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Adicione o seguinte fragmento de código para o App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Abra o ficheiro Package. appxmanifest, navegue para **declarações**, na **declarações disponíveis** na lista pendente, selecione **protocolo** e clique em **adicionar** botão. Configurar agora o **propriedades** do **protocolo** declaração. No **nome a apresentar**, adicione o nome que pretende apresentar aos utilizadores da sua aplicação. No **nome**, adicione o {url_scheme_of_your_app}.
7. Prima a tecla F5 para executar a aplicação, clique em de **sessão** botão e inicie sessão na aplicação com o fornecedor de identidade que escolheu. Após o início de sessão, a aplicação é executado sem erros e é capazes de consultar o seu back-end e disponibilizar atualizações aos dados.

## <a name="tokens"></a>Armazenar o token de autenticação no cliente
O exemplo anterior mostrou uma norma início de sessão, que exige que o cliente contactar o fornecedor de identidade e o serviço de aplicação sempre que a aplicação for iniciada. Não só é ineficaz, pode executar este método para utilização-relacionado com problemas de muitos clientes tente iniciar a aplicação ao mesmo tempo. É uma melhor abordagem para colocar em cache o token de autorização devolvido pelo seu serviço de aplicações e tente utilizar este primeiro antes de utilizar um fornecedor com base no início de sessão.

> [!NOTE]
> Pode colocar em cache o token emitido por serviços aplicacionais independentemente se estão a utilizar autenticação geridos pelo cliente ou serviço gerido. Este tutorial utiliza a autenticação de serviço gerido.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial de autenticação básica, considere continuar para um dos seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
