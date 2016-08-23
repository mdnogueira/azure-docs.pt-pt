<properties
    pageTitle="Introdução às Mobile Apps utilizando Xamarin.Forms"
    description="Siga este tutorial para começar a utilizar Mobile Apps do Azure para desenvolvimento do Xamarin.Forms"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="glenga"/>

#Criar uma aplicação Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Descrição geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel Xamarin.Forms utilizando um back-end da Aplicação Móvel do Azure. Poderá criar tanto um novo back-end da Aplicação Móvel assim como uma simples aplicação Xamarin.Forms de uma _Lista de tarefas_ que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações Xamarin.Forms.

##Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Para obter instruções, consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 

* Um Mac com Xcode v7.0 ou posterior e Xamarin Studio Community instalado. Consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configuração, instalação e verificações para utilizadores Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar App Service](https://tryappservice.azure.com/?appServiceName=mobile) e comece de imediato a criar uma Aplicação Móvel de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.

## Criar um novo back-end da Aplicação Móvel do Azure

Siga estes passos para criar um novo back-end da Aplicação Móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, deve transferir um projeto de servidor para um back-end simples de uma “lista de tarefas” e publicá-lo no Azure.

## Configurar o projeto de servidor

Siga os passos abaixo para configurar o projeto de servidor para utilizar o back-end Node.js ou .NET.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (Opcional) Testar o projeto de back-end localmente

Caso tenha optado pela configuração de back-end .NET acima, opcionalmente, pode testar o back-end localmente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]


##Transferir e executar a aplicação Xamarin.Forms

Aqui tem duas opções. Pode transferir a solução para um Mac e abri-la no Xamarin Studio ou pode transferir a solução para um computador com o Windows e abri-la no Visual Studio, utilizando um Mac em rede para compilar a aplicação iOS. Consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) se necessitar de instruções mais detalhadas sobre os cenários de configuração do Xamarin.

Vamos prosseguir:

 1. No Mac ou no computador com o Windows, abra o [Portal do Azure] numa janela do browser.
 2. No painel de definições da Aplicação Móvel, clique em **Introdução** (em Móvel) > **Xamarin.Forms**. No passo 3, clique em **Criar uma nova aplicação**, se a opção ainda não estiver selecionada.  Em seguida, clique no botão **Transferir**.

    Este processo transfere um projeto que contém uma aplicação cliente que está ligada à sua aplicação móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

 3. Extraia o projeto que transferiu e, em seguida, abra-o no Xamarin Studio ou Visual Studio.

    ![][9]

    ![][8]


##(Opcional) Executar o projeto iOS

Esta secção destina-se à execução do projeto iOS Xamarin para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

####No Xamarin Studio

1. Clique com o botão direito do rato no projeto iOS e, em seguida, clique em **Configurar como Projeto de Arranque**.
2. No menu **Executar**, clique em **Iniciar Depuração** para compilar o projeto e iniciar a aplicação no emulador do iPhone.

####No Visual Studio
1. Clique com o botão direito do rato no projeto iOS e, em seguida, clique em **Configurar como Projeto de Arranque**.
2. No menu **Compilar**, clique em **Gestor de Configuração**.
3. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** do projeto iOS.
4. Prima a tecla **F5** para compilar o projeto e iniciar a aplicação no emulador do iPhone.

    >[AZURE.NOTE] Se tiver algum problema ao compilar, execute o gestor de pacote NuGet e atualize para versão mais recente dos pacotes de suporte do Xamarin. Por vezes, os projetos de Início Rápido podem ficar mais lentos durante a atualização para a versão mais recente.    

Na aplicação, digite um texto significativo, tal como _Saber mais sobre o Xamarin_, e clique no botão **+**.

![][10]

Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

>[AZURE.NOTE]
> Vai encontrar o código que acede ao back-end da aplicação móvel no ficheiro TodoItemManager.cs em C# do projeto da biblioteca de classe portátil da sua solução.

##(Opcional) Executar o projeto Android

Esta secção destina-se à execução do projeto droid Xamarin para Android. Pode ignorar esta secção se não estiver a trabalhar com dispositivos Android.

####No Xamarin Studio

1. Clique com o botão direito do rato no projeto Android e, em seguida, clique em **Configurar como Projeto de Arranque**.
2. No menu **Executar**, clique em **Iniciar Depuração** para compilar o projeto e iniciar a aplicação no emulador do Android.

####No Visual Studio
1. Clique com o botão direito do rato no projeto Android (Droid) e, em seguida, clique em **Configurar como Projeto de Arranque**.
4. No menu **Compilar**, clique em **Gestor de Configuração**.
5. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** do projeto Android.
6. Prima a tecla **F5** para compilar o projeto e iniciar a aplicação no emulador do Android.

    >[AZURE.NOTE] Se tiver algum problema ao compilar, execute o gestor de pacote NuGet e atualize para versão mais recente dos pacotes de suporte do Xamarin. Por vezes, os projetos de Início Rápido podem ficar mais lentos durante a atualização para a versão mais recente.    


Na aplicação, digite um texto significativo, tal como _Saber mais sobre o Xamarin_, e clique no botão **+**.

![][11]

Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

> [AZURE.NOTE]
> Vai encontrar o código que acede ao back-end da aplicação móvel no ficheiro TodoItemManager.cs em C# do projeto da biblioteca de classe portátil da sua solução.


##(Opcional) Executar o projeto Windows


Esta secção destina-se à execução do projeto WinApp Xamarin para dispositivos Windows. Pode ignorar esta secção se não estiver a trabalhar com dispositivos Windows.


####No Visual Studio
1. Clique com o botão direito do rato em qualquer projeto Windows e, em seguida, clique em **Configurar como Projeto de Arranque**.
4. No menu **Compilar**, clique em **Gestor de Configuração**.
5. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** do projeto Windows que escolher.
6. Prima a tecla **F5** para compilar o projeto e iniciar a aplicação no emulador do Windows.

    >[AZURE.NOTE] Se tiver algum problema ao compilar, execute o gestor de pacote NuGet e atualize para versão mais recente dos pacotes de suporte do Xamarin. Por vezes, os projetos de Início Rápido podem ficar mais lentos durante a atualização para a versão mais recente.    


Na aplicação, digite um texto significativo, tal como _Saber mais sobre o Xamarin_, e clique no botão **+**.

Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

![][12]

> [AZURE.NOTE]
> Vai encontrar o código que acede ao back-end da aplicação móvel no ficheiro TodoItemManager.cs em C# do projeto da biblioteca de classe portátil da sua solução.

##Passos seguintes

* [Adicionar autenticação à aplicação](app-service-mobile-xamarin-forms-get-started-users.md)  
Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Adicionar notificações push à aplicação](app-service-mobile-xamarin-forms-get-started-push.md)  
Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.

* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

* [Como utilizar o cliente gerido para Mobile Apps do Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Saiba como trabalhar com o SDK cliente gerido na aplicação Xamarin. 


<!-- Anchors. -->
[Introdução aos back-ends de Mobile Apps]:#getting-started
[Criar um novo back-end da aplicação móvel]:#create-new-service
[Passos Seguintes]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK da aplicação Móvel]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/




<!--HONumber=Aug16_HO1-->


