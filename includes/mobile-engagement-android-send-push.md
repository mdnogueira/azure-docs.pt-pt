
### <a name="update-manifest-file-to-enable-notifications"></a>Atualizar ficheiro de manifesto para ativar notificações
Copie os recursos de mensagens na aplicação abaixo para o Manifest.xml entre as etiquetas `<application>` e `</application>`.

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

### <a name="specify-an-icon-for-notifications"></a>Especificar um ícone para as notificações
Cole o seguinte fragmento XML no Manifest.xml entre as etiquetas `<application>` e `</application>`.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Este procedimento permite definir o ícone que é apresentado nas notificações do sistema e nas notificações na aplicação. É opcional para as notificações na aplicação, mas é obrigatório para as notificações do sistema. O Android rejeitará as notificações do sistema com ícones inválidos.

Confirme que está a utilizar um ícone que existe numa das pastas **drawable** (como ``engagement_close.png``). A pasta **mipmap** não é suportada.

> [!NOTE]
> Não deve utilizar o ícone do **iniciador**. Tem uma resolução diferente e está normalmente nas pastas mipmap, que não são suportadas.
> 
> 

Para aplicações reais, pode utilizar um ícone que seja adequado para as notificações de acordo com as [Diretrizes de conceção do Android](http://developer.android.com/design/patterns/notifications.html).

> [!TIP]
> Para garantir que utiliza as resoluções de ícones corretas, pode ver [estes exemplos](https://www.google.com/design/icons).
> Desloque o ecrã para baixo até à secção **Notificação**, clique num ícone e, em seguida, em `PNGS` para transferir o conjunto de desenho de ícones. Pode ver que pastas drawable e com que resolução deve utilizar para cada versão do ícone.
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>Permitir que a aplicação receba notificações push do GCM
1. Cole o seguinte no Manifest.xml entre as etiquetas `<application>` e `</application>`, depois de substituir a **ID do Remetente** obtida na consola do projeto Firebase. O \n é intencional. Por isso, confirme que termina o número do projeto com ele.
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. Cole o código abaixo no Manifest.xml entre as etiquetas `<application>` e `</application>`. Substitua o nome do pacote <Your package name>.
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>
3. Adicione o último conjunto de permissões que estão realçadas antes da etiqueta `<application>`. Substitua `<Your package name>` pelo nome real do pacote da aplicação.
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

