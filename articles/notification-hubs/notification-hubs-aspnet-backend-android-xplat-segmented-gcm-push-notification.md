---
title: "Notification Hubs notícias de última hora Tutorial - Android"
description: "Saiba como utilizar Hubs de notificação de barramento de serviço do Azure para enviar notificações de notícias de última para dispositivos Android."
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 76ec01c874fceedab7d76b2ef58e4b45b5489f58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar Notification Hubs para enviar notícias de última hora
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como utilizar Notification Hubs do Azure para difundir notificações de notícias de última para uma aplicação Android. Quando terminar, irá conseguir registar-se de que está interessado de categorias de notícias de última hora e receber notificações de push apenas para as categorias. Este cenário é um padrão comum para muitas aplicações onde notificações devem ser enviadas para grupos de utilizadores que tenham sido anteriormente declarado interesse nos mesmos, por exemplo, leitor de RSS, as aplicações para ventoinhas de música, etc.

Cenários de difusão estão ativados, incluindo um ou mais *etiquetas* quando criar um registo no hub de notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que registou da etiqueta irão receber a notificação. Uma vez que as etiquetas são simplesmente cadeias, tem de ser aprovisionados com antecedência. Para obter mais informações sobre etiquetas, consulte [encaminhamento de Hubs de notificação e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Pré-requisitos
Este tópico baseia-se a aplicação que criou no [introdução aos Hubs de notificação][get-started]. Antes de começar este tutorial, tem de ter já concluído [introdução aos Hubs de notificação][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção da categoria para a aplicação
O primeiro passo consiste em adicionar os elementos de IU para a atividade principal existente que permitem ao utilizador selecionar categorias para registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação for iniciada, um registo do dispositivo é criado no notification hub com as categorias selecionadas como etiquetas.

1. Abra o ficheiro res/layout/activity_main.xml e substitua o conteúdo com o seguinte:
   
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">
   
                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>
2. Abra o ficheiro res/values/strings.xml e adicione as seguintes linhas:
   
        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>
   
    A esquema de gráfico main_activity.xml deve agora ter o seguinte aspeto:
   
    ![][A1]
3. Agora, crie uma classe **notificações** no mesmo pacote como seu **MainActivity** classe.
   
        import java.util.HashSet;
        import java.util.Set;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;
   
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;
   
            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
   
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }
   
            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }
   
            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }
   
            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
   
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
   
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
   
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }
   
        }
   
    Esta classe utiliza o armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Também contém métodos para se registar para estas categorias.
4. No seu **MainActivity** classe remover os campos privados para **NotificationHub** e **GoogleCloudMessaging**, e adicione um campo para **notificações**:
   
        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;
5. Em seguida, no **onCreate** método, remova a inicialização do **hub** campo e o **registerWithNotificationHubs** método. Em seguida, adicione as seguintes linhas que inicializar uma instância do **notificações** classe. 

        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);

            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`e `HubListenConnectionString` já deve ser definido com o `<hub name>` e `<connection string with listen access>` marcadores de posição pelo nome do seu hub de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anteriormente.

    > [AZURE.NOTE] Porque as credenciais que são distribuídas com uma aplicação de cliente não são geralmente seguras, só deve de distribuir a chave de acesso escuta com a sua aplicação de cliente. Escutar permite o acesso não é possível modificar a sua aplicação para se registar para notificações, mas os registos existentes e não não possível enviar notificações. A chave de acesso total é utilizada num serviço protegidos back-end para envio de notificações e alterar os registos existentes.


1. Em seguida, adicione as seguintes importações e `subscribe` método para lidar com o botão de subscrever clique evento:
   
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;
   
        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();
   
            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");
   
            notifications.storeCategoriesAndSubscribe(categories);
        }
   
    Este método cria uma lista de categorias e utiliza o **notificações** etiquetas de classe para armazenar a lista no armazenamento local e registar correspondente com o notification hub. Quando são alteradas categorias, o registo é recriado com as categorias de novo.

A aplicação agora é capaz de armazenar um conjunto de categorias no armazenamento local no dispositivo e registar o notification hub sempre que o utilizador altera a seleção das categorias.

## <a name="register-for-notifications"></a>Registar-se as notificações
Estes passos registar com o hub de notificação no arranque utilizando as categorias que foram armazenadas no armazenamento local.

> [!NOTE]
> Porque pode alterar o registrationId atribuído pelo Google Cloud Messaging (GCM) em qualquer altura, deve registar para notificações frequentemente evitar falhas de notificação. Neste exemplo regista a notificação sempre que a aplicação for iniciada. Para aplicações que são executadas com frequência, mais do que uma vez por dia, pode provavelmente ignorar registo para preservar a largura de banda, se menos de um dia foi efectuada com êxito desde o registo anterior.
> 
> 

1. Adicione o seguinte código no final do **onCreate** método o **MainActivity** classe:
   
        notifications.subscribeToCategories(notifications.retrieveCategories());
   
    Isto certifica-se de que sempre que a aplicação for iniciada, obtém as categorias de armazenamento local e os pedidos de um registo para estas categorias. 
2. Em seguida, atualize o `onStart()` método o `MainActivity` classe da seguinte forma:
   
    @Overrideprotegido void onStart() {
   
        super.onStart();
        isVisible = true;
   
        Set<String> categories = notifications.retrieveCategories();
   
        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
   
    Isto atualiza a atividade principal, com base no estado das categorias guardados anteriormente.

A aplicação está agora concluída e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registar o notification hub sempre que o utilizador altera a seleção das categorias. Em seguida, iremos definir um back-end que pode enviar notificações de categoria para esta aplicação.

## <a name="sending-tagged-notifications"></a>Enviar notificações marcadas
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações
1. No Android Studio, crie a aplicação e inicie-o num dispositivo ou emulador.
   
    Tenha em atenção que a aplicação IU fornece um conjunto de mudanças de modo email que lhe permite escolher as categorias para subscrever.
2. Ativar uma ou mais categorias mudanças de modo email, em seguida, clique em **subscrever**.
   
    A aplicação converte as categorias selecionadas etiquetas e os pedidos de um novo registo de dispositivo para as etiquetas selecionados do hub de notificação. As categorias registadas são devolvidas e apresentadas numa notificação de alerta.
3. Envie uma notificação nova ao executar a aplicação de consola .NET.  Em alternativa, pode enviar notificações de modelo marcado utilizando o separador de depuração do notification hub no [Portal clássico do Azure].
   
    As notificações para as categorias selecionadas são apresentados como notificações de alerta.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a difusão notícias de última hora por categoria. Considere a concluir um dos seguintes tutoriais que realcem os outros cenários avançados de Hubs de notificação:

* [Utilizar Notification Hubs para difusão notícias de última localizada hora]
  
    Saiba como expandir a aplicação de notícias de última para ativar o envio de notificações localizadas.

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Utilizar Notification Hubs para difusão notícias de última localizada hora]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Portal clássico do Azure]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
