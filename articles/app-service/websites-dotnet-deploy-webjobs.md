---
title: Desenvolver e implementar WebJobs com o Visual Studio - Azure
description: "Saiba como desenvolver e implementar WebJobs do Azure para o serviço de aplicações do Azure com o Visual Studio."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 8793485f2f1967e8c14ef335f14f81c9b946f974
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Desenvolver e implementar WebJobs com o Visual Studio - App Service do Azure

## <a name="overview"></a>Descrição geral

Este tópico explica como utilizar o Visual Studio para implementar um projeto de aplicação de consola para uma aplicação web no [do serviço de aplicações](app-service-web-overview.md) como um [trabalho Web do Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implementar WebJobs utilizando o [portal do Azure](https://portal.azure.com), consulte [tarefas de execução em segundo plano com WebJobs](web-sites-create-web-jobs.md).

Quando o Visual Studio implementa um projeto de aplicação de consola com WebJobs, executa duas tarefas:

* Copia os ficheiros de tempo de execução para a pasta adequada na aplicação web (*App_Data/tarefas/contínua* para WebJobs contínuos, *App_Data/tarefas/acionada* para WebJobs agendadas e a pedido).
* Configura [tarefas do agendador do Azure](#scheduler) para WebJobs que estejam agendados para execução em alturas específicas. (Isto não é necessária para WebJobs contínuos.)

Um projeto WebJobs capacidade tem os seguintes itens adicionados ao mesmo:

* O [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) pacote NuGet.
* A [settings.json webjob publicar](#publishsettings) ficheiro que contém as definições de implementação e programador. 

![Diagrama que mostra o que é adicionado a uma aplicação de consola para ativar a implementação, como um trabalho Web](./media/websites-dotnet-deploy-webjobs/convert.png)

Pode adicionar estes itens a um projeto de aplicação de consola existente ou utilize um modelo para criar um novo projeto de aplicação de consola com WebJobs. 

Pode implementar um projeto como um WebJob por si só ou ligá-la a um projeto web para que implementa automaticamente sempre que implementar o projeto web. Para ligar projetos, o Visual Studio inclui o nome do projeto WebJobs ativado num [webjobs list.json](#webjobslist) ficheiros no projeto web.

![Diagrama que mostra o projeto de WebJob a associar ao projeto web](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Pré-requisitos

Se estiver a utilizar o Visual Studio 2015, instale o [Azure SDK para .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se estiver a utilizar o Visual Studio 2017, instale o [carga de trabalho de desenvolvimento do Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a>Ativar a implementação de WebJobs para um projeto de aplicação de consola existente

Tem duas opções:

* [Ativar a implementação automática com um projeto web](#convertlink).

  Configure um projeto de aplicação de consola existente para que o se implementa automaticamente como um WebJob quando implementar um projeto web. Utilize esta opção quando pretender executar o seu trabalho Web na mesma aplicação web em que executa a aplicação web relacionados.

* [Ativar a implementação sem um projeto web](#convertnolink).

  Configure um projeto de aplicação de consola existente para implementar como um WebJob por si só, sem ligação com um projeto web. Utilize esta opção quando pretender executar um trabalho Web na aplicação web por si só, com nenhuma aplicação web em execução na aplicação web. Pode querer fazê-lo para poder dimensionar os recursos de trabalho Web independentemente os recursos de aplicação web.

### <a id="convertlink"></a>Ativar a implementação automática de WebJobs com um projeto web

1. Clique com o botão direito no projeto web na **Explorador de soluções**e, em seguida, clique em **adicionar** > **projeto existente como o trabalho Web do Azure**.
   
    ![Projeto existente como o trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    O [trabalho Web do Azure de adicionar](#configure) é apresentada a caixa de diálogo.
2. No **nome do projeto** na lista pendente, selecione a aplicação de consola do projeto para adicionar como um WebJob.
   
    ![Selecionar o projeto na caixa de diálogo Adicionar WebJob do Azure](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Concluir o [trabalho Web do Azure de adicionar](#configure) caixa de diálogo e, em seguida, clique em **OK**. 

### <a id="convertnolink"></a>Ativar a implementação de WebJobs sem um projeto web
1. Clique com o botão direito no projeto de aplicação de consola em **Explorador de soluções**e, em seguida, clique em **publicar como trabalho Web do Azure...** . 
   
    ![Publicar como trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    O [trabalho Web do Azure de adicionar](#configure) aparece a caixa de diálogo, com o projeto selecionado no **nome do projeto** caixa.
2. Concluir o [trabalho Web do Azure de adicionar](#configure) caixa de diálogo e, em seguida, clique em **OK**.
   
   O **publicar Web** é apresentado o assistente.  Se não pretender publicar imediatamente, feche o assistente. As definições que introduziu são guardadas para a quando pretende [implementar o projeto](#deploy).

## <a id="create"></a>Criar um novo projeto WebJobs-ativado
Para criar um novo projeto WebJobs ativado, pode utilizar o modelo de projeto de aplicação de consola e ativar a implementação de WebJobs conforme explicado no [secção anterior](#convert). Como alternativa, pode utilizar o modelo de novo projeto de WebJobs:

* [Utilizar o modelo de novo projeto de WebJobs para um WebJob independente](#createnolink)
  
    Criar um projeto e configurá-lo para implementar por si próprio como um WebJob, sem ligação com um projeto web. Utilize esta opção quando pretender executar um trabalho Web na aplicação web por si só, com nenhuma aplicação web em execução na aplicação web. Pode querer fazê-lo para poder dimensionar os recursos de trabalho Web independentemente os recursos de aplicação web.
* [Utilizar o modelo de novo projeto de WebJobs para um WebJob associado a um projeto web](#createlink)
  
    Crie um projeto que está configurado para implementar automaticamente como um WebJob quando um projeto web na mesma solução for implementado. Utilize esta opção quando pretender executar o seu trabalho Web na mesma aplicação web em que executa a aplicação web relacionados.

> [!NOTE]
> O modelo de novo projeto de WebJobs automaticamente instala os pacotes NuGet e inclui código *Program.cs* para o [SDK de WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se não pretender utilizar o SDK de WebJobs, remover ou alterar o `host.RunAndBlock` instrução no *Program.cs*.
> 
> 

### <a id="createnolink"></a>Utilizar o modelo de novo projeto de WebJobs para um WebJob independente
1. Clique em **ficheiro** > **novo projeto**e, em seguida, no **novo projeto** clique da caixa de diálogo **nuvem**  >   **Trabalho Web do Azure (.NET Framework)**.
   
    ![Caixa de diálogo do projeto nova que mostra o modelo do trabalho Web](./media/websites-dotnet-deploy-webjobs/np.png)
2. Siga as instruções apresentadas anteriormente a [tornar a aplicação de consola projetar um projeto de WebJobs independente](#convertnolink).

### <a id="createlink"></a>Utilizar o modelo de novo projeto de WebJobs para um WebJob associado a um projeto web
1. Clique com o botão direito no projeto web na **Explorador de soluções**e, em seguida, clique em **adicionar** > **novo projeto do trabalho Web do Azure**.
   
    ![Nova entrada de menu do projeto de trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    O [trabalho Web do Azure de adicionar](#configure) é apresentada a caixa de diálogo.
2. Concluir o [trabalho Web do Azure de adicionar](#configure) caixa de diálogo e, em seguida, clique em **OK**.

## <a id="configure"></a>A caixa de diálogo Adicionar WebJob do Azure
O **trabalho Web do Azure de adicionar** caixa de diálogo permite-lhe introduzir o nome do WebJob e executar a definição do modo para o trabalho Web. 

![Adicionar caixa de diálogo do trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Os campos nesta caixa de diálogo correspondem aos campos no **adicionar WebJob** caixa de diálogo do portal do Azure. Para obter mais informações, consulte [tarefas de execução em segundo plano com WebJobs](web-sites-create-web-jobs.md).

> [!NOTE]
> * Para obter informações sobre a implementação da linha de comandos, consulte [ativação da linha de comandos ou entrega de Azure WebJobs contínuos](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se implementar um WebJob e, em seguida, decida que pretende alterar o tipo de WebJob e volte a implementar, terá de eliminar o *settings.json webjobs publicar* ficheiro. Esta ação irá tornar o Visual Studio para mostrar as opções de publicação novamente, para que possa alterar o tipo do trabalho Web.
> * Se implementar um WebJob e posteriormente, altera o modo de execução do contínua para não contínuos ou vice-versa, o Visual Studio cria um WebJob novo no Azure, quando implementar novamente. Se alterar outras definições de agendamento, mas deixe modo de execução da mesma ou alternar entre agendada e a pedido, Visual Studio atualiza a tarefa existente em vez de criar um novo.
> 
> 

## <a id="publishsettings"></a>settings.json webjob publicar
Quando configura uma aplicação de consola para a implementação de WebJobs, instala o Visual Studio a [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet pacote e armazena informações de agendamento um *settings.json webjob publicar*  ficheiros no projeto *propriedades* pasta do projeto WebJobs. Eis um exemplo desse ficheiro:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Pode editar este ficheiro diretamente e o Visual Studio fornece IntelliSense. O esquema do ficheiro é armazenado em [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) e podem ser visualizados não existe.  

## <a id="webjobslist"></a>webjobs list.json
Quando liga um projeto ativada de WebJobs para um projeto web, o Visual Studio armazena o nome do projeto WebJobs num *webjobs list.json* ficheiro o projeto web *propriedades* pasta. A lista pode conter vários projetos de WebJobs, conforme mostrado no exemplo seguinte:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Pode editar este ficheiro diretamente e o Visual Studio fornece IntelliSense. O esquema do ficheiro é armazenado em [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) e podem ser visualizados não existe.

## <a id="deploy"></a>Implementar um projeto de WebJobs
Um projeto de WebJobs de ligação a um projeto web implementa automaticamente com o projeto web. Para obter informações sobre a implementação do projeto web, consulte **guias de procedimentos** > **implementar aplicações** no painel de navegação esquerdo.

Para implementar um projeto de WebJobs por si só, clique com botão direito no projeto em **Explorador de soluções** e clique em **publicar como trabalho Web do Azure...** . 

![Publicar como trabalho Web do Azure](./media/websites-dotnet-deploy-webjobs/paw.png)

Para um WebJob independente, o mesmo **publicar Web** assistente que é utilizado para projetos web é apresentada, mas com menos definições disponíveis para alterar.
