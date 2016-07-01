<properties
    pageTitle="Introdução às Mobile Apps do Azure para aplicações Xamarin.Android"
    description="Siga este tutorial para começar a utilizar Mobile Apps do Azure para desenvolvimento do Xamarin.Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="glenga" />

#Criar uma Aplicação Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Descrição geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Xamarin.Android. Para obter mais informações, consulte [O que são Mobile Apps](app-service-mobile-value-prop.md).

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações Xamarin.Android.

##Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Para obter instruções, consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).  
 
>[AZURE.NOTE] Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar App Service](https://tryappservice.azure.com/?appServiceName=mobile) e comece de imediato a criar uma Aplicação Móvel de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.

## Criar um novo back-end da Aplicação Móvel do Azure

Siga estes passos para criar um novo back-end da Aplicação Móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, deve transferir um projeto de servidor para um back-end simples de uma “lista de tarefas” e publicá-lo no Azure.

## Configurar o projeto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Transferir e executar a aplicação Xamarin.Android

1. Em **Transferir e executar o projeto Xamarin.Android**, clique no botão **Transferir**.

    Este processo transfere um projeto que contém uma aplicação cliente que está ligada à sua aplicação móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

2. Prima a tecla **F5** para compilar o projeto e iniciar a aplicação.

3. Na aplicação, digite um texto significativo, tal como _Concluir o tutorial_, e clique no botão **Adicionar**.

    ![][10]

    Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

    > [AZURE.NOTE] Pode rever o código que acede ao seu back-end da aplicação móvel para consultar e inserir dados (presente no ficheiro ToDoActivity.cs c#).

##Passos seguintes

* [Adicionar autenticação à aplicação ](app-service-mobile-xamarin-android-get-started-users.md)  
Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Adicionar notificações push à aplicação Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md)  
Saiba como adicionar notificações push à aplicação.
* [Como utilizar o cliente gerido para Mobile Apps do Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Saiba como trabalhar com o SDK cliente gerido na aplicação Xamarin. 


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Portal do Azure]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Jun16_HO2-->


