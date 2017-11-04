1. No seu **aplicação** do projeto, abra o ficheiro `AndroidManifest.xml`. No código nos dois passos, substitua  *`**my_app_package**`*  com o nome do pacote de aplicação para o seu projeto. Este é o valor da `package` atributo o `manifest` tag.
2. Adicione as seguintes permissões de novo após existente `uses-permission` elemento:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. Adicione o seguinte código após o `application` tag de início:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. Abra o ficheiro *ToDoActivity.java*e adicione as seguintes declarações de importação:

        import com.microsoft.windowsazure.notifications.NotificationsManager;
5. Adicione a seguinte variável privada à classe. Substitua  *`<PROJECT_NUMBER>`*  com o número de projeto atribuído pelo Google à sua aplicação no procedimento anterior.

        public static final String SENDER_ID = "<PROJECT_NUMBER>";
6. Altere a definição de *MobileServiceClient* de **privada** para **estáticos públicos**, pelo que agora aspeto:

        public static MobileServiceClient mClient;
7. Adicione uma nova classe para processar as notificações. No Explorador de projeto, abra o **src** > **principal** > **java** nós e o botão direito no nó de nome do pacote. Clique em **novo**e, em seguida, clique em **classe Java**.
8. No **nome**, tipo `MyHandler`e, em seguida, clique em **OK**.

    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)

9. No ficheiro MyHandler, substitua a declaração de classe com:

        public class MyHandler extends NotificationsHandler {
10. Adicione as seguintes declarações de importação para o `MyHandler` classe:

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
11. Em seguida, adicione este membro para o `MyHandler` classe:

        public static final int NOTIFICATION_ID = 1;
12. No `MyHandler` classe, adicione o seguinte código para ignorar o **onRegistered** método, que regista o dispositivo com o hub de notificação de serviço móvel.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
           super.onRegistered(context, gcmRegistrationId);

           new AsyncTask<Void, Void, Void>() {

               protected Void doInBackground(Void... params) {
                   try {
                       ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                       return null;
                   }
                   catch(Exception e) {
                       // handle error                
                   }
                   return null;              
               }
           }.execute();
       }
13. No `MyHandler` classe, adicione o seguinte código para ignorar o **onReceive** método, o que faz com que a notificação a apresentar quando é recebido.

        @Override
        public void onReceive(Context context, Bundle bundle) {
               String msg = bundle.getString("message");

               PendingIntent contentIntent = PendingIntent.getActivity(context,
                       0, // requestCode
                       new Intent(context, ToDoActivity.class),
                       0); // flags

               Notification notification = new NotificationCompat.Builder(context)
                       .setSmallIcon(R.drawable.ic_launcher)
                       .setContentTitle("Notification Hub Demo")
                       .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                       .setContentText(msg)
                       .setContentIntent(contentIntent)
                       .build();

               NotificationManager notificationManager = (NotificationManager)
                       context.getSystemService(Context.NOTIFICATION_SERVICE);
               notificationManager.notify(NOTIFICATION_ID, notification);
       }
14. Novamente no ficheiro TodoActivity.java, atualize o **onCreate** método o *ToDoActivity* classe para registar a classe de processador de notificação. Certifique-se adicionar este código após o *MobileServiceClient* ser instanciado.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    A aplicação agora é atualizada para suportar notificações push.
