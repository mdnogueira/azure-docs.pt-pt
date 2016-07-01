<properties
    pageTitle="Introdução aos Mobile Services para aplicações Xamarin iOS | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar os Mobile Services do Azure para o desenvolvimento de Xamarin iOS."
    services="mobile-services"
    documentationCenter="xamarin"
    authors="conceptdev"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/16/2016"
    ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Introdução aos Mobile Services
[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão equivalente deste tópico para Aplicações Móveis, consulte [Criar uma Aplicação Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Xamarin.iOS utilizando os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel.

Se preferir ver um vídeo, o clip abaixo segue os mesmos passos deste tutorial.

Vídeo: “Introdução ao Xamarin e aos Mobile Services do Azure” com Craig Dunn, programador de Xamarin (duração: 10:05 min.)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]

Para concluir este tutorial, é necessário o XCode e o Xamarin Studio para OS X ou o Visual Studio no Windows com um MAC em rede. Pode obter instruções de instalação completas em [Configuração e Instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 

> [AZURE.IMPORTANT] Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Services gratuitos, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação Xamarin.iOS

Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação ou modificar uma aplicação existente para ligar ao seu serviço móvel.

Nesta secção, irá criar uma nova aplicação Xamarin.iOS que está ligada ao seu serviço móvel.

1.  No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.

2. No separador de início rápido, clique em **Xamarin.iOS** em **Escolher plataforma** e expanda **Criar uma nova aplicação Xamarin.iOS**.

    ![][6]

    Esta ação apresenta os três passos fáceis necessários para criar uma aplicação Xamarin.iOS ligada ao seu serviço móvel.

    ![][7]

3. Se ainda não o fez, transfira e instale o Xcode (recomendamos a versão mais recente, Xcode 6.0 ou posterior) e o [Xamarin Studio].

4. Clique em **Criar tabela de Itens da Lista da Tarefas** para criar uma tabela para armazenar os dados da aplicação.

5. Em **Transferir e executar a aplicação**, clique em **Transferir**.

    Esta ação transfere o projeto para a aplicação _Lista de tarefas_ de exemplo que está ligada ao seu serviço móvel e faz referência ao componente de Mobile Services para Xamarin.iOS. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

## Executar a nova aplicação Xamarin.iOS

A etapa final deste tutorial consiste em criar e executar a sua nova aplicação.

1. Navegue até à localização onde guardou os ficheiros de projeto comprimidos, expanda os ficheiros no computador e abra o ficheiro de solução **XamarinTodoQuickStart.iOS.sln** com o Xamarin Studio ou o Visual Studio.

    ![][8]

    ![][9]

2. Prima o botão **Executar** para criar o projeto e iniciar a aplicação no emulador do iPhone, que é a predefinição para este projeto.

3. Na aplicação, digite um texto significativo, tal como _Concluir o tutorial_, e clique no ícone de mais (**+**).

    ![][10]

    Esta ação envia um pedido POST para o novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo serviço móvel e os dados são apresentados na lista.

    > [AZURE.NOTE] Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, que se encontra no ficheiro C# TodoService.cs.

4. No [Portal Clássico do Azure], clique no separador **Dados** e, em seguida, clique na tabela **Itens da Lista de Tarefas**.

    ![][11]

    Esta ação permite procurar os dados inseridos pela aplicação na tabela.

    ![][12]


## Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Introdução à sincronização de dados offline] Saiba como o guia de introdução utiliza a sincronização de dados offline para tornar a sua aplicação robusta e responsiva.

* [Introdução à autenticação] Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Introdução às notificações push] Saiba como enviar uma notificação push muito básica para a sua aplicação.




[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Passos Seguintes]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução à sincronização de dados offline]: mobile-services-xamarin-ios-get-started-offline-data.md
[Introdução à autenticação]: partner-xamarin-mobile-services-ios-get-started-users.md
[Introdução às notificações push]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[SDK iOS dos Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal Clássico do Azure]: https://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


