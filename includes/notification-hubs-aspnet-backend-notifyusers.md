## <a name="create-the-webapi-project"></a>Criar o Projeto WebAPI
Será criado um novo back-end de ASP.NET WebAPI nas secções que se seguem, que terá três objetivos principais:

1. **Autenticar os clientes**: será adicionado um processador de mensagens posteriormente, para autenticar os pedidos dos clientes e associar os utilizadores aos pedidos.
2. **Registos de Notificação de cliente**: mais tarde, vai adicionar um controlador para processar registos novos para que um dispositivo de cliente receba notificações. O nome de utilizador autenticado será adicionado automaticamente ao registo como uma [etiqueta](https://msdn.microsoft.com/library/azure/dn530749.aspx).
3. **Enviar Notificações para os Clientes**: posteriormente, também vai adicionar um controlador para permitir que um utilizador acione um envio seguro para dispositivos e clientes associados à etiqueta. 

Os passos seguintes mostram como criar o novo back-end de ASP.NET WebAPI: 

> [!IMPORTANT]
> se estiver a utilizar o Visual Studio 2015 ou anterior, antes de começar este tutorial, certifique-se que instalou a versão mais recente do Gestor de Pacotes de NuGet. Para confirmar, inicie o Visual Studio. No menu **Ferramentas**, clique em **Extensões e Atualizações**. Procure o **Gestor de Pacotes de NuGet** para a sua versão do Visual Studio e verifique que tem a versão mais recente. Se não tiver, desinstale e reinstale o Gestor de Pacotes.
> 
> ![][B4]
> 
> [!NOTE]
> Verifique que tem instalado o [Azure SDK](https://azure.microsoft.com/downloads/) para Visual Studio para implementação de Website.
> 
> 

1. Inicie o Visual Studio ou o Visual Studio Express. Clique em **Explorador de Servidores** e inicie sessão na sua conta do Azure. O Visual Studio requer que tenha sessão iniciada para criar os recursos do Website na sua conta.
2. No Visual Studio, clique em **Ficheiro**, em seguida, clique em **Novo**, em seguida, **Projeto**, expanda **Modelos**, **Visual c#**, em seguida, clique em **Web** e em **Aplicação Web ASP.NET**, escreva o nome **AppBackend** e, for fim, clique em **OK**. 
   
    ![][B1]
3. Na caixa de diálogo **Novo Projeto ASP.NET**, clique em **API Web** e em **OK**.
   
    ![][B2]
4. Na caixa de diálogo **Configurar Aplicação Web do Microsoft Azure** e escolha uma subscrição e um **plano do Serviço de Aplicações** que já criou. Também pode optar por **criar um novo plano do Serviço de Aplicações** e criar uma caixa de diálogo. Não precisa de uma base de dados para este tutorial. Assim que tiver selecionado o plano do Serviço de Aplicações, clique em **OK** para criar o projeto.
   
    ![][B5]

## <a name="authenticating-clients-to-the-webapi-backend"></a>Autenticar Clientes no Back-end de WebAPI
Nesta secção, vai criar uma nova classe de processadores de mensagens com o nome **AuthenticationTestHandler** para o back-end novo. Esta classe é derivada de [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) e adicionada como um processador de mensagens, para poder processar todos os pedidos enviados para o back-end. 

1. No Explorador de Soluções, clique com o botão direito do rato no projeto **AppBackend**, clique em **Adicionar** e clique em **Classe**. Dê o nome **AuthenticationTestHandler.cs** à classe e clique em **Adicionar** para gerar a classe. Esta classe será utilizada para autenticar utilizadores com a *autenticação básica*, para simplicidade. Tenha em atenção que a sua aplicação pode utilizar qualquer esquema de autenticação.
2. No AuthenticationTestHandler.cs, adicione as instruções `using` seguintes:
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Text;
        using System.Threading.Tasks;

3. No AuthenticationTestHandler.cs, substitua a definição de classe `AuthenticationTestHandler` pelo código seguinte. 
   
    Este processador autorizará o pedido quando se verificarem as três condições seguintes:
   
   * O pedido inclui um cabeçalho *Autorização*. 
   * O pedido utiliza a autenticação *básica*. 
   * A cadeia de nome de utilizador e a cadeia de palavra-passe são a mesma cadeia.
     
     Caso contrário, o pedido será rejeitado. Esta não é uma verdadeira abordagem de autenticação e autorização. É apenas um exemplo muito simples para este tutorial.
     
     Se `AuthenticationTestHandler` autenticar e autorizar a mensagem do pedido, o utilizador de autenticação básica será anexado ao pedido atual no [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). As informações do utilizador no HttpContext serão utilizadas por outro controlador (RegisterController) mais tarde, para adicionar uma [etiqueta](https://msdn.microsoft.com/library/azure/dn530749.aspx) ao pedido de registo de notificação.
     
       public class AuthenticationTestHandler : DelegatingHandler   {       protected override Task<HttpResponseMessage> SendAsync(       HttpRequestMessage request, CancellationToken cancellationToken)       {           var authorizationHeader = request.Headers.GetValues("Authorization").First();
     
               if (authorizationHeader != null && authorizationHeader
                   .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
               {
                   string authorizationUserAndPwdBase64 =
                       authorizationHeader.Substring("Basic ".Length);
                   string authorizationUserAndPwd = Encoding.Default
                       .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                   string user = authorizationUserAndPwd.Split(':')[0];
                   string password = authorizationUserAndPwd.Split(':')[1];
     
                   if (verifyUserAndPwd(user, password))
                   {
                       // Attach the new principal object to the current HttpContext object
                       HttpContext.Current.User =
                           new GenericPrincipal(new GenericIdentity(user), new string[0]);
                       System.Threading.Thread.CurrentPrincipal =
                           System.Web.HttpContext.Current.User;
                   }
                   else return Unauthorized();
               }
               else return Unauthorized();
     
               return base.SendAsync(request, cancellationToken);
           }
     
           private bool verifyUserAndPwd(string user, string password)
           {
               // This is not a real authentication scheme.
               return user == password;
           }
     
           private Task<HttpResponseMessage> Unauthorized()
           {
               var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
               var tsc = new TaskCompletionSource<HttpResponseMessage>();
               tsc.SetResult(response);
               return tsc.Task;
           }
       }
     
     > [!NOTE]
     > **Nota de Segurança**: a classe `AuthenticationTestHandler` não fornecer uma autenticação verdadeira. É utilizada apenas para imitar a autenticação básica e não é segura. Tem de implementar um mecanismo de autenticação segura nos seus serviços e aplicações de produção.                
     > 
     > 
4. Adicione o seguinte código ao final do método `Register` na classe **App_Start/WebApiConfig.cs** para registar o processador de mensagens:
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());
5. Guarde as alterações.

## <a name="registering-for-notifications-using-the-webapi-backend"></a>Utilizar o Back-End de WebAPI para Registar Notificações
Nesta secção, vamos adicionar um controlador novo ao back-end de WebAPI para processar pedidos de registo de um utilizador e de um dispositivo para as notificações através da biblioteca de cliente dos hubs de notificação. O controlador vai adicionar uma etiqueta de utilizador para o utilizador que o `AuthenticationTestHandler` autenticou e anexou ao HttpContext. O formato da cadeia será `"username:<actual username>"`.

1. No Explorador de Soluções, clique com o botão direito no projeto **AppBackend** e, em seguida, clique em **Gerir Pacotes de NuGet**.
2. No lado esquerdo, clique em **Online**e procure **Microsoft.Azure.NotificationHubs** na caixa **Pesquisar**.
3. Na lista de resultados, clique em **Hubs de Notificação do Microsoft Azure** e clique em **Instalar**. Conclua a instalação e feche a janela do gestor de pacotes de NuGet.
   
    Esta ação adiciona uma referência ao SDK dos Notification Hubs do Azure utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Pacote NuGet Microsoft.Azure.Notification Hubs</a>.
4. Vamos agora criar um ficheiro de classe novo que representa a ligação ao hub de notificação utilizado para enviar notificações. No Explorador de Soluções, clique com o botão direito do rato na pasta **Modelos**, clique em **Adicionar** e clique em **Classe**. Dê o nome **Notifications.cs** à classe nova e clique em **Adicionar** para gerá-la. 
   
    ![][B6]
5. Em Notifications.cs, adicione a instrução `using` à parte superior do ficheiro:
   
        using Microsoft.Azure.NotificationHubs;
6. Substitua a definição de classe `Notifications` pelo seguinte e confirme que substitui os dois marcadores de posição pela cadeia de ligação (com acesso total) do seu hub de notificação e o nome do hub (disponível no [Portal Clássico do Azure](http://manage.windowsazure.com)):
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. Em seguida, vamos criar um controlador novo com o nome **RegisterController**. No Explorador de Soluções, clique com o botão direito do rato na pasta **Controladores**, clique em **Adicionar** e, em seguida, clique em **Controlador**. Clique no item **Web API 2 Controller -- Empty** e, em seguida, clique em **Adicionar**. Dê o nome **RegisterController** à classe nova e clique em **Adicionar** novamente para gerar o controlador.
   
    ![][B7]
   
    ![][B8]
8. No RegisterController.cs, adicione as instruções `using` seguintes:
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
9. Adicione o código seguinte dentro da definição de classe `RegisterController`. Tenha em conta que, neste código, adicionamos uma etiqueta de utilizador para o utilizador que está anexado ao HttpContext. O utilizador foi autenticado e anexado ao HttpContext através do filtro de mensagem que adicionámos, `AuthenticationTestHandler`. Também pode adicionar verificações opcionais para confirmar que o utilizador tem direitos para se registar nas etiquetas pedidas.
   
        private NotificationHubClient hub;
   
        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }
   
        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }
   
        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            string newRegistrationId = null;
   
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);
   
                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }
   
            if (newRegistrationId == null) 
                newRegistrationId = await hub.CreateRegistrationIdAsync();
   
            return newRegistrationId;
        }
   
        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }
   
            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;
   
            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);
   
            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }
