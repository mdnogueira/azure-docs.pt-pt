## <a name="create-the-webapi-project"></a>Criar o projeto de end WebAPI
As secções seguintes abordam a criação de um novo end de ASP.NET WebAPI de back-end. Este processo tem três objetivos principais:

* **Autentique clientes**: Adiciona um processador de mensagem mais tarde para autenticar pedidos de cliente e associar o utilizador com o pedido.

* **Registar para notificações utilizando o end WebAPI back-end**: adicionar um controlador para processar novos registos para um dispositivo de cliente receber notificações. O nome de utilizador autenticado é automaticamente adicionado ao registo como um [tag](https://msdn.microsoft.com/library/azure/dn530749.aspx).

* **Enviar notificações para os clientes**: também adicionar um controlador para fornecer uma forma dos utilizadores acionar um push seguro de dispositivos e clientes associados a etiqueta. 

Crie novo end de ASP.NET WebAPI back-end da seguinte forma: 

> [!IMPORTANT]
> Se estiver a utilizar o Visual Studio 2015 ou anterior, antes de começar este tutorial, certifique-se de que instalou a versão mais recente do Gestor de pacotes NuGet para Visual Studio. 
>
>Para confirmar, inicie o Visual Studio. No **ferramentas** menu, selecione **extensões e atualizações**. Procurar **Gestor de pacotes NuGet** na sua versão do Visual Studio e certifique-se de que tem a versão mais recente. Se a sua versão não é a versão mais recente, desinstale-a e, em seguida, reinstalar o Gestor de pacotes NuGet.
 
![][B4]

> [!NOTE]
> Verifique que tem instalado o [Azure SDK](https://azure.microsoft.com/downloads/) para Visual Studio para implementação de Website.
> 
> 

1. Inicie o Visual Studio ou o Visual Studio Express. 

2. Selecione **Explorador de servidores**e inicie sessão na sua conta do Azure. Para criar os recursos do web site na sua conta, tem de ser assinado.

3. No Visual Studio, selecione **ficheiro** > **novo** > **projeto**, expanda **modelos**, expanda **Visual c#**e, em seguida, selecione **Web** e **aplicação Web ASP.NET**.

4. No **nome** caixa, escreva **AppBackend**e, em seguida, selecione **OK**. 
   
    ![Janela novo projeto][B1]

5. No **novo projeto ASP.NET** janela, selecione o **Web API** caixa de verificação e, em seguida, selecione **OK**.
   
    ![A janela novo projeto ASP.NET][B2]

6. No **configurar aplicação da Web do Microsoft Azure** janela, selecione uma subscrição e, em seguida, no **plano do App Service** lista, efetue um dos seguintes procedimentos:

    * Selecione um plano de serviço de aplicação que já criou. 
    * Selecione **criar um novo plano do app service**e, em seguida, criar um. 
    
  Não precisa de uma base de dados para este tutorial. Depois de selecionar o plano de serviço de aplicações, selecione **OK** para criar o projeto.
   
    ![A janela de configurar a aplicação da Web do Microsoft Azure][B5]

## <a name="authenticate-clients-to-the-webapi-back-end"></a>Autentique clientes até ao fim de back-end WebAPI
Nesta secção, vai criar uma nova classe de processador de mensagens com o nome **AuthenticationTestHandler** para o novo back-end. Esta classe é derivada de [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) e adicionados como um processador de mensagens, de modo a que pode processar todos os pedidos que provenientes em back-end. 

1. No Explorador de soluções, clique com botão direito do **AppBackend** projeto, selecione **adicionar**e, em seguida, selecione **classe**. 
 
2. Nome da nova classe **AuthenticationTestHandler.cs**e, em seguida, selecione **adicionar** para gerar a classe. Esta classe autentica utilizadores utilizando *autenticação básica* de simplicidade. A aplicação pode utilizar qualquer esquema de autenticação.

3. No AuthenticationTestHandler.cs, adicione as instruções `using` seguintes:
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Text;
        using System.Threading.Tasks;

4. No AuthenticationTestHandler.cs, substitua o `AuthenticationTestHandler` classe definição com o seguinte código: 
   
    O processador autorizará o pedido quando as seguintes três condições são verdadeiras:
   
   * O pedido inclui um *autorização* cabeçalho. 
   * O pedido utiliza a autenticação *básica*. 
   * A cadeia de nome de utilizador e a cadeia de palavra-passe são a mesma cadeia.
     
  Caso contrário, o pedido será rejeitado. Esta não é uma verdadeira abordagem de autenticação e autorização. É apenas um exemplo muito simples para este tutorial.
     
  Se a mensagem de pedido é autenticada e autorizada por `AuthenticationTestHandler`, o utilizador de autenticação básica está ligado ao pedido atual no [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Informações do utilizador no HttpContext serão utilizadas por outro controlador (RegisterController) mais tarde para adicionar um [tag](https://msdn.microsoft.com/library/azure/dn530749.aspx) para o pedido de registo de notificação.
     
       public class AuthenticationTestHandler : DelegatingHandler
       {
           protected override Task<HttpResponseMessage> SendAsync(
           HttpRequestMessage request, CancellationToken cancellationToken)
           {
               var authorizationHeader = request.Headers.GetValues("Authorization").First();
     
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
     > Nota de segurança: O `AuthenticationTestHandler` classe não fornecer autenticação verdadeira. É utilizada apenas para imitar a autenticação básica e não é segura. Tem de implementar um mecanismo de autenticação segura nos seus serviços e aplicações de produção.                
     > 
     > 
5. Para registar o processador de mensagens, adicione o seguinte código no final do `Register` método o **App_Start/WebApiConfig.cs** classe:
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());

6. Guarde as alterações.

## <a name="register-for-notifications-by-using-the-webapi-back-end"></a>Registar para notificações utilizando o end WebAPI back-end
Nesta secção, adicione um novo controlador para o end WebAPI back-end para processar pedidos para registar um dispositivo para as notificações e um utilizador através da biblioteca de cliente para os notification hubs. O controlador adiciona uma etiqueta de utilizador para o utilizador que foi autenticado e ligado à HttpContext por `AuthenticationTestHandler`. O formato da cadeia será `"username:<actual username>"`.

1. No Explorador de soluções, clique com botão direito do **AppBackend** projeto e, em seguida, selecione **gerir pacotes NuGet**.

2. No painel esquerdo, selecione **Online** e, em seguida, no **pesquisa** caixa, escreva **notificationhubs**.

3. Na lista de resultados, selecione **os Notification Hubs do Microsoft Azure**e, em seguida, selecione **instalar**. Concluir a instalação e, em seguida, feche a janela do Gestor de pacotes NuGet.
   
    Esta ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure mediante a utilização do <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Pacote NuGet Microsoft.Azure.Notification Hubs</a>.

4. Crie um novo ficheiro de classe que representa a ligação com o hub de notificação que é utilizada para enviar notificações. No Explorador de soluções, clique com botão direito do **modelos** pasta, selecione **adicionar**e, em seguida, selecione **classe**. Nome da nova classe **Notifications.cs**e, em seguida, selecione **adicionar** para gerar a classe. 
   
    ![A janela de adicionar novo Item][B6]

5. Em Notifications.cs, adicione a instrução `using` à parte superior do ficheiro:
   
        using Microsoft.Azure.NotificationHubs;

6. Substitua o `Notifications` definição com o seguinte código de classe e substitua os marcadores de dois posição com a cadeia de ligação (com acesso total) para o notification hub e o nome do hub (disponível em [portal clássico do Azure](http://manage.windowsazure.com)):
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. Em seguida, crie um novo controlador com o nome **RegisterController**. No Explorador de soluções, clique com botão direito do **controladores** pasta, selecione **adicionar**e, em seguida, selecione **controlador**. 

8. Selecione **controlador no Web API 2 - vazio**e, em seguida, selecione **adicionar**.
   
    ![A janela de adicionar andaime][B7]
   
9. No **nome do controlador** caixa, escreva **RegisterController** para designar a classe novo e, em seguida, selecione **adicionar**.

    ![A janela de adicionar controlador][B8]

10. No RegisterController.cs, adicione as instruções `using` seguintes:
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

11. Adicione o código seguinte dentro da definição de classe `RegisterController`. Tenha em atenção que neste código, iremos adicionar uma etiqueta de utilizador para o utilizador que está ligado a HttpContext. O utilizador foi autenticado e ligado à HttpContext pelo filtro de mensagem que adicionámos, `AuthenticationTestHandler`. Também pode adicionar verificações opcionais para confirmar que o utilizador tem direitos para se registar nas etiquetas pedidas.
   
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
12. Guarde as alterações.

## <a name="send-notifications-from-the-webapi-back-end"></a>Enviar notificações a partir do end WebAPI back-end
Nesta secção, pode adiciona um novo controlador que expõe uma forma de dispositivos de cliente enviar uma notificação. A notificação baseia-se a etiqueta de nome de utilizador que utiliza a biblioteca de gestão de serviço do Azure Notification Hubs no final de back-end de ASP.NET WebAPI.

1. Criar outro controlador de novo com o nome **NotificationsController** da mesma forma que criou **RegisterController** na secção anterior.

2. No NotificationsController.cs, adicione as instruções `using` seguintes:
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. Adicione o seguinte método para o **NotificationsController** classe:
   
    Este código envia um tipo de notificação que se baseia no serviço de notificação de plataforma (PNS) `pns` parâmetro. O valor de `to_tag` é utilizado para definir a etiqueta *nome de utilizador* na mensagem. Esta etiqueta tem de corresponder a uma etiqueta de nome de utilizador de um registo de hub de notificação ativo. A mensagem da notificação é retirada do corpo do pedido POST e formatada para o PNS de destino. 
   
    Consoante o PNS que utilizam os seus dispositivos suportados para receber notificações, as notificações são suportadas por uma variedade de formatos. Por exemplo, nos dispositivos com Windows, poderá utilizar um [alerta notificação com WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) que diretamente não é suportada pela noutro PNS. Uma instância, o back-end tem de formatar a notificação para uma notificação suportada para o PNS dos dispositivos que pretende suportar. Em seguida, utilize o adequada de envio de API no [NotificationHubClient classe](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx).
   
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

4. Para executar a aplicação e certifique-se a precisão do seu trabalho até ao momento, selecione o **F5** chave. A aplicação abre um browser e for apresentado na home page do ASP.NET. 

## <a name="publish-the-new-webapi-back-end"></a>Publicar novo end WebAPI back-end
Em seguida, implementar a aplicação para um Web site do Azure para que seja acessível a partir de todos os dispositivos. 

1. Clique com botão direito do **AppBackend** projeto e, em seguida, selecione **publicar**.

2. Selecione **App Service do Microsoft Azure** como destino da publicação e, em seguida, selecione **publicar**.  
    Abre a janela criar App Service. Aqui, pode criar todos os recursos do Azure necessários para executar a aplicação web do ASP.NET no Azure.

    ![O mosaico do App Service do Microsoft Azure][B15]

3. No **criar App Service** janela, selecione a sua conta do Azure. Selecione **alterar tipo** > **aplicação Web**. Mantenha a predefinição **nome da aplicação Web**e, em seguida, selecione o **subscrição**, **grupo de recursos**, e **plano do App Service**. 

4. Selecione **Criar**.

5. Anote a propriedade **URL do Site**, na secção **Resumo**. Este URL é sua *ponto final de back-end* posterior no tutorial. 

6. Selecione **publicar**.

Após concluir o assistente, publica a aplicação web do ASP.NET para o Azure e, em seguida, abre-se a aplicação no browser predefinido.  A aplicação é visualizável em serviços de aplicações do Azure.

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
