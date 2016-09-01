<properties
    pageTitle="Introdução às Mobile Apps do App Service do Azure para aplicações Xamarin.iOS | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar Mobile Apps para o desenvolvimento do Xamarin.iOS"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/04/2016"
    ms.author="normesta"/>


#Criar uma aplicação Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Descrição geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel Xamarin.iOS utilizando um back-end da aplicação móvel do Azure.  Poderá criar tanto um novo back-end da aplicação móvel assim como uma simples aplicação Xamarin.iOS de uma _Lista de tarefas_ que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Xamarin.iOS referentes à utilização da funcionalidade Mobile Apps no App Service do Azure.

##Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Para obter instruções, consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* Um Mac com Xcode v7.0 ou posterior e Xamarin Studio Community instalado. Consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configuração, instalação e verificações para utilizadores Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE] Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://tryappservice.azure.com/?appServiceName=mobile). Aqui, pode criar imediatamente uma aplicação móvel de arranque de curta duração no App Service: sem cartões de crédito, sem compromissos.

## Criar um novo back-end da Aplicação Móvel do Azure

Siga estes passos para criar um novo back-end da Aplicação Móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurar o projeto de servidor

Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, deve transferir um projeto de servidor para um back-end simples de uma “lista de tarefas” e publicá-lo no Azure.

Siga os passos abaixo para configurar o projeto de servidor para utilizar o back-end Node.js ou .NET.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## Transferir e executar a aplicação Xamarin.iOS

1. Abra o [Portal do Azure] numa janela do browser.

2. No painel de definições da Aplicação Móvel, clique em **Introdução** > **Xamarin.iOS**. No passo 3, clique em **Criar uma nova aplicação**, se a opção ainda não estiver selecionada.  Em seguida, clique no botão **Transferir**.

    Este processo transfere um projeto que contém uma aplicação cliente que está ligada à sua aplicação móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

3. Extraia o projeto que transferiu e, em seguida, abra-o no Xamarin Studio (ou Visual Studio).

    ![][9]

    ![][8]

4. Prima a tecla F5 para compilar o projeto e iniciar a aplicação no emulador do iPhone.

5. Na aplicação, digite um texto significativo, tal como _Saber mais sobre o Xamarin_, e clique no botão **+**.

    ![][10]

    Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

>[AZURE.NOTE]Pode rever o código que acede ao seu back-end da aplicação móvel para consultar e inserir dados no ficheiro QSTodoService.cs C#.

##Passos seguintes

* [Adicionar autenticação à aplicação ](app-service-mobile-xamarin-ios-get-started-users.md)
  <br/>Saiba como autenticar os utilizadores da aplicação utilizando um fornecedor de identidade.

* [Adicionar notificações push à aplicação](app-service-mobile-xamarin-ios-get-started-push.md)
  <br/>Saiba como enviar uma notificação push muito básica para a sua aplicação.

<!-- Anchors. -->
[Introdução aos back-ends de Mobile Apps]:#getting-started
[Criar um novo back-end da aplicação móvel]:#create-new-service
[Passos Seguintes]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/



<!--HONumber=ago16_HO4-->


