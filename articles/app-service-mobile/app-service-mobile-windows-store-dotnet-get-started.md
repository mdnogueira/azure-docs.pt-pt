<properties
    pageTitle="Criar uma Plataforma Universal do Windows (UWP) nas Mobile Apps | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar back-ends da aplicação móvel do Azure para o desenvolvimento da aplicação Plataforma Universal do Windows (UWP) em C#, Visual Basic ou JavaScript."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="glenga"/>

#Criar uma aplicação do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Descrição geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Plataforma Universal do Windows (UWP). Para obter mais informações, consulte [O que são Mobile Apps](app-service-mobile-value-prop.md). As capturas de ecrã seguintes mostram a aplicação completa:

![Aplicação de ambiente de trabalho completa](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Em execução num ambiente de trabalho. 

![Aplicação de telemóvel completa](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Em execução num telemóvel.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações UWP. 

##Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2015] ou uma versão posterior.

>[AZURE.NOTE] Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://tryappservice.azure.com/?appServiceName=mobile). Aqui, pode criar imediatamente uma aplicação móvel de arranque de curta duração no App Service: sem cartões de crédito, sem compromissos.

##Criar um novo back-end da Aplicação Móvel do Azure

Siga estes passos para criar um novo back-end da Aplicação Móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, deve transferir um projeto de servidor para um back-end simples de uma “lista de tarefas” e publicá-lo no Azure.

## Configurar o projeto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##Transferir e executar o projeto de cliente

Depois de configurar o back-end da Aplicação Móvel, pode criar uma nova aplicação de cliente ou modificar uma aplicação existente para se ligar ao Azure. Nesta secção, vai transferir um projeto de modelo de aplicação UWP personalizado para se ligar ao back-end da Aplicação Móvel.

1. De volta ao painel **Início Rápido** do back-end da Aplicação Móvel, clique em **Criar uma nova aplicação** > **Transferir**, em seguida, extraia os ficheiros do projeto comprimido para o computador local.

    ![Transferir o projeto de início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

3. (Opcional) Adicione o projeto de aplicação UWP para a mesma solução como o projeto de servidor. Isto torna mais fácil a depuração e o teste da aplicação e do back-end na mesma solução Visual Studio, caso opte por fazê-lo. Para adicionar um projeto de aplicação UWP à solução, deve utilizar o Visual Studio 2015 ou uma versão posterior.

4. Com a aplicação UWP como o projeto de arranque, prima a tecla F5 para implementar e executar a aplicação.

5. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, na caixa de texto **Inserir um Item da Lista a Fazer** e, em seguida, clique no botão **Guardar**.

    ![Ambiente de trabalho completo do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel que está alojado no Azure.

6. (Opcional) Pare a aplicação e reinicie-a num dispositivo diferente ou emulador móvel.

    ![Telemóvel completo do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Tenha em atenção que os dados guardados no passo anterior são carregados a partir do Azure após a aplicação UWP iniciar. 

##Passos seguintes

* [Adicionar autenticação à aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Adicionar notificações push à aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.

* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[SDK da Aplicação Móvel]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=ago16_HO4-->


