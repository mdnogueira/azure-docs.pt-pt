<properties
    pageTitle="Introdução aos Mobile Services do Azure para aplicações Android (Back-end de JavaScript)"
    description="Siga este tutorial para começar a utilizar os Mobile Services do Azure para desenvolvimento do Android (Back-end de JavaScript)."
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="reikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="04/08/2016"
    ms.author="ricksal"/>

# Introdução aos Mobile Services para Android (Back-end de JavaScript)

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão equivalente deste tópico para Mobile Apps, consulte [Criar uma aplicação Android nas Mobile Apps do Azure](../app-service-mobile/app-service-mobile-android-get-started.md).

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Android utilizando os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação **Lista de tarefas** simples que armazena os dados da aplicação no novo serviço móvel.

> [AZURE.VIDEO mobile-get-started-android]

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Pré-requisitos

A conclusão deste tutorial requer as [Ferramentas de Programador Android](https://developer.android.com/sdk/index.html), que inclui o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente. É necessário o Android 4.2 ou uma versão posterior.

O projeto de início rápido transferido contém o SDK de Mobile Services do Azure para Android.

> [AZURE.IMPORTANT] Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação Android

Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação ou modificar uma aplicação existente para ligar ao seu serviço móvel.

Nesta secção, irá criar uma nova aplicação Android que está ligada ao seu serviço móvel.

1.  No Portal Clássico do Azure, clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.

2. No separador de início rápido, clique em **Android** em **Escolher plataforma** e expanda **Criar uma nova aplicação Android**.

    ![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

    Esta ação apresenta os três passos fáceis necessários para criar uma aplicação Android ligada ao seu serviço móvel.

    ![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Se ainda não o fez, transfira e instale as [Ferramentas de Programador Android](https://go.microsoft.com/fwLink/p/?LinkID=280125) no seu computador local ou máquina virtual.

4. Clique em **Criar tabela com Item da Lista de Tarefas** para criar uma tabela para armazenar os dados da aplicação.


5. Agora, transfira a aplicação ao premir o botão **Transferir**.

## Executar a aplicação Android

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados através dos Mobile Services.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Introdução às notificações push]
  <br/>Saiba como enviar uma notificação push muito básica para a sua aplicação.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- URLs. -->
[Introdução (Eclipse)]: mobile-services-android-get-started-ec.md
[Introdução aos dados]: mobile-services-android-get-started-data.md
[Introdução à autenticação]: mobile-services-android-get-started-users.md
[Introdução às notificações push]: mobile-services-javascript-backend-android-get-started-push.md
[SDK Android dos Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533




<!--HONumber=Jun16_HO2-->


