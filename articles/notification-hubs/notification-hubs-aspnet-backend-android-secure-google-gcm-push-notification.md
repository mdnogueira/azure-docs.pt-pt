---
title: "Enviar notificações Push segura com Notification Hubs do Azure"
description: "Saiba como enviar notificações push segura para uma aplicação Android do Azure. Exemplos de código escrito em Java e c#."
documentationcenter: android
keywords: "notificação push, notificações push, push mensagens, notificações android push"
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 29f8c516e611c13fb73c7edc15e7c52708c75bb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Enviar notificações Push segura com Notification Hubs do Azure
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Descrição geral
> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Suporte de notificação push no Microsoft Azure permite-lhe aceder a uma fácil de utilizar multiplatform, infraestrutura de mensagens push ampliada, pois simplifica bastante a implementação de notificações push para aplicações de consumidor e para empresas para plataformas móveis.

Devido a regulamentação ou restrições de segurança, por vezes, uma aplicação pode pretender incluir algo na notificação que não pode ser transmitida através da infraestrutura de notificação push padrão. Este tutorial descreve como obter a mesma experiência, enviando informações confidenciais através de uma ligação segura, autenticada entre o dispositivo Android do cliente e o back-end da aplicação.

Um nível elevado, o fluxo é:

1. O back-end da aplicação:
   * Payload de arquivos segura na base de dados de back-end.
   * Envia o ID desta notificação para o dispositivo Android (sem informações seguras são enviadas).
2. A aplicação no dispositivo, quando receber a notificação:
   * O dispositivo Android contacta o back-end pedir o payload seguro.
   * A aplicação pode mostrar o payload como uma notificação no dispositivo.

É importante ter em atenção que o fluxo anterior (e neste tutorial), partimos do princípio que o dispositivo armazena um token de autenticação no armazenamento local, depois do utilizador iniciar sessão. Esta ação garante uma experiência totalmente integrada, como o dispositivo pode obter payload segura a notificação utilizando este token. Se a aplicação não armazena os tokens de autenticação do dispositivo, ou se estes tokens podem ter expirados, a aplicação de dispositivo, ao receber a notificação push deve apresentar uma notificação genérica pedir ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra o payload de notificação.

Este tutorial mostra como enviar notificações push segura. Baseia-se no [notificam os utilizadores](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) tutorial, pelo que deverá concluir os passos que tutorial primeiro se ainda não o fez.

> [!NOTE]
> Este tutorial parte do princípio de que criou e configurado o notification hub, conforme descrito em [introdução aos Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificar o projeto Android
Agora que alterou o seu back-end da aplicação para enviar apenas o *id* de uma notificação push, tem de alterar a sua aplicação Android para processar essa notificação e chamar novamente o back-end para obter a mensagem segura que será apresentado.
Para alcançar este objetivo, tem de certificar-se de que a sua aplicação Android sabe como para se autenticar com o back-end quando recebe as notificações push.

Iremos agora modificar o *início de sessão* fluxo para guardar o valor do cabeçalho de autenticação nas preferências de partilhado da sua aplicação. Mecanismos semelhante podem ser utilizados para armazenar nenhum token de autenticação (por exemplo, tokens de OAuth) que a aplicação tem de utilizar sem necessidade de credenciais de utilizador.

1. No seu projeto de aplicação Android, adicione as seguintes constantes na parte superior do **MainActivity** classe:
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Ainda no **MainActivity** classe, atualize o `getAuthorizationHeader()` método para conter o seguinte código:
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Adicione o seguinte `import` declarações na parte superior do **MainActivity** ficheiro:
   
        import android.content.SharedPreferences;

Agora vamos alterará o processador que é chamado quando a notificação é recebida.

1. No **MyHandler** classe alterações a `OnReceive()` método para conter:
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Em seguida, adicione o `retrieveNotification()` método, substituir o marcador de posição `{back-end endpoint}` com o ponto final de back-end obtido durante a implementação de back-end:
   
        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);
   
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

Este método chama o back-end da aplicação para obter o conteúdo de notificação utilizando as credenciais armazenadas nas preferências partilhadas e apresenta-o como uma notificação normal. A notificação de procura para o utilizador de aplicação exatamente como quaisquer outro notificação push.

Tenha em atenção que é preferível para processar de acordo com os casos de propriedade de cabeçalho de autenticação em falta ou rejeição back-end. O processamento específico nestes casos dependem principalmente na sua experiência de utilizador de destino. É uma opção apresentar uma notificação com uma linha de comandos genérica para o utilizador autenticar para obter a notificação real.

## <a name="run-the-application"></a>Executar a aplicação
Para executar a aplicação, efetue o seguinte:

1. Certifique-se **AppBackend** é implementado no Azure. Se utilizar o Visual Studio, execute o **AppBackend** aplicação Web API. É apresentada uma página web do ASP.NET.
2. No Eclipse, execute a aplicação no dispositivo Android físico ou o emulador.
3. Na aplicação Android IU, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia, mas têm de ser o mesmo valor.
4. Na aplicação Android IU, clique em **início de sessão**. Em seguida, clique em **enviar push**.