10. Guarde as alterações.

## <a name="sending-notifications-from-the-webapi-backend"></a>Enviar Notificações a Partir do Back-end de WebAPI
Nesta secção, vai adicionar um controlador novo que expõe uma forma de os dispositivos cliente enviarem notificações com base na etiqueta de nome de utilizador através da Biblioteca de Gestão do Serviço Hub de Notificação no back-end de ASP.NET WebAPI.

1. Crie outro controlador novo com o nome **NotificationsController**. Crie-o da mesma forma que criou o **RegisterController** na secção anterior.
2. No NotificationsController.cs, adicione as instruções `using` seguintes:
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
3. Adicione o método seguinte à classe **NotificationsController**.
   
    Este código envia um tipo de notificação com base no parâmetro `pns` do Serviço de Notificação de Plataforma (PNS). O valor de `to_tag` é utilizado para definir a etiqueta *nome de utilizador* na mensagem. Esta etiqueta tem de corresponder a uma etiqueta de nome de utilizador de um registo de hub de notificação ativo. A mensagem da notificação é retirada do corpo do pedido POST e formatada para o PNS de destino. 
   
    Consoante o Serviço de Notificação de Plataforma (PNS) que os seus dispositivos suportados utilizam para receber notificações, são suportadas diferentes notificações com diferentes formatos. Por exemplo, em dispositivos Windows, pode utilizar uma [notificação de alerta com WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) que não seja diretamente suportada por outro PNS. Assim, o seu back-end teria de formatar a notificação numa que seja suportada pelo PNS dos dispositivos que pretende incluir. Depois, utilize a API de envio adequada na [classe NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)
   
        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;
   
            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;
   
            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }
   
            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }
   
            return Request.CreateResponse(ret);
        }
