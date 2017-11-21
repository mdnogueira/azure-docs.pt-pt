1. No seu **aplicação** do projeto, abra o ficheiro `AndroidManifest.xml`. Adicione o seguinte código após o `application` tag de início:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Abra o ficheiro `ToDoActivity.java`e efetuar os seguintes alterações:

    - Adicione declarações de importação:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Altere a definição de `MobileServiceClient` de **privada** para **estática privada**, pelo que agora aspeto:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Adicionar `registerPush` método:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Atualização do **onCreate** método o `ToDoActivity` classe. Certifique-se adicionar este código após o `MobileServiceClient` ser instanciado.

        ```java
        registerPush();
        ```

3. Adicione uma nova classe para processar as notificações. No Explorador de projeto, abra o **aplicação** > **java** > **your-projeto-espaço de nomes** nós e o botão direito no nó de nome do pacote. Clique em **novo**e, em seguida, clique em **classe Java**. No nome, escreva `ToDoMessagingService`e, em seguida, clique em OK. Em seguida, substitua a declaração de classe com:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Adicione outra classe para lidar com atualizações de token. Criar `ToDoInstanceIdService` java de classe e substitua a declaração de classe com:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

A aplicação agora é atualizada para suportar notificações push.
