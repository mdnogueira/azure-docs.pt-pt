---
title: "Integração do Android SDK do Azure Mobile Engagement"
description: "Mais recentes atualizações e procedimentos para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 26ba47b19f3a503693d60d344ad39b9eba74fe99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Como integrar o Engagement alcance no Android
> [!IMPORTANT]
> Tem de seguir o procedimento de integração descrito num documento do Android a como integrar o Engagement antes de seguir este guia.
> 
> 

## <a name="standard-integration"></a>Integração padrão

Copie os ficheiros de recursos de alcance do SDK no projeto:

* Copie os ficheiros do `res/layout` pasta fornecidas com o SDK para o `res/layout` pasta da sua aplicação.
* Copie os ficheiros do `res/drawable` pasta fornecidas com o SDK para o `res/drawable` pasta da sua aplicação.

Editar o `AndroidManifest.xml` ficheiro:

* Adicione a secção seguinte (entre o `<application>` e `</application>` etiquetas):
  
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/plain" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/html" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
              <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
              <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
          </activity>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
              <action android:name="android.intent.action.BOOT_COMPLETED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
          </receiver>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
              <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
          </receiver>
* Tem esta permissão de reprodução de notificações do sistema que não foram clicadas no arranque (caso contrário, serão mantidos no disco, mas não serão apresentados já, realmente tem de incluir isto).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Especificar um ícone utilizado para notificações (tanto no como aplicação sistema aqueles) ao copiar e editar a secção seguinte (entre o `<application>` e `</application>` etiquetas):
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Esta secção se **obrigatório** se planeia utilizar notificações de sistema ao criar campanhas de alcance. Android impede que as notificações do sistema sem ícones a ser mostrada. Por isso, se omitir esta secção, os utilizadores finais não será possível para recebê-las.
> 
> 