4. Prima **F5** para executar a aplicação e para garantir a precisão do seu trabalho até ao momento. A aplicação deverá iniciar um browser e apresentar a home page do ASP.NET. 

## <a name="publish-the-new-webapi-backend"></a>Publicar o Back-end de WebAPI Novo
1. Agora, vamos implementar esta aplicação num Website do Azure, para que seja acessível a partir de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
2. Selecione **Serviço de Aplicações do Microsoft Azure** como o destino da publicação e clique em **Publicar**. É aberta a caixa de diálogo Criar Serviço de Aplicações, que o ajuda a criar todos os recursos do Azure necessários para executar a sua aplicação Web ASP.NET no Azure.

    ![][B15]
3. Na caixa de diálogo **Criar Serviço de Aplicações**, selecione a sua conta do Azure. Clique em **Alterar Tipo** e selecione **Aplicação Web**. Mantenha o **Nome da Aplicação Web** dado e selecione a **Subscrição**, o **Grupo de Recursos** e o **Plano do Serviço de Aplicações**.  Clique em **Criar**.

4. Anote a propriedade **URL do Site**, na secção **Resumo**. Vamos referir-nos a este URL como o *ponto final do seu back-end* mais adiante neste tutorial. Clique em **Publicar**.

5. Depois de concluir o assistente, este publica a aplicação Web ASP.NET no Azure e, em seguida, inicia a aplicação no browser predefinido.  A aplicação estará visualizável no Serviço de Aplicações do Azure.

O URL utiliza o nome da aplicação Web que especificou anteriormente, com o formato http://<nome_da_aplicação>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
