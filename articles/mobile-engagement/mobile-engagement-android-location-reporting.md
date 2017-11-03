---
title: "Localização do relatório do SDK Android do Azure Mobile Engagement"
description: "Descreve como configurar a localização do Reporting Services para Android SDK do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Localização do relatório do SDK Android do Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Este tópico descreve como fazê-lo para a sua aplicação Android de relatórios de localização.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Relatórios de localização
Se pretender que as localizações sejam comunicados, tem de adicionar algumas linhas de configuração (entre o `<application>` e `</application>` etiquetas).

### <a name="lazy-area-location-reporting"></a>Relatórios de localização de área em diferido
Relatórios de localização de área em diferido permite reporting do país, região e Localidade associados a dispositivos. Este tipo de relatórios de localização apenas utiliza localizações de rede (com base no ID de célula ou Wi-Fi). A área de dispositivos é reportada no máximo uma vez por sessão. O GPS nunca é utilizada e, por conseguinte, este tipo de relatório de localização tem impacto baixo com a bateria.

Áreas comunicadas são utilizadas para calcular geográficas estatísticas sobre utilizadores, sessões, eventos e erros. Também pode ser utilizados como critério de campanhas de alcance.

Ativar a localização de área em diferido relatórios utilizando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também tem de especificar uma permissão de localização. Este código utiliza ``COARSE`` permissão:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Se a sua aplicação requê-lo, pode utilizar ``ACCESS_FINE_LOCATION`` em vez disso.

### <a name="real-time-location-reporting"></a>Relatórios de localização em tempo real
Relatórios de localização em tempo real permite que o Reporting Services a latitude e longitude associados a dispositivos. Por predefinição, este tipo de relatórios de localização utiliza apenas localizações de rede, com base no ID de célula ou Wi-Fi. O Reporting Services só está ativa quando a aplicação é executada em primeiro plano (por exemplo, durante uma sessão).

Localizações em tempo real são *não* utilizado para calcular estatísticas. Os seus única finalidade é permitir a utilização de geométrico em tempo real \<alcance-público-alvo-barreira geográfica\> critério no campanhas de alcance.

Para ativar a localização em tempo real, relatórios, adicione uma linha de código onde definir a cadeia de ligação do Engagement na atividade de iniciador. O resultado tem o seguinte aspeto:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS com base em relatórios
Por predefinição, apenas os relatórios de localização em tempo real utilizam localizações de rede. Para ativar a utilização de localizações com base em GPS, que são muito mais exatos, utilize o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de adicionar a permissão seguinte se estiverem em falta:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatórios de em segundo plano
Por predefinição, os relatórios de localização em tempo real só estão ativo quando a aplicação é executada em primeiro plano (por exemplo, durante uma sessão). Para ativar os relatórios também em segundo plano, utilize este objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Quando a aplicação é executada em segundo plano, com base na rede localizações apenas são comunicadas, mesmo se tiver ativado o GPS.
> 
> 

Se o utilizador reinicia o respetivo dispositivo, o relatório de localização em segundo plano está parado. Para torná-lo reinicie automaticamente no momento do arranque, adicione este código.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Também terá de adicionar a permissão seguinte se estiverem em falta:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M permissões
A partir do Android M, alguns permissões são geridas no tempo de execução e precisam de aprovação do utilizador.

Se o nível de API do Android 23 de destino, as permissões de tempo de execução estão desativadas por predefinição para novas instalações de aplicações. Caso contrário, são ativadas por predefinição.

Pode ativar/desativar essas permissões no menu de definições do dispositivo. Desativar as permissões no menu de sistema inutilizam os processos em segundo plano da aplicação, que é um comportamento do sistema e não tem impacto na capacidade de receber push em segundo plano.

No contexto de relatórios de localização de Mobile Engagement, as permissões que necessitem de aprovação no tempo de execução são:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Pedir permissões de utilizador utilizando uma caixa de diálogo de sistema padrão. Se o utilizador aprova, informe ``EngagementAgent`` a tomar nesse mudança em tempo real. Caso contrário, a alteração é processada da próxima vez que o utilizador inicia a aplicação.

Eis um exemplo de código para utilizar numa atividade da sua aplicação para solicitar permissões e reencaminhar o resultado se positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
