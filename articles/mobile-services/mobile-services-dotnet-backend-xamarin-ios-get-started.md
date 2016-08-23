<properties
    pageTitle="Introdução aos Mobile Services para aplicações Xamarin iOS | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar os Mobile Services do Azure para o desenvolvimento de Xamarin iOS"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="donnam"/>

# <a name="getting-started"> </a>Introdução aos Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão equivalente deste tópico para Aplicações Móveis, consulte [Criar uma Aplicação Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Xamarin iOS utilizando os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação _Lista de tarefas_ simples que armazena os dados da aplicação no novo serviço móvel. O serviço móvel que irá criar utiliza as linguagens .NET suportadas, utilizando o Visual Studio para a lógica de negócio do lado do servidor e para a gestão do serviço móvel. Para criar um serviço móvel que permita escrever a lógica de negócio do lado do servidor em JavaScript, consulte a secção [Versão back-end de JavaScript] deste tópico.

>[AZURE.NOTE]Este tópico mostra como criar um novo projeto de Mobile Services utilizando o Portal Clássico do Azure. Se utilizar o Visual Studio 2013 Atualização 2, também pode adicionar um novo projeto de Mobile Services a uma solução do Visual Studio existente. Para obter mais informações, consulte [Guia de introdução: adicionar um serviço móvel (back-end .NET)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]


A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Mobile Services para aplicações Xamarin iOS.

>[AZURE.NOTE]Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Services gratuitos, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Avaliação Gratuita do Azure</a>.<br />Este tutorial requer o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Está disponível uma versão de avaliação gratuita.

## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar uma nova aplicação Xamarin iOS

Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação ou modificar uma aplicação existente para ligar ao seu serviço móvel.

Nesta secção, irá transferir uma nova aplicação Xamarin iOS e um projeto de serviço para o seu serviço móvel.

1. Se ainda não o fez, instale o Visual Studio com o Xamarin. Pode encontrar instruções em [Configuração e Instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). Também pode utilizar o Xamarin Studio num computador Mac OS X, consulte [Configuração, instalação e verificações para utilizadores de Mac](https://msdn.microsoft.com/library/mt488770.aspx).

2. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.

3. No separador de início rápido, clique em **Xamarin** em **Escolher plataforma** e expanda **Criar uma nova aplicação Xamarin**.

    ![][6]

    Esta ação apresenta os três passos fáceis necessários para criar uma aplicação Xamarin iOS ligada ao seu serviço móvel.

    ![][7]

4. Em **Transferir e publicar o seu serviço na nuvem**, selecione **iOS** e clique em **Transferir**.

    Esta ação transfere uma solução que contém projetos para o serviço móvel e para a aplicação _Lista de tarefas_ de exemplo que está ligada ao seu serviço móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

5. Transfira o seu perfil de publicação, guarde o ficheiro transferido no computador local e tome nota do local onde o guardou.

## Testar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Executar a aplicação Xamarin iOS

A etapa final deste tutorial consiste em criar e executar a sua nova aplicação.

1. Navegue para o projeto de cliente na solução de Mobile Services, no Visual Studio ou no Xamarin Studio.

    ![][8]

    ![][9]

2. Prima o botão **Executar** para compilar o projeto de cliente e iniciar a aplicação no emulador do iPhone.

3. Na aplicação, digite um texto significativo, tal como _Concluir o tutorial_, e clique no ícone de mais (**+**).

    ![][10]

    Esta ação envia um pedido POST ao novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo serviço móvel e os dados são apresentados na lista.

>[AZURE.NOTE]Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados no ficheiro C# QSTodoService.cs.


## Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* [Introdução à sincronização de dados offline]
  <br/>Saiba como o guia de introdução utiliza a sincronização de dados offline para tornar a aplicação robusta e responsiva.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Introdução às notificações push]
  <br/>Saiba como enviar uma notificação push muito básica para a sua aplicação.

* [Resolver problemas relacionados com um back-end .NET dos Mobile Services]
  <br/> Saiba como diagnosticar e corrigir problemas que podem surgir com um back-end .NET dos Mobile Services.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Passos Seguintes]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introdução à sincronização de dados offline]: mobile-services-xamarin-ios-get-started-offline-data.md
[Introdução à autenticação]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Introdução às notificações push]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK dos Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: mobile-services-win8-javascript/
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Versão back-end de JavaScript]: mobile-services-ios-get-started.md
[Resolver problemas relacionados com um back-end .NET dos Mobile Services]: mobile-services-dotnet-backend-how-to-troubleshoot.md


<!--HONumber=Aug16_HO1-->


