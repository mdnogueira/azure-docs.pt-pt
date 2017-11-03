## <a name="webapi-project"></a>End WebAPI projeto
1. No Visual Studio, abra o **AppBackend** projeto que criou no **notificam os utilizadores** tutorial.
2. No Notifications.cs, substitua o inteiro **notificações** classe com o seguinte código. Não se esqueça de substituir os marcadores de posição pela cadeia de ligação (com acesso total) para o notification hub e o nome do hub. Pode obter estes valores no [Portal clássico do Azure](http://manage.windowsazure.com). Este módulo agora representa as diferentes notificações seguras que serão enviadas. Uma implementação completa, as notificações serão armazenadas numa base de dados; de simplicidade, neste caso, iremos armazená-las na memória.
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. No NotificationsController.cs, substitua o código no interior do **NotificationsController** classe definição com o seguinte código. Este componente implementa uma forma do dispositivo obter a notificação de forma segura e também fornece uma forma (para efeitos deste tutorial) para acionar um push seguro para os seus dispositivos. Tenha em atenção que ao enviar a notificação para o hub de notificação, apenas Enviámos uma notificação não processada, com o ID de notificação (e não existem mensagens real):
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


Tenha em atenção que o `Post` método agora não enviar uma notificação de alerta. Envia uma notificação em bruto que contém apenas o ID de notificação e não a qualquer conteúdo confidencial. Além disso, certifique-se comente a operação de envio para as plataformas que não tem credenciais configuradas no seu hub de notificação, conforme irá causar erros.

1. Agora iremos novamente implementar esta aplicação para um Web site do Azure para o tornam acessível a partir de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
2. Selecione o Web site do Azure como o destino da publicação. Iniciar sessão com a sua conta do Azure e selecione um Web site existente ou novo e anote o **URL de destino** propriedade no **ligação** separador. Vamos referir-nos a este URL como o *ponto final do seu back-end* mais adiante neste tutorial. Clique em **Publicar**.

