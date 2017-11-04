---
title: "Integração do Android SDK do Azure Mobile Engagement"
description: "Mais recentes atualizações e procedimentos para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 0282abbf44406cac89c13520bc2a4e375817ed1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>Como integrar o GCM Mobile engagement
> [!IMPORTANT]
> Tem de seguir o procedimento de integração descrito num documento do Android a como integrar o Engagement antes de seguir este guia.
> 
> Este documento é útil apenas se já tiver integrado o módulo de alcance e planeie a push dispositivos Google Play. Para integrar campanhas de alcance na sua aplicação, leia primeiro como integrar o Engagement contactar no Android.
> 
> 

## <a name="introduction"></a>Introdução
Integrar o GCM permite à aplicação ser instalada.

Contém payloads GCM enviadas para o SDK sempre o `azme` chave do objeto de dados. Deste modo, se utilizar o GCM para outra finalidade na sua aplicação, pode filtrar pushes com base nessa chave.

> [!IMPORTANT]
> Apenas os dispositivos com Android 2.2 ou superior, com o Google Play instalado e ter Google ligação em segundo plano ativada pode ser enviada com GCM; No entanto, pode integrar este código de segurança em dispositivos não suportados (apenas utiliza pendentes).
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Criar um projeto do Google Cloud Messaging com a chave de API
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>Integração do SDK
### <a name="managing-device-registrations"></a>Gerir registos de dispositivos
Cada dispositivo terá de enviar um comando de registo para os servidores da Google, caso contrário, não é possível aceder.

Um dispositivo também pode anular o registo de notificações GCM (o dispositivo é automaticamente anulado se a aplicação é desinstalada).

Se não utilizar [Google Play SDK] ou não já envia a intenção de registo por si, pode tornar o Engagement registar o dispositivo automaticamente para si.

Para ativar esta opção, adicione o seguinte ao seu `AndroidManifest.xml` , no interior de ficheiros a `<application/>` etiqueta:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar o id de registo para o serviço Push o Engagement e receber notificações
Para poder comunicar o id de registo do dispositivo para o serviço Push o Engagement e receber as notificações, adicione o seguinte ao seu `AndroidManifest.xml` , no interior de ficheiros a `<application/>` tag (mesmo se gerir registos de dispositivos por si):

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
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Certifique-se de que tem as seguintes permissões seu `AndroidManifest.xml` (depois do `</application>` tag).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Conceder ao Mobile Engagement acesso à Chave de API do GCM
Siga [neste guia](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) para conceder acesso de Mobile Engagement para a chave de API do GCM.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
