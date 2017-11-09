---
title: "Implementar uma aplicação de Service Fabric do Azure para um Cluster de terceiros | Microsoft Docs"
description: "Saiba como implementar uma aplicação para um Cluster de terceiros."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: d7496b0578301713ebae7381e9a54642e226eb96
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Implementar uma aplicação para um Cluster de terceiros no Azure
Este tutorial é parte dois de uma série e mostra-lhe como implementar uma aplicação de Service Fabric do Azure para um Cluster de terceiros no Azure.

Na parte dois da série tutorial, saiba como:
> [!div class="checklist"]
> * Implementar uma aplicação para um cluster remoto, utilizando o Visual Studio
> * Remover uma aplicação de um cluster utilizando o Service Fabric Explorer

Este tutorial série, a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Implementar a aplicação para um cluster remoto
> * [Configurar CI/CD utilizando o Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar a monitorização e diagnóstico para a aplicação](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instalar Visual Studio 2017](https://www.visualstudio.com/) e instalar o **programação do Azure** e **desenvolvimento ASP.NET e web** cargas de trabalho.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Transferir a aplicação de exemplo de voto
Se não foi possível criar a aplicação de exemplo de voto [parte de um desta série tutorial](service-fabric-tutorial-create-dotnet-app.md), poderá transferi-lo. Na janela da linha de comandos, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-a-party-cluster"></a>Configurar um Cluster de terceiros
Os party clusters são clusters do Service Fabric gratuitos, limitados temporalmente, alojados no Azure e executados pela equipa do Service Fabric, nos quais qualquer pessoa pode implementar aplicações e saber mais sobre a plataforma. Gratuitamente!

Para obter acesso a um Cluster de terceiros, navegue para este site: http://aka.ms/tryservicefabric e siga as instruções para obter acesso a um cluster. Necessita de uma conta do Facebook ou GitHub para obter acesso a um Cluster de terceiros.

Pode utilizar o seu próprio cluster em vez do Cluster de terceiros, se pretender.  O web de núcleo ASP.NET front-end utiliza o proxy inverso para comunicar com o back-end de serviço com estado.  Clusters intervenientes e o cluster de desenvolvimento local tem proxy inverso ativado por predefinição.  Se implementar a aplicação de exemplo de voto para o seu próprio cluster, tem de [ativar o proxy inverso no cluster](service-fabric-reverseproxy.md#setup-and-configuration).

> [!NOTE]
> Não são protegidos clusters intervenientes, para que as suas aplicações e dados que colocar nas mesmas podem ser visíveis para outros utilizadores. Não implementa qualquer coisa que não pretende que os outros possam ver. Não se esqueça de ler sobre nossos termos de utilização para todos os detalhes.

## <a name="deploy-the-app-to-the-azure"></a>Implementar a aplicação do Azure
Agora que a aplicação esteja pronta, pode implementá-la para o Cluster de terceiros direta a partir do Visual Studio.

1. Clique com botão direito **voto** no Explorador de soluções e escolha **publicar**.

    ![Caixa de diálogo Publicar](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. Introduza o ponto final da ligação de Cluster de terceiros no **ponto final de ligação** campo e clique em **publicar**.

    Depois do publicar estiver concluída, deverá conseguir enviar um pedido para a aplicação através de um browser.

3. Abra o browser preferencial e escreva o endereço de cluster (o ligação ponto final sem as informações de porta - por exemplo, win1kw5649s.westus.cloudapp.azure.com).

    Deverá ver o mesmo resultado como que viu quando executar a aplicação localmente.

    ![Resposta de API do Cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Remover a aplicação de um cluster utilizando o Service Fabric Explorer
Explorador do Service Fabric é uma interface de utilizador gráfica para explorar e gerir aplicações num cluster de Service Fabric.

Para remover a aplicação do Cluster de terceiros:

1. Navegue para o Service Fabric Explorer, através da ligação fornecida pela página de inscrição do Cluster de terceiros. Por exemplo, http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html.

2. No Service Fabric Explorer, navegue para o **fabric://Voting** nó na treeview no lado esquerdo.

3. Clique em de **ação** botão o direito **Essentials** painel e escolha **eliminar aplicação**. Confirme a eliminação da instância da aplicação, que elimina a instância do nosso aplicação em execução no cluster.

![Eliminar a aplicação no Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

## <a name="remove-the-application-type-from-a-cluster-using-service-fabric-explorer"></a>Remover o tipo de aplicação de um cluster utilizando o Service Fabric Explorer
As aplicações são implementadas como tipos de aplicações num cluster de Service Fabric, que permite-lhe ter várias instâncias e as versões da aplicação em execução no cluster. Depois de ter removido a instância da nossa aplicação em execução, pode também Removemos o tipo, para concluir a limpeza da implementação.

Para obter mais informações sobre o modelo de aplicação no Service Fabric, consulte [modelar uma aplicação no Service Fabric](service-fabric-application-model.md).

1. Navegue para o **VotingType** nó na treeview.

2. Clique em de **ação** botão o direito **Essentials** painel e escolha **tipo de não aprovisionamento**. Confirme a anulação de aprovisionamento do tipo de aplicação.

![Anular o aprovisionamento de tipo de aplicação no Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

Isto conclui o tutorial.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implementar uma aplicação para um cluster remoto, utilizando o Visual Studio
> * Remover uma aplicação de um cluster utilizando o Service Fabric Explorer

Avançar para o próximo tutorial:
> [!div class="nextstepaction"]
> [Configurar a integração contínua com o Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)