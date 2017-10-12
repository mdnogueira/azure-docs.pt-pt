---
title: "Criar uma Plataforma Universal do Windows (UWP) nas Aplicações Móveis | Microsoft Docs"
description: "Siga este tutorial para começar a utilizar back-ends da aplicação móvel do Azure para o desenvolvimento da aplicação Plataforma Universal do Windows (UWP) em C#, Visual Basic ou JavaScript."
services: app-service\mobile
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 5408e032670dda7f149c442e08f52b02abe23f05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-app"></a>Criar uma aplicação do Windows
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Plataforma Universal do Windows (UWP). Para obter mais informações, consulte [O que são Mobile Apps](app-service-mobile-value-prop.md). As capturas de ecrã seguintes mostram a aplicação completa:

![Aplicação de ambiente de trabalho completa](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Em execução num ambiente de trabalho.

![Aplicação de telemóvel completa](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Em execução num telemóvel.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações UWP.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio Community 2015] ou uma versão posterior.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end da Aplicação Móvel do Azure
Siga estes passos para criar um novo back-end da Aplicação Móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, deve transferir um projeto de servidor para um back-end simples de uma “lista de tarefas” e publicá-lo no Azure.

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Transferir e executar o projeto de cliente
Depois de configurar o back-end da Aplicação Móvel, pode criar uma nova aplicação de cliente ou modificar uma aplicação existente para se ligar ao Azure. Nesta secção, vai transferir um projeto de modelo de aplicação UWP personalizado para se ligar ao back-end da Aplicação Móvel.

1. De volta ao painel **Início Rápido** do back-end da Aplicação Móvel, clique em **Criar uma nova aplicação** > **Transferir**, em seguida, extraia os ficheiros do projeto comprimido para o computador local.

    ![Transferir o projeto de início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)
2. (Opcional) Adicione o projeto de aplicação UWP para a mesma solução como o projeto de servidor. Isto torna mais fácil a depuração e o teste da aplicação e do back-end na mesma solução Visual Studio, caso opte por fazê-lo. Para adicionar um projeto de aplicação UWP à solução, deve utilizar o Visual Studio 2015 ou uma versão posterior.
3. Com a aplicação UWP como o projeto de arranque, prima a tecla F5 para implementar e executar a aplicação.
4. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, na caixa de texto **Inserir um Item da Lista a Fazer** e, em seguida, clique no botão **Guardar**.

    ![Ambiente de trabalho completo do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel que está alojado no Azure.
5. (Opcional) Pare a aplicação e reinicie-a num dispositivo diferente ou emulador móvel.

    ![Telemóvel completo do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Tenha em atenção que os dados guardados no passo anterior são carregados a partir do Azure após a aplicação UWP iniciar.

## <a name="next-steps"></a>Passos seguintes
* [Adicionar autenticação à aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Adicionar notificações push à aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203
