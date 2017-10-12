---
title: "Introdução ao Azure Mobile Engagement para aplicações Android"
description: "Saiba como utilizar o Azure Mobile Engagement com notificações push e de análise para aplicações Android."
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: dc255a930bf71e6ef6d964bc5e3472a38ce4e467
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Introdução ao Azure Mobile Engagement para aplicações Android
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e saber como enviar notificações push para utilizadores segmentados de uma aplicação Android.
Este tutorial demonstra o cenário de difusão simples utilizando o Mobile Engagement. Aqui, irá criar uma aplicação Android em branco que recolhe dados básicos e recebe notificações push através Google Cloud Messaging (GCM).

## <a name="prerequisites"></a>Pré-requisitos
A conclusão deste tutorial requer as [Ferramentas de Programador Android](https://developer.android.com/sdk/index.html), que inclui o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente.

Também requer o [SDK Android do Mobile Engagement](https://aka.ms/vq9mfn).

> [!IMPORTANT]
> Para concluir este tutorial, precisa de uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurar o Mobile Engagement para a aplicação Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica", o conjunto mínimo necessário para recolher dados e enviar uma notificação push. Irá criar uma aplicação básica com o Android Studio para demonstrar a integração.

Encontrará toda a documentação da integração na página [Integração do SDK Android do Mobile Engagement](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Criar um projeto Android
1. Inicie o **Android Studio** e, no pop-up, selecione **Iniciar um novo projeto do Android Studio**.

    ![][1]
2. Forneça um nome de aplicação e o domínio da empresa. Anote o que está a preencher, uma vez que será necessário mais tarde. Clique em **Seguinte**.

    ![][2]
3. Selecione o fator de formulário de destino e o nível da API e clique em **seguinte**.

   > [!NOTE]
   > O Mobile Engagement requer no mínimo uma API de nível 10 (Android 2.3.3).
   >
   >

    ![][3]
4. Selecione **Atividade em Branco**, que é o único ecrã desta aplicação, e clique em **Seguinte**.

    ![][4]
5. Por fim, deixe as predefinições como estão e clique em **Concluir**.

    ![][5]

O Android Studio cria agora a aplicação de demonstração na qual iremos integrar o Mobile Engagement.

### <a name="include-the-sdk-library-in-your-project"></a>Incluir a biblioteca do SDK no projeto
1. Transfira o [SDK Android do Mobile Engagement](https://aka.ms/vq9mfn).
2. Extraia o ficheiro do arquivo para uma pasta no computador.
3. Identifique a biblioteca .jar da versão atual deste SDK e copie-a para a Área de Transferência.

      ![][6]
4. Navegue para a secção **Projeto** (1) e cole o .jar na pasta bibliotecas (2).

      ![][7]
5. Para carregar a biblioteca, sincronize o projeto.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Ligar a aplicação ao back-end do Mobile Engagement com a Cadeia de Ligação
1. Copie as linhas de código seguintes para a criação de atividade (tem de ser feita apenas num único local da aplicação, normalmente, a atividade principal). Para esta aplicação de exemplo, abra a MainActivity em src -> principal -> pasta java e adicione o seguinte:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Resolva as referências premindo Alt + Enter ou adicionando as seguintes declarações de importação:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. Volte ao Portal Clássico do Azure na página **Informações de Ligação** da aplicação e copie a **Cadeia de Ligação**.

      ![][9]
4. Cole-a no parâmetro `setConnectionString`, substituindo toda a cadeia apresentada no seguinte código:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Adicionar permissões e uma declaração de serviço
1. Adicione estas permissões ao Manifest.xml do projeto imediatamente antes ou depois da etiqueta `<application>`:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. Para declarar o serviço do agente, adicione este código entre as etiquetas `<application>` e `</application>`:

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. No código que colou, substitua `"<Your application name>"` na etiqueta apresentada no menu **Definições**, onde pode ver os serviços que estão a ser executados no dispositivo. Por exemplo, pode adicionar a palavra "Serviço" nessa etiqueta.

### <a name="send-a-screen-to-mobile-engagement"></a>Enviar um ecrã para o Mobile Engagement
Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (Atividade) para o back-end do Mobile Engagement.

Aceda a **MainActivity.java** e adicione o seguinte para substituir a classe base da **MainActivity** para **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> Se a sua classe base não for *Activity*, consulte o [Relatório Android Avançado](mobile-engagement-android-advanced-reporting.md) para saber como herdar a partir de diferentes classes.
>
>

Comente a seguinte linha deste cenário de exemplo simples:

    // setSupportActionBar(toolbar);

Se quiser manter o `ActionBar` na sua aplicação, consulte [Relatório Android Avançado](mobile-engagement-android-advanced-reporting.md).

## <a name="connect-app-with-real-time-monitoring"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Ativar as notificações push e mensagens na aplicação
Durante uma campanha, o Mobile Engagement permite-lhe interagir e ALCANÇAR os seus utilizadores com notificações push e mensagens na aplicação. Este módulo é designado ALCANCE no portal do Mobile Engagement.
A secção seguinte configura a aplicação para as receber.

### <a name="copy-sdk-resources-in-your-project"></a>Copiar recursos do SDK no projeto
1. Navegue de volta para o conteúdo de transferência do SDK e copie a pasta **res**.

    ![][10]
2. Volte ao Android Studio, selecione o diretório **principal** dos ficheiros do projeto e, em seguida, cole-os para adicionar os recursos ao projeto.

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Passos seguintes
Aceda ao [SDK Android](mobile-engagement-android-sdk-overview.md) para obter conhecimentos detalhados sobre a integração do SDK.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
