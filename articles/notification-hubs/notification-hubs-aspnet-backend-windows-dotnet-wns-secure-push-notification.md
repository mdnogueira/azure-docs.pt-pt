---
title: Notification Hubs do Azure segurados Push
description: "Saiba como enviar notificações push segura no Azure. Exemplos de código escrito em C# utilizando a API .NET."
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 9c626ec1534c4899588150a58c0da57b9d963f6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-secure-push"></a>Notification Hubs do Azure segurados Push
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Descrição geral
Suporte de notificação push no Microsoft Azure permite-lhe aceder a uma infraestrutura de push de fácil utilização, multiplataformas, escalamento horizontal, o que simplifica bastante a implementação de notificações push para aplicações de consumidor e para empresas para plataformas móveis.

Devido a regulamentação ou restrições de segurança, por vezes, uma aplicação pode pretender incluir algo na notificação que não pode ser transmitida através da infraestrutura de notificação push padrão. Este tutorial descreve como obter a mesma experiência, enviando informações confidenciais através de uma ligação segura, autenticada entre o dispositivo de cliente e o back-end da aplicação.

Um nível elevado, o fluxo é:

1. O back-end da aplicação:
   * Payload de arquivos segura na base de dados de back-end.
   * Envia o ID desta notificação para o dispositivo (sem informações seguras são enviadas).
2. A aplicação no dispositivo, quando receber a notificação:
   * O dispositivo contacta o back-end pedir o payload seguro.
   * A aplicação pode mostrar o payload como uma notificação no dispositivo.

É importante ter em atenção que o fluxo anterior (e neste tutorial), partimos do princípio que o dispositivo armazena um token de autenticação no armazenamento local, depois do utilizador iniciar sessão. Esta ação garante uma experiência totalmente integrada, como o dispositivo pode obter payload segura a notificação utilizando este token. Se a aplicação não armazena os tokens de autenticação do dispositivo, ou se estes tokens podem ter expirados, a aplicação de dispositivo, ao receber a notificação deverá apresentar uma notificação genérica pedir ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra o payload de notificação.

Este tutorial de Secure Push mostra como enviar uma notificação push de forma segura. O tutorial baseia-se a [notificam os utilizadores](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) tutorial, pelo que deverá concluir os passos que tutorial primeiro.

> [!NOTE]
> Este tutorial parte do princípio de que criou e configurado o notification hub, conforme descrito em [introdução aos Notification Hubs (loja Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Além disso, tenha em atenção que o Windows Phone 8.1 requer credenciais do Windows (não Windows Phone), e que as tarefas em segundo plano não funcionam no Windows Phone 8.0 ou Silverlight 8.1. Para aplicações da loja Windows, pode receber notificações através de uma tarefa em segundo plano apenas se a aplicação está ativada de ecrã de bloqueio (clique na caixa de verificação a Appmanifest).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o projeto do Windows Phone
1. No **NotifyUserWindowsPhone** do projeto, adicione o seguinte código para App.xaml.cs para registar a tarefa de fundo de push. Adicione a seguinte linha de código no final de `OnLaunched()` método:
   
        RegisterBackgroundTask();
2. Ainda no App.xaml.cs, adicione o seguinte código imediatamente depois do `OnLaunched()` método:
   
        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();
   
                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }
3. Adicione o seguinte `using` declarações na parte superior do ficheiro App.xaml.cs:
   
        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;
4. A partir do menu **Ficheiro** no Visual Studio, clique em **Guardar Tudo**.

## <a name="create-the-push-background-component"></a>Criar o componente de fundo de Push
O passo seguinte consiste em criar o componente de fundo de push.

1. No Explorador de soluções, clique no nó de nível superior da solução (**solução SecurePush** neste caso), em seguida, clique em **adicionar**, em seguida, clique em **novo projeto**.
2. Expanda **aplicações da loja**, em seguida, clique em **aplicações do Windows Phone**, em seguida, clique em **componente de tempo de execução do Windows (Windows Phone)**. Nomeie o projeto **PushBackgroundComponent**e, em seguida, clique em **OK** para criar o projeto.
   
    ![][12]
3. No Explorador de soluções, clique com botão direito do **PushBackgroundComponent (Windows Phone 8.1)** do projeto, em seguida, clique em **adicionar**, em seguida, clique em **classe**. Nome da nova classe **PushBackgroundTask.cs**. Clique em **adicionar** para gerar a classe.
4. Substitua os conteúdos integrais do **PushBackgroundComponent** definição de espaço de nomes com o código seguinte, substituindo o marcador de posição `{back-end endpoint}` com o ponto final de back-end obtido durante a implementação de back-end:
   
        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }
   
            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";
   
                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;
   
                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);
   
                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);
   
                    ShowToast(notification);
   
                    deferral.Complete();
                }
   
                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }
5. No Explorador de soluções, clique com botão direito do **PushBackgroundComponent (Windows Phone 8.1)** projeto e, em seguida, clique em **gerir pacotes NuGet**.
6. No lado esquerdo, clique em **Online**.
7. No **pesquisa** caixa, escreva **cliente Http**.
8. Na lista de resultados, clique em **bibliotecas de cliente do Microsoft HTTP**e, em seguida, clique em **instalar**. Conclua a instalação.
9. Regresso do NuGet **pesquisa** caixa, escreva **Json.net**. Instalar o **Json.NET** do pacote, em seguida, feche a janela do Gestor de pacotes NuGet.
10. Adicione o seguinte `using` declarações na parte superior do **PushBackgroundTask.cs** ficheiro:
    
        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;
11. No Explorador de soluções, no **NotifyUserWindowsPhone (Windows Phone 8.1)** do projeto, clique com botão direito **referências**, em seguida, clique em **Adicionar referência...** . Na caixa de diálogo Gestor de referências, selecione a caixa junto **PushBackgroundComponent**e, em seguida, clique em **OK**.
12. No Explorador de soluções, faça duplo clique **Package. appxmanifest** no **NotifyUserWindowsPhone (Windows Phone 8.1)** projeto. Em **notificações**, defina **com capacidade de alerta** para **Sim**.
    
    ![][3]
13. Ainda na **Package. appxmanifest**, clique em de **declarações** menu perto da parte superior. No **declarações disponíveis** lista pendente, clique em **tarefas em segundo plano**e, em seguida, clique em **adicionar**.
14. No **Package. appxmanifest**, em **propriedades**, verifique **notificação Push**.
15. No **Package. appxmanifest**, em **as definições de aplicação**, tipo **PushBackgroundComponent.PushBackgroundTask** no **ponto de entrada** campo.
    
    ![][13]
16. No menu **Ficheiro**, clique em **Guardar Tudo**.

## <a name="run-the-application"></a>Executar a aplicação
Para executar a aplicação, efetue o seguinte:

1. No Visual Studio, execute o **AppBackend** aplicação Web API. É apresentada uma página web do ASP.NET.
2. No Visual Studio, execute o **NotifyUserWindowsPhone (Windows Phone 8.1)** aplicação do Windows Phone. O emulador do Windows Phone é executado e carrega automaticamente a aplicação.
3. No **NotifyUserWindowsPhone** aplicação IU, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia, mas têm de ser o mesmo valor.
4. No **NotifyUserWindowsPhone** aplicação IU, clique em **iniciar sessão e registar**. Em seguida, clique em **enviar push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
