---
title: Introdução aos Mobile Services do Azure para aplicações iOS | Microsoft Docs
description: Siga este tutorial para começar a utilizar os Mobile Services do Azure para o desenvolvimento de iOS.
services: mobile-services
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/21/2016
ms.author: krisragh

---
# <a name="getting-started"> </a>Introdução aos Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para a versão equivalente deste tópico para Aplicações Móveis, consulte [Criar uma aplicação iOS nas Aplicações Móveis do Azure](../app-service-mobile/app-service-mobile-ios-get-started.md).
> 
> 

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação iOS utilizando os Mobile Services do Azure.

Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel. O serviço móvel que irá criar utiliza JavaScript para a lógica de negócio do lado do servidor. Para criar um serviço móvel com a lógica de negócio do lado do servidor no .NET, consulte a secção [Versão back-end de .NET] deste tópico.

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter [Mobile Services gratuitos, que pode continuar a utilizar mesmo após o final do período de avaliação](https://azure.microsoft.com/pricing/details/mobile-services/). Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20).
> 
> 

## <a name="create-new-service"> </a>Criar um novo serviço móvel
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação iOS
Pode seguir um Guia de Introdução fácil no Portal Clássico do Azure para criar uma nova aplicação ligada ao seu serviço móvel:

1. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.
2. No separador de Início Rápido, clique em **iOS** em **Escolher uma plataforma** e expanda **Criar uma nova aplicação iOS**. Esta ação apresenta os passos necessários para criar uma aplicação iOS ligada ao seu serviço móvel.
3. Clique em **Criar tabela com Item da Lista de Tarefas** para criar uma tabela para armazenar os dados da aplicação.
4. Em **Transferir e executar a aplicação**, clique em **Transferir**. Esta ação transfere o projeto para a aplicação *Lista de tarefas* de exemplo que está ligada ao seu serviço móvel, juntamente com o SDK iOS dos Mobile Services. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Executar a nova aplicação iOS
[!INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4">

<li><p>No [Portal Clássico do Azure], clique no separador **DADOS** e, em seguida, clique na tabela **Item da Lista de Tarefas**. Esta ação permite procurar os dados inseridos pela aplicação na tabela.<p></li></ol></p>

## <a name="next-steps"> </a>Passos Seguintes
Saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Introdução à sincronização de dados offline]
    <br/>Saiba como utilizar a sincronização de dados offline para tornar a sua aplicação robusta e responsiva.
* [Adicionar autenticação a uma aplicação existente]
    <br/>Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Adicionar notificações push a uma aplicação existente]
    <br/>Saiba como enviar uma notificação push muito básica para a sua aplicação.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Passos Seguintes]:#next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução à sincronização de dados offline]: mobile-services-ios-get-started-offline-data.md
[Adicionar autenticação a uma aplicação existente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Adicionar notificações push a uma aplicação existente]: mobile-services-dotnet-backend-ios-get-started-push.md


[SDK iOS dos Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versão back-end de .NET]: mobile-services-dotnet-backend-ios-get-started.md



<!--HONumber=Aug16_HO1-->


