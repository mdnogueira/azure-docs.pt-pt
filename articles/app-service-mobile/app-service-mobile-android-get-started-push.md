---
title: "Adicionar notificações push para a sua aplicação Android com aplicações móveis | Microsoft Docs"
description: "Saiba como utilizar as Mobile Apps para enviar notificações push para a sua aplicação Android."
services: app-service\mobile
documentationcenter: android
manager: syntaxc4
editor: 
author: ggailey777
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/17/2017
ms.author: glenga
ms.openlocfilehash: 6882a7b1864d511e70495e1d1d782aa0f03beca3
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="add-push-notifications-to-your-android-app"></a>Adicionar notificações push para a sua aplicação Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, adicionar notificações push para o [Android início rápido] projeto para que uma notificação push é enviada para o dispositivo sempre que é inserido um registo.

Se não utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Para obter mais informações, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
Precisa do seguinte:

* Um IDE, dependendo de back-end do seu projeto:

  * [Android Studio](https://developer.android.com/sdk/index.html) se esta aplicação tem um Node.js back-end.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou posterior se esta aplicação tem um back-end de .NET de Microsoft.
* Android 2.3 ou posterior, repositório Google revisão 27 ou posterior e serviços do Google Play 9.0.2 ou posterior para Firebase Cloud Messaging.
* Concluir o [Android início rápido].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Criar um projeto que suporte o Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Ativar as notificações push para o projeto de servidor
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações push à aplicação
Nesta secção, atualizar a sua aplicação Android do cliente para processar as notificações push.

### <a name="verify-android-sdk-version"></a>Verificar a versão do Android SDK
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

O próximo passo é instalar serviços do Google Play. Firebase Cloud Messaging tem alguns requisitos de nível de API mínimos para desenvolvimento e testes, que o **minSdkVersion** propriedade no manifesto deve estar em conformidade com.

Se estiver a testar com um dispositivo mais antigo, consulte [adicionar Firebase à sua projeto Android] para determinar como insuficiente pode definir este valor e defini-la corretamente.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Adicionar Firebase Cloud Messaging para o projeto
[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Adicionar código
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testar a aplicação contra o serviço móvel publicado
Pode testar a aplicação anexando um telemóvel Android com um cabo USB diretamente ou através de um dispositivo virtual no emulador.

## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial, considere continuar para um dos seguintes tutoriais:

* [Adicionar autenticação à sua aplicação Android](app-service-mobile-android-get-started-users.md).
  Saiba como adicionar autenticação para o projeto de início rápido todolist no Android utilizando um fornecedor de identidade suportados.
* [Ativar a sincronização offline para a sua aplicação Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de aplicações móveis. Com a sincronização offline, os utilizadores poderão interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede.

<!-- URLs -->
[Android início rápido]: app-service-mobile-android-get-started.md
[adicionar Firebase à sua projeto Android]:https://firebase.google.com/docs/android/setup
