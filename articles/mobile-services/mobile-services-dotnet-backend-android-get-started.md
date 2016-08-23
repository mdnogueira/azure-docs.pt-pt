
<properties
    pageTitle="Introdução aos Mobile Services do Azure para aplicações Android"
    description="Siga este tutorial para começar a utilizar os Mobile Services do Azure para desenvolvimento do Android."
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="ricksal"/>


# <a name="getting-started"> </a>Introdução aos Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão equivalente deste tópico para Mobile Apps, consulte [Criar uma aplicação Android nas Mobile Apps do Azure](../app-service-mobile/app-service-mobile-android-get-started.md).

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Android utilizando os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação _Lista de tarefas_ simples que armazena os dados da aplicação no novo serviço móvel. O serviço móvel que irá criar utiliza as linguagens .NET suportadas, utilizando o Visual Studio para a lógica de negócio do lado do servidor e para a gestão do serviço móvel. Para criar um serviço móvel que permita escrever a lógica de negócio do lado do servidor em JavaScript, consulte a secção [Versão back-end de JavaScript](mobile-services-android-get-started.md) deste tópico.

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

A conclusão deste tutorial requer as [Ferramentas de Programador Android][Android Studio], que inclui o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente. É necessário o Android 4.2 ou uma versão posterior.

O projeto de início rápido transferido contém o SDK de Mobile Services para o Android.

> [AZURE.IMPORTANT] Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Services gratuitos, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Transferir o serviço móvel para o computador local

Agora que criou o serviço móvel, transfira o projeto de Mobile Services personalizado que pode executar no seu computador local ou numa máquina virtual.

1. Clique no serviço móvel que acabou de criar. Em seguida, no separador de início rápido, clique em **Android** em **Escolher plataforma** e expanda **Criar uma nova aplicação Android**.

    ![][1]

2. Se ainda não o fez, transfira e instale o [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou uma versão posterior.

3. No passo 2, clique em **Transferir** em **Transferir e publicar o seu serviço na nuvem**.

    Esta ação transfere o projeto do Visual Studio que implementa o seu serviço móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Testar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Criar uma nova aplicação Android

Nesta secção, irá criar uma nova aplicação Android que está ligada ao seu serviço móvel.

1. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.

2. No separador de início rápido, clique em **Android** em **Escolher plataforma** e expanda **Criar uma nova aplicação Android**.

    ![][2]

3. Se ainda não o fez, transfira e instale as [Ferramentas de Programador Android][SDK Android] no computador local ou numa máquina virtual.

4. Em **Transferir e executar a aplicação**, clique em **Transferir**.

    Esta ação transfere o projeto para a aplicação _Lista de tarefas_ de exemplo que está ligada ao seu serviço móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Executar a aplicação Android

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Adicionar notificações push à aplicação]
  <br/>Saiba como enviar uma notificação push muito básica para a sua aplicação.

* [Adicionar autenticação à aplicação]
  <br/>Saiba como restringir o acesso aos dados back-end a utilizadores registados específicos da aplicação.

* [Resolver problemas relacionados com um back-end .NET dos Mobile Services]
  <br/> Saiba como diagnosticar e corrigir problemas que podem surgir com um back-end .NET dos Mobile Services.

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Passos Seguintes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-AS.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-AS.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introdução (Eclipse)]: mobile-services-dotnet-backend-android-get-started-ec.md
[Adicionar notificações push à aplicação]: mobile-services-dotnet-backend-android-get-started-push.md
[Adicionar autenticação à aplicação]: mobile-services-dotnet-backend-android-get-started-auth.md
[SDK Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[SDK Android dos Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Resolver problemas relacionados com um back-end .NET dos Mobile Services]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Portal Clássico do Azure]: https://manage.windowsazure.com/



<!--HONumber=Aug16_HO1-->


