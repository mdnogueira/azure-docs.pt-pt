---
title: "Integração do Android SDK do Azure Mobile Engagement"
description: "Mais recentes atualizações e procedimentos para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 43987962ea2b7b825b88643d18b4db65f1f1670e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-adm-with-engagement"></a>Como integrar o Engagement ADM
> [!IMPORTANT]
> Tem de seguir o procedimento de integração descrito num documento do Android a como integrar o Engagement antes de seguir este guia.
> 
> Este documento é útil apenas se já tiver integrado o módulo de alcance e planeie a push dispositivos Amazon. Para integrar campanhas de alcance na sua aplicação, leia primeiro como integrar o Engagement contactar no Android.
> 
> 

## <a name="introduction"></a>Introdução
Integrar ADM permite à aplicação ser instalada quando os dispositivos Amazon Android a filtrar.

Contém payloads ADM enviadas para o SDK sempre o `azme` chave do objeto de dados. Deste modo, se utilizar ADM para outra finalidade na sua aplicação, pode filtrar pushes com base nessa chave.

> [!IMPORTANT]
> Apenas Amazon Kindle dispositivos com Android 4.0.3 ou superior, são suportadas pelo Amazon Device Messaging; No entanto, pode integrar este código de forma segura nos outros dispositivos.
> 
> 

## <a name="sign-up-to-adm"></a>Inscrever-se ADM
Se não o tiver feito, tem de ativar ADM na sua conta Amazon.

O procedimento está detalhado na: [ <https://developer.amazon.com/sdk/adm/credentials.html>].

Após concluir o procedimento, obter:

* Credenciais de OAuth (um ID de cliente e um segredo do cliente) para o Engagement conseguir push os seus dispositivos.
* Uma chave de API que tem de ser integrado na sua aplicação.

## <a name="sdk-integration"></a>Integração do SDK
### <a name="managing-device-registrations"></a>Gerir registos de dispositivos
Cada dispositivo terá de enviar um comando de registo para os servidores do ADM, caso contrário, não é possível aceder.

Se já utilizar o [biblioteca de clientes do ADM]e já tiver [integrado ADM] pode aceder diretamente a receber de android sdk adm.

Se não tiver integrado ADM ainda, o Engagement tem uma forma mais simples para ativá-la na sua aplicação:

Editar o `AndroidManifest.xml` ficheiro:

* Adicionar espaço de nomes Amazon, o ficheiro deve começar como esta:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Dentro do `<application/>` tag, adicione esta secção:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Depois de adicionar a etiqueta do amazon, pode ter um erro de compilação do projeto de compilação de destino esteja abaixo Android 2.1. Tem de utilizar um **Android 2.1 +** criar destino (não se preocupe, ainda pode ter um `minSdkVersion` definido para 4).
* Integrar a chave de API do ADM como um recurso seguindo [este procedimento].

Em seguida, siga as instruções das secções seguintes.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar o id de registo para o serviço Push o Engagement e receber notificações
Para poder comunicar o id de registo do dispositivo para o serviço Push o Engagement e receber as notificações, adicione o seguinte ao seu `AndroidManifest.xml` , no interior de ficheiros a `<application/>` tag (mesmo que utilize ADM sem o Engagement):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Certifique-se de que tem as seguintes permissões seu `AndroidManifest.xml` (antes do `</application>` tag).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Credenciais conceder Engagement OAuth
Submeta as credenciais OAuth (ID de cliente e segredo do cliente) no Portal do Engagement.

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[biblioteca de clientes do ADM]:https://developer.amazon.com/sdk/adm/setup.html
[integrado ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[este procedimento]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
