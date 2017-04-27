

1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de o projeto ser criado, clique em **Adicionar Firebase à sua aplicação Android** e siga as instruções fornecidas.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na consola Firebase, clique na roda dentada do seu projeto e, em seguida, clique em **Definições do Projeto**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Clique no separador **Cloud Messaging** nas definições do projeto e copie o valor da **Chave do servidor** e do **ID do Remetente**. Estes valores serão utilizados posteriormente para configurar a política de acesso do hub de notificação e o controlador de notificação na aplicação.
