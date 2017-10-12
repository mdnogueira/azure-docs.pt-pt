---
title: "Introdução às Aplicações Móveis com o Xamarin.Forms"
description: "Siga este tutorial para começar a utilizar Aplicações Móveis para desenvolvimento do Xamarin.Forms"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: ee12caaad4095cff6dae3282f747ae804f93db81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-xamarinforms-app"></a>Criar uma aplicação Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end com base na cloud a uma aplicação móvel Xamarin.Forms com a funcionalidade Aplicações Móveis do Serviço de Aplicações do Azure como o back-end. Pode criar tanto um novo back-end das Aplicações Móveis assim como uma simples aplicação Xamarin.Forms de uma lista de tarefas que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações Xamarin.Forms.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais informações, veja [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/) (Avaliação Gratuita do Azure).

* Visual Studio com Xamarin. Para informações, veja a página [Set up and install Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Configurar e instalar o Visual Studio e Xamarin).

* Um Mac com Xcode v7.0 ou posterior e Xamarin Studio Community instalado. Para obter mais informações, veja [Set up and install Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Configuração e instalação do Visual Studio e Xamarin) e [Set up, install, and verify for Mac users](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN) (Configuração, instalação e verificações para utilizadores Mac).

## <a name="create-a-new-mobile-apps-back-end"></a>Criar um novo back-end de Aplicações Móveis

Para criar um novo back-end das Aplicações Móveis, faça o seguinte:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Definiu agora um back-end de Aplicações Móveis que as suas aplicações de cliente móveis podem utilizar. Em seguida, transfira um projeto de servidor para um back-end de lista de tarefas simples e, em seguida, publique-o no Azure.

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor

Para configurar o projeto de servidor para utilizar o back-end Node.js ou .NET., faça o seguinte:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Transferir e executar a aplicação Xamarin.Forms

Pode transferir a solução de duas formas. Pode transferi-la para um Mac e abri-la no Xamarin Studio ou pode transferi-la para um computador com o Windows e abri-la no Visual Studio com um Mac em rede para compilar a aplicação iOS. Para informações, veja [Set up and install Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Configurar e instalar o Visual Studio e Xamarin).

Num computador Mac ou Windows, faça o seguinte:

1. Aceda ao [Portal do Azure].

2. No painel **Definições** da aplicação móvel, em **Móvel**, selecione **Introdução** > **Xamarin.Forms**. No **passo 3**, selecione **Criar uma nova aplicação** e, em seguida, selecione **Transferir**.

   Esta ação transfere um projeto que contém uma aplicação cliente que está ligada à sua aplicação móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

3. Extraia o projeto que transferiu e, em seguida, abra-o no Xamarin Studio (Mac) ou Visual Studio (Windows).

   ![Projeto extraído no Xamarin Studio][9]

   ![Projeto extraído no Visual Studio][8]

## <a name="optional-run-the-ios-project"></a>(Opcional) Executar o projeto iOS
Nesta secção irá executar o projeto iOS Xamarin para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

#### <a name="in-xamarin-studio"></a>No Xamarin Studio
1. Clique com o botão direito do rato no projeto iOS e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. No menu **Executar**, selecione **Iniciar Depuração** para compilar o projeto e iniciar a aplicação no emulador do iPhone.

#### <a name="in-visual-studio"></a>No Visual Studio
1. Clique com o botão direito do rato no projeto iOS e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. No menu **Compilar**, selecionar **Gestor de Configuração**.

3. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** junto ao projeto iOS.

4. Para compilar o projeto e iniciar a aplicação no emulador do iPhone, selecione a tecla **F5**.

   > [!NOTE]
   > Se tiver algum problema ao compilar o projeto, execute o gestor de pacote NuGet e atualize para versão mais recente dos pacotes de suporte do Xamarin. Os projetos do guia de introdução podem ser lentos para atualizar para versões mais recentes.    
   >
   >

5. Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição (**+**).

    ![][10]

    Esta ação envia um pedido post para o novo back-end das Aplicações Móveis que está alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens que estão armazenados na tabela são devolvidos pelo back-end das Aplicações Móveis e os dados são apresentados na lista.

    > [!NOTE]
    > Vai encontrar o código que acede ao back-end da Aplicação Móvel no ficheiro TodoItemManager.cs em C# do projeto da biblioteca de classe portátil da sua solução.
    >
    >

## <a name="optional-run-the-android-project"></a>(Opcional) Executar o projeto Android
Nesta secção irá executar o projeto droid Xamarin para Android. Pode ignorar esta secção se não estiver a trabalhar com dispositivos Android.

#### <a name="in-xamarin-studio"></a>No Xamarin Studio

1. Clique com o botão direito do rato no projeto Android e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. Para compilar o projeto e iniciar a aplicação num emulador do Android, no menu **Executar**, selecione **Iniciar Depuração**.

#### <a name="in-visual-studio"></a>No Visual Studio

1. Clique com o botão direito do rato no projeto Android (Droid) e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. No menu **Compilar**, selecionar **Gestor de Configuração**.

3. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** junto ao projeto Android.

4. Para compilar o projeto e iniciar a aplicação no emulador do Android, selecione a tecla **F5**.

   > [!NOTE]
   > Se tiver algum problema ao compilar o projeto, execute o gestor de pacote NuGet e atualize para versão mais recente dos pacotes de suporte do Xamarin. Os projetos do guia de introdução podem ser lentos para atualizar para versões mais recentes.    
   >
   >

5. Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição (**+**).

    ![][11]
    
    Esta ação envia um pedido post para o novo back-end das Aplicações Móveis que está alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens que estão armazenados na tabela são devolvidos pelo back-end das Aplicações Móveis e os dados são apresentados na lista.
    
    > [!NOTE]
    > Vai encontrar o código que acede ao back-end da Aplicação Móvel no ficheiro TodoItemManager.cs em C# do projeto da biblioteca de classe portátil da sua solução.
    >
    >

## <a name="optional-run-the-windows-project"></a>(Opcional) Executar o projeto Windows

Nesta secção irá executar o projeto WinApp Xamarin para dispositivos Windows. Pode ignorar esta secção se não estiver a trabalhar com dispositivos Windows.

#### <a name="in-visual-studio"></a>No Visual Studio

1. Clique com o botão direito do rato em qualquer projeto Windows e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. No menu **Compilar**, selecionar **Gestor de Configuração**.

3. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** junto ao projeto Windows que escolher.

4. Para compilar o projeto e iniciar a aplicação no emulador do Windows, selecione a tecla **F5**.

   > [!NOTE]
   > Se tiver algum problema ao compilar o projeto, execute o gestor de pacote NuGet e atualize para versão mais recente dos pacotes de suporte do Xamarin. Os projetos do guia de introdução podem ser lentos para atualizar para versões mais recentes.    
   >
   >

5. Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição (**+**).

    Esta ação envia um pedido post para o novo back-end das Aplicações Móveis que está alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens que estão armazenados na tabela são devolvidos pelo back-end das Aplicações Móveis e os dados são apresentados na lista.
    
    ![][12]
    
    > [!NOTE]
    > Vai encontrar o código que acede ao back-end da Aplicação Móvel no ficheiro TodoItemManager.cs em C# do projeto da biblioteca de classe portátil da sua solução.
    >
    >

## <a name="next-steps"></a>Passos seguintes

* [Adicionar autenticação à aplicação](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Adicionar notificações push à aplicação](app-service-mobile-xamarin-forms-get-started-push.md)  
  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Hubs de Notificação do Azure para enviar as notificações push.

* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação com um back-end de Aplicações Móveis. Com a sincronização offline, pode ver, adicionar ou modificar dados da sua aplicação móvel, mesmo quando não existe nenhuma ligação de rede.

* [Utilizar o cliente gerido para Aplicações Móveis](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Saiba como trabalhar com o SDK cliente gerido na aplicação Xamarin.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/