* Se criar campanhas de notificações do sistema utilizando a visão, terá de adicionar as seguintes permissões (depois do `</application>` tag) se estiverem em falta:
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * No Android M e se a aplicação está direcionada para o nível de API do Android 23 ou superior, ``WRITE_EXTERNAL_STORAGE`` permissão requer aprovação do utilizador. Leia [nesta secção](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* Para as notificações do sistema pode também especificar nas campanhas de alcance se o dispositivo deve anel e/ou Vibre. Para que esta funcione, tem de certificar-se de que declarado a permissão seguinte (depois do `</application>` tag):
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Sem esta permissão, Android, impede que as notificações do sistema de que está a ser apresentado se tiver selecionado o toque ou a opção de vibrate no Gestor de campanhas de alcance.

## <a name="native-push"></a>Push nativo
Agora que configurou o módulo de alcance, terá de configurar o push nativo para conseguir recebe campanhas no dispositivo.

Suportamos dois serviços no Android:

* Dispositivos do Google Play: Utilize [Google Cloud Messaging] seguindo o [como integrar o GCM guia Engagement](mobile-engagement-android-gcm-integrate.md) guia.
* Dispositivos de Amazon: Utilize [Amazon Device Messaging] seguindo o [como ADM integrar com o guia de envolvimento](mobile-engagement-android-adm-integrate.md) guia.

Se pretender dispositivos Amazon e do Google Play, o possível ter tudo dentro de 1 AndroidManifest.xml/APK para o desenvolvimento de destino. Mas, ao submeter a Amazon, poderá rejeitar a aplicação de se encontrar o código do GCM.

Deve utilizar vários APKs nesse caso.

**A aplicação está agora pronta para receber e apresentar campanhas de alcance!**

## <a name="how-to-handle-data-push"></a>Como processar push de dados
### <a name="integration"></a>Integração
Se pretender que a aplicação para poder receber alcance pushes de dados, tem de criar uma classe secundárias de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` e referencie-na `AndroidManifest.xml` ficheiro (entre o `<application>` e/ou `</application>` etiquetas):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Em seguida, pode substituir o `onDataPushStringReceived` e `onDataPushBase64Received` chamadas de retorno. Segue-se um exemplo:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Categoria
O parâmetro de categoria é opcional ao criar uma campanha Push de dados e permite-lhe dados de filtro pushes. Isto é útil se tiver vários recetores difusão processar tipos diferentes de pushes de dados, ou se pretender emitir diferentes tipos de `Base64` dados e pretender identificar o respetivo tipo antes de a análise-los.

### <a name="callbacks-return-parameter"></a>Parâmetro de retorno dos chamadas de retorno
Eis algumas diretrizes para processar corretamente o parâmetro de retorno de `onDataPushStringReceived` e `onDataPushBase64Received`:

* Deverá devolver um recetor difusão `null` numa chamada de retorno se não souber como processar um push de dados. Deve utilizar a categoria para determinar se o recetor difusão deve processar o push de dados ou não.
* Um recetor difusão deverá devolver `true` numa chamada de retorno se aceita o push de dados.
* Um recetor difusão deverá devolver `false` numa chamada de retorno se reconhece o push de dados, mas elimina-lo por qualquer motivo. Por exemplo, devolver `false` quando os dados recebidos são inválidos.
* Se um recetor devolve de difusão `true` enquanto outra um devolve `false` para o mesmo push de dados, o comportamento não está definido, que nunca deve fazer.

O tipo de retorno é utilizado apenas para as estatísticas de alcance:

* `Replied`é incrementado se um dos recetores difusão devolveu um `true` ou `false`.
* `Actioned`é incrementado apenas se um dos recetores difusão devolveu `true`.

## <a name="how-to-customize-campaigns"></a>Como personalizar campanhas
Para personalizar as campanhas, pode modificar as esquemas fornecidas no SDK do alcance.

Deve manter todos os identificadores utilizados nas esquemas e manter os tipos de vistas que utilizam um identificador, especialmente para vistas de texto e vistas de imagem. Algumas vistas apenas são utilizadas para ocultar ou mostrar áreas para que o respetivo tipo poderá ser alterado. Verifique o código de origem, se pretender alterar o tipo de vista das esquemas fornecidas.

### <a name="notifications"></a>Notificações
Existem dois tipos de notificações: notificações de sistema e na aplicação que utilizam ficheiros de esquema diferente.

#### <a name="system-notifications"></a>Notificações do sistema
Para personalizar as notificações do sistema tem de utilizar o **categorias**. Pode saltar para [categorias](#categories).

#### <a name="in-app-notifications"></a>Nas notificações na aplicação
Por predefinição, uma notificação na aplicação é uma Vista dinâmica é adicionada à interface de utilizador de atividade atual graças ao método Android `addContentView()`. Isto denomina-se numa sobreposição de notificação. Sobreposição de notificação é ótimos para uma integração rápida porque é necessário modificar qualquer esquema na sua aplicação.

Para modificar o aspeto da sua sobrepõe de notificação, pode simplesmente modificar o ficheiro `engagement_notification_area.xml` às suas necessidades.

> [!NOTE]
> O ficheiro `engagement_notification_overlay.xml` é o que é utilizado para criar uma sobreposição de notificação inclui o ficheiro `engagement_notification_area.xml`. Também pode personalizá-lo de acordo com as suas necessidades (como idêntica à área de notificação na sobreposição de posicionamento).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Incluir o esquema de notificação como parte de um esquema de atividade
As sobreposições são ótimos para uma integração rápida, mas podem ser inconveniente ou ter efeitos secundários em casos especiais. O sistema de sobreposição pode ser personalizado ao nível de uma atividade, facilitando a evitar efeitos secundários para atividades especiais.

Pode optar por incluir nosso esquema de notificação no seu esquema existente graças ao Android **incluem** instrução. Segue-se um exemplo de um modificado `ListActivity` esquema que contém apenas um `ListView`.

**Antes de integração do Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Após a integração do Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Neste exemplo adicionámos um contentor principal, desde que o esquema original utilizado uma vista de lista, como o elemento de nível superior. Adicionámos também `android:layout_weight="1"` para poder adicionar uma vista abaixo uma vista de lista configurada com `android:layout_height="fill_parent"`.

SDK de alcançar o Engagement Deteta automaticamente que o esquema de notificação está incluído nesta atividade e não irá adicionar uma sobreposição para esta atividade.

> [!TIP]
> Se utilizar um ListActivity na sua aplicação, uma sobreposição de alcance visível irá impedir a agir os clicado já itens na vista de lista. Este é um problema conhecido. Para contornar este problema sugerimos que lhe incorporar o esquema de notificação no seu próprio esquema de atividade de lista, como no exemplo anterior.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Desativar a notificação da aplicação por atividade
Se não quiser sobreposição para ser adicionado a sua atividade e, se não incluir o esquema de notificação no seu próprio esquema, pode desativar a sobreposição para esta atividade no `AndroidManifest.xml` adicionando um `meta-data` secção como no exemplo seguinte:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Categorias
Quando modifica as esquemas fornecidas, modifique o aspeto de todas as suas notificações. Categorias permitem-lhe definir o aspeto que tem de destino várias (possivelmente comportamentos) para notificações. É possível especificar uma categoria quando cria uma campanha de alcance. Tenha em atenção que categorias também permitem-lhe personalizar anúncios e inquéritos, que é descrita mais à frente neste documento.

Para registar um processador de categoria para as notificações, tem de adicionar uma chamada quando a aplicação é inicializada.

> [!IMPORTANT]
> Leia o aviso sobre o atributo de android: processo \<android-sdk-engagement-process\> na como integrar o Engagement no tópico Android antes de continuar.
> 
> 

O exemplo seguinte assume confirmadas o aviso anterior e utilizar uma classe secundárias de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

O `MyNotifier` objeto é a implementação do processador de categoria de notificação. É a uma implementação do `EngagementNotifier` interface ou uma classe de subchaves da implementação predefinida: `EngagementDefaultNotifier`.

Tenha em atenção que o mesmo notifier pode processar várias categorias, pode registá-los como esta:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Para substituir a implementação de categoria predefinida, pode registar a sua implementação, como no exemplo seguinte:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

A categoria atual utilizada um processador é transmitida como um parâmetro na maioria dos métodos que pode substituir no `EngagementDefaultNotifier`.

Ser transmitido como um `String` parâmetro ou indiretamente num `EngagementReachContent` objeto que tenha um `getCategory()` método.

Pode alterar a maior parte do processo de criação de notificação ao redefinir métodos em `EngagementDefaultNotifier`, para personalização mais avançada não hesite veja a documentação técnica e o código de origem.

##### <a name="in-app-notifications"></a>Nas notificações na aplicação
Se pretender utilizar esquemas alternativas para uma categoria específica, pode implementar este como no exemplo seguinte:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exemplo de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Como pode ver, o identificador de vista de sobreposição é diferente daquela padrão. É importante que cada esquema de utiliza um identificador exclusivo para as Sobreposições.

**Exemplo de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Como pode ver, o identificador de vista da área de notificação é diferente daquela padrão. É importante que cada esquema utiliza um identificador exclusivo de áreas de notificação.

Neste exemplo simples da categoria faz com que a aplicação (ou na aplicação) notificações apresentadas na parte superior do ecrã. Iremos não mudou os identificadores padrão utilizados na área de notificação de si próprio.

Se pretender alterar que, terá de redefinir o `EngagementDefaultNotifier.prepareInAppArea` método. É recomendado para ver a documentação técnica e o código fonte da `EngagementNotifier` e `EngagementDefaultNotifier` se pretender que este nível de personalização avançada.

##### <a name="system-notifications"></a>Notificações do sistema
Ao alargar `EngagementDefaultNotifier`, pode substituir `onNotificationPrepared` para alterar a notificação que foi preparada pela implementação predefinido.

Por exemplo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Este exemplo faz com que uma notificação do sistema para um conteúdo que está a ser apresentado como um evento em curso quando é utilizada a categoria "em curso".

Se pretender criar o `Notification` de objeto a partir do zero, pode regressar `false` para o método e a chamada `notify` por si no `NotificationManager`. Nesse caso é importante que mantenha um `contentIntent`, um `deleteIntent` e o identificador de notificação utilizados pelo `EngagementReachReceiver`.

Eis um exemplo correto de uma implementação deste tipo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Anúncios apenas de notificação
A gestão, clique numa notificação de anúncio só pode ser personalizado através da substituição `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` para modificar o preparado `Intent`. Através deste método permite-lhe otimizar facilmente os sinalizadores.

Por exemplo, para adicionar o `SINGLE_TOP` sinalizador:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Para os utilizadores do Engagement legados, tenha em atenção que as notificações do sistema sem ação URL agora inicia a aplicação se foi em segundo plano, para que este método pode ser chamado com um anúncio sem o URL de ação. Deve considerar que ao personalizar a intenção.

Também pode implementar `EngagementNotifier.executeNotifAnnouncementAction` a partir do zero.

##### <a name="notification-life-cycle"></a>Ciclo de vida de notificação
Quando a categoria de predefinido a utilizar, alguns métodos de ciclo de vida são denominados no `EngagementReachInteractiveContent` de objeto para estatísticas de relatório e atualizar o estado da campanha:

* Quando a notificação é apresentada na aplicação ou colocar na barra de estado, o `displayNotification` método é denominado (que comunica estatísticas) por `EngagementReachAgent` se `handleNotification` devolve `true`.
* Se a notificação é dispensada, o `exitNotification` se chama o método, é reportada estatística e campanhas seguintes agora podem ser processadas.
* Se a notificação é clicada, `actionNotification` é chamado, é reportada estatística e a intenção associada é iniciada.

Se a implementação de `EngagementNotifier` ignora o comportamento predefinido, terá de chamar estes métodos de ciclo de vida por si. Os exemplos seguintes mostram alguns casos em que o comportamento predefinido é ignorado:

* Não expande `EngagementDefaultNotifier`, por exemplo, implementado o processamento de categoria do zero.
* Para as notificações do sistema, overrode o `onNotificationPrepared` e é modificado `contentIntent` ou `deleteIntent` no `Notification` objeto.
* Para notificações na aplicação, overrode `prepareInAppArea`, lembre-se de que, pelo menos, mapear `actionNotification` para um dos seus U.I controla.

> [!NOTE]
> Se `handleNotification` gera uma exceção, o conteúdo é eliminada e `dropContent` é chamado. Isto é reportado no estatísticas e campanhas seguintes agora podem ser processadas.
> 
> 

### <a name="announcements-and-polls"></a>Anúncios e inquéritos
#### <a name="layouts"></a>Esquemas
Pode modificar o `engagement_text_announcement.xml`, `engagement_web_announcement.xml` e `engagement_poll.xml` ficheiros para personalizar os anúncios de texto, anúncios web e inquéritos.

Estes ficheiros partilham dois esquemas comuns para a área de título e a área do botão. O esquema para o título é `engagement_content_title.xml` e utiliza o ficheiro de drawable eponymous para a imagem de fundo. O esquema para os botões de ação e saída é `engagement_button_bar.xml` e utiliza o ficheiro de drawable eponymous para a imagem de fundo.

Em inquéritos, o esquema de pergunta e as opções são dinamicamente cheia com várias vezes o `engagement_question.xml` ficheiro de esquema para as perguntas e `engagement_choice.xml` ficheiro para as escolhas.

#### <a name="categories"></a>Categorias
##### <a name="alternate-layouts"></a>Esquemas alternativas
Como as notificações, categoria a campanha pode ser utilizada com esquemas alternativas para os anúncios e inquéritos.

Por exemplo, para criar uma categoria para um anúncio de texto, pode expandir `EngagementTextAnnouncementActivity` e referencie-o a `AndroidManifest.xml` ficheiro:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Tenha em atenção que a categoria no filtro intenção é utilizada para fazer a diferença com a atividade de anúncio de predefinição.

O SDK do alcance utiliza o sistema intenção de atividade certa para uma categoria específica de resolver e ficarem volta na categoria do predefinida se a resolução falhou.

Terá de implementar `MyCustomTextAnnouncementActivity`, se pretender alterar o esquema (mas manter os mesmo ver identificadores), apenas terá de definir a classe, como no exemplo seguinte:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Para substituir a categoria de predefinição de anúncios de texto, basta substituir `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` pela sua implementação.

Anúncios Web e inquéritos podem ser personalizados de forma semelhante.

Para anúncios web permite-lhe expandir `EngagementWebAnnouncementActivity` e declarar a atividade no `AndroidManifest.xml` como no exemplo seguinte:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Para consultar pode expandir a `EngagementPollActivity` e declarar no `AndroidManifest.xml` como no exemplo seguinte:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementação a partir do zero
Pode implementar categorias para as atividades de anúncio (e consulta) sem expandir um do `Engagement*Activity` classes fornecidas pelo SDK do alcance. Por exemplo é útil se pretender definir um esquema que não utiliza as mesmas vistas como as esquemas padrão.

Como para personalização avançada de notificação, recomenda-para ver o código fonte da implementação padrão.

Seguem-se alguns aspetos a ter em mente: alcance iniciará a atividade com um objetivo específico (correspondentes ao filtro de intenção) juntamente com um parâmetro adicional que é o identificador de conteúdo.

Para obter o objeto do conteúdo que contém os campos especificados ao criar a campanha no web site para fazer isto:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Para estatísticas, deve comunicar o conteúdo é apresentado no `onResume` eventos:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Em seguida, não se esqueça de chamar o `actionContent(this)` ou `exitContent(this)` no objeto de conteúdo antes da atividade entra em segundo plano.

Se não chame o `actionContent` ou `exitContent`, não serão enviadas estatísticas (ou seja, sem análises sobre a campanha) e mais importante ainda, as campanhas seguintes não serão notificadas enquanto o processo de aplicação não for reiniciado.

Orientação ou outras alterações de configuração podem tornar o código tricky determinar se a atividade entra em segundo plano ou não, a implementação padrão certifica-se do conteúdo é reportado como terminado se os utilizadores saírem da atividade (um premindo `HOME`ou `BACK`), mas não se altera a orientação.

Segue-se a parte da implementação interessante:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Como pode ver, se chamar `actionContent(this)` depois de concluída a atividade, `exitContent(this)` pode ser chamado em segurança sem ter qualquer efeito.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
