---
title: "Criar a sua primeira função no Azure usando o Visual Studio | Microsoft Docs"
description: "Criar e publicar uma simples função ativada por HTTP no Azure usando ferramentas de funções do Azure para Visual Studio."
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/05/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 4a6b706b63c4e1b0df3c46bce4ff6877efca4ead
ms.contentlocale: pt-pt
ms.lasthandoff: 08/02/2017

---
# <a name="create-your-first-function-using-visual-studio"></a>Criar a sua primeira função com o Visual Studio

As Funções do Azure permitem-lhe executar o seu código num ambiente sem servidor, sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

> [!IMPORTANT]
> Este tópico utiliza uma Versão de Pré-visualização do Visual Studio para concluir os passos descritos. Antes de continuar, confirme que tem instalada a [versão 15.3 da Pré-visualização do Visual Studio 2017](https://www.visualstudio.com/vs/preview/).

Neste tópico, aprenderá a usar as ferramentas de função do Azure para Visual Studio 2017 para criar e testar localmente uma função de "hello world". Em seguida, publicará o código de função no Azure.

![Código de funções do Azure num projeto do Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, instale:

* [Versão de pré-visualização do Visual Studio 2017, versão 15.3](https://www.visualstudio.com/vs/preview/), incluindo a carga de trabalho de **desenvolvimento do Azure**.

    ![Instalar o Visual Studio 2017 com a carga de trabalho de desenvolvimento do Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-functions-tools-for-visual-studio-2017"></a>Instalar as ferramentas de funções do Azure para Visual Studio 2017

Antes de começar, deverá transferir e instalar as ferramentas de funções do Azure para Visual Studio 2017. Essas ferramentas só podem ser usadas com a versão 15.3 do Visual Studio 2017 Preview ou uma versão posterior. Se já tiver instalado as ferramentas de funções do Azure, pode ignorar esta secção.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Criar um projeto de funções do Azure no Visual Studio

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Agora que criou o projeto, pode criar sua primeira função.

## <a name="create-the-function"></a>Criar a função

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto e selecione **Adicionar** > **Novo item**. Selecione **Função do Azure** e clique em **Adicionar**.

2. Selecione **HttpTrigger**, digite o **Nome da função**, selecione **Anónimo** para **Direitos de acesso**e clique em **Criar**. A função criada é acedida por uma solicitação HTTP por parte de qualquer cliente. 

    ![Criar uma nova função do Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Agora que criou uma função acionada por HTTP, poderá testá-la no seu computador local.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar o projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.  

1. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI).  Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.  

    ![Tempo de execução local do Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Cole o URL do pedido HTTP na barra de endereço do browser. Anexe a cadeia de consulta `&name=<yourname>` a este URL e execute o pedido. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função: 

    ![Resposta da função localhost no browser](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. Para interromper a depuração, clique no botão **Parar** na barra de ferramentas do Visual Studio.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. Pode criar uma aplicação de funções diretamente a partir do Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base. Tal como antes, certifique-se de que anexa a cadeia de consulta `&name=<yourname>` a este URL e execute o pedido.

    O URL que remete para a função ativada por HTTP tem esta aparência:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função: 

    ![Resposta da função no browser](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio para criar uma aplicação de função C# com uma função simples acionada por HTTP. 

+ Para saber como configurar o seu projeto para suportar outros tipos de acionadores e enlaces, veja a secção [Configure the project for local development](functions-develop-vs.md#configure-the-project-for-local-development) (Configurar o projeto de desenvolvimento local) em [Azure Functions Tools for Visual Studio](functions-develop-vs.md) (Ferramentas das Funções do Azure para Visual Studio).
+ Para saber mais sobre o teste e depuração local usando as ferramentas de núcleo de funções do Azure, consulte [código e teste de funções do Azure localmente](functions-run-local.md). 
+ Para obter mais informações sobre o desenvolvimento de funções como bibliotecas de classes do .NET, veja [Utilizar bibliotecas de classes do .NET com as Funções do Azure](functions-dotnet-class-library.md). 


