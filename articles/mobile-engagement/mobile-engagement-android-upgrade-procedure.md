---
title: "Integração do Android SDK do Azure Mobile Engagement"
description: "Mais recentes atualizações e procedimentos para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1f047f93fa8bc852b28c86e91d0c007a94fb4299
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-procedures"></a>Procedimentos de atualização
Se já tiver integrado uma versão mais antiga do nosso SDK na sua aplicação, terá de considerar os seguintes pontos ao atualizar o SDK.

Terá de seguir vários procedimentos, se omitido várias versões do SDK. Por exemplo, se a migração do 1.4.0 para 1.6.0 que tem de primeiro siga o procedimento "de 1.4.0 para 1.5.0", em seguida, o procedimento "de 1.5.0 para 1.6.0".

Seja qual for a versão a atualização, tem de substituir o `mobile-engagement-VERSION.jar` pelo novo.

## <a name="from-420-to-421"></a>De 4.2.0 para 4.2.1
Este passo, na verdade, pode ser feito em qualquer versão do SDK, é uma melhoria de segurança quando integrar o alcance atividades.

Agora deve adicionar `exported="false"` a todas as atividades de alcance.

Atividades de alcance devem agora ter o seguinte aspeto seu `AndroidManifest.xml`:

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

## <a name="from-400-to-410"></a>De 4.0.0 para 4.1.0
O SDK agora identificador nova permissão modelo do Android M.

Se utilizar funcionalidades de localização ou notificações de visão geral, leia [nesta secção](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Para além do novo modelo de permissão, agora suportamos o configurar funcionalidades de localização em tempo de execução.
Estamos ainda compatíveis com os parâmetros de manifesto para a localização, mas agora está a ser preterido. Para utilizar a configuração do runtime, remover as secções seguintes do seu ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

e ler [isto atualizado procedimento](mobile-engagement-android-integrate-engagement.md#location-reporting) para utilizar em vez disso, a configuração de tempo de execução.

## <a name="from-300-to-400"></a>De 3.0.0 para 4.0.0
### <a name="native-push"></a>Push nativo
Push nativo (GCM/ADM) agora também é utilizado para em notificações da aplicação, de modo tem de configurar as credenciais de push nativo para qualquer tipo de campanha push.

Se não estiver já siga [este procedimento](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Integração de alcance tenha sido modificada nos ``AndroidManifest.xml``.

Substitua este:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Por

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
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

Não há possivelmente um ecrã de carregamento agora quando clica num anúncio (com o texto/web conteúdo) ou um inquérito.
Tem de adicionar este para esses campanhas funcionar no 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Recursos
Incorporar o novo `res/layout/engagement_loading.xml` ficheiros no seu projeto.

## <a name="from-240-to-300"></a>De 2.4.0 para 3.0.0
O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS numa aplicação ligada ao Azure Mobile Engagement. Se estiver a migrar de uma versão anterior, consulte o web site do Capptain para migrar para 2.4.0 primeiro e, em seguida, aplique o procedimento seguinte.

> [!IMPORTANT]
> Capptain e Mobile Engagement não são os mesmos serviços e o procedimento indicado abaixo apenas realça como migrar a aplicação de cliente. Migrar o SDK na aplicação não é migrados os dados saídos de servidores Capptain para os servidores de Mobile Engagement.
> 
> 

### <a name="jar-file"></a>Ficheiro JAR
Substitua `capptain.jar` por `mobile-engagement-VERSION.jar` no seu `libs` pasta.

### <a name="resource-files"></a>Ficheiros de recursos
Todos os ficheiros de recursos que fornecemos-lhe (como prefixo `capptain_`) tem de ser substituído pelos novos (prefixo `engagement_`).

Se personalizado esses ficheiros, terá de voltar a aplicar a personalização em ficheiros novos, **todos os identificadores nos ficheiros de recursos também mudança de nome**.

### <a name="application-id"></a>ID da aplicação
Agora o Engagement utiliza uma cadeia de ligação para configurar os identificadores SDK, tais como o identificador da aplicação.

Tem de utilizar `EngagementAgent.init` método na sua atividade iniciador como esta:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de ligação para a sua aplicação é apresentada no Portal do Azure.

Remova quaisquer chamada para `CapptainAgent.configure` como `EngagementAgent.init` substitui este método.

O `appId` já não pode ser configurada com `AndroidManifest.xml`.

Remova esta secção do seu `AndroidManifest.xml` se tiver:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>API de Java
Cada chamada para qualquer classe Java do nosso SDK tem de ser mudado; Por exemplo, `CapptainAgent.getInstance(this)` tem de ser mudado `EngagementAgent.getInstance(this)`, `extends CapptainActivity` tem de ser mudado `extends EngagementActivity` etc...

Se foram integrado com os ficheiros de preferência do agente predefinido, o nome de ficheiro predefinido é agora `engagement.agent` e a chave é `engagement:agent`.

Ao criar anúncios web, o Gestor de enlaces de Javascript é agora `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Não existe uma grande quantidade de alterações foi efetuada, o serviço não está a ser partilhado deixam de ser e muitos recetores já não são exportável.

A declaração de serviço agora é mais simples; Remova o filtro de intenção e todos os dados meta dentro-la e adicionar `exportable=false`.

Além tudo é mudado para utilizar o engagement.

Agora parece:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Quando pretender ativar registos de teste, os dados de metadados agora foi movido para a etiqueta da aplicação e o nome foi mudado:

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

Todos os outro meta-data apenas mudança de nome, eis a lista completa (de mudança de nome de decorrer apenas aqueles que utilizar):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play e SmartAd controlo foi removido do SDK apenas tem de remover este sem substituição:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

As atividades de alcance agora declaradas como esta:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

Se tiver de atividades personalizadas de alcance, terá apenas alterar as ações para corresponder a intenção `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` ou `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Os recetores difusão tem o nome foi mudados e Iremos agora adicionar `exported=false`. Eis a lista completa de recetores com a especificação de nova (de mudança de nome de decorrer apenas aqueles que utilizar):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Recetor de controlo foi removido, pelo que terá de remover esta secção:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Tenha em atenção que a declaração da sua implementação do recetor difusão **EngagementMessageReceiver** mudou no `AndroidManifest.xml`. Isto acontece porque a API para enviar e remover as mensagens de com XMPP arbitrários arbitrários entidades de com XMPP e a API para enviar e receber mensagens entre dispositivos foram removidos. Assim, tem também eliminar as chamadas de retorno seguintes a partir do seu **EngagementMessageReceiver** implementação:

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

e

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

em seguida, elimine qualquer chamada no **EngagementAgent** para:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

e

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
Configuração proguard pode ser afetada por rebranding, as regras agora procura como:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }

