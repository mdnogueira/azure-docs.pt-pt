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
ms.openlocfilehash: 5766ef2097b0da295d42e7c5909efc524049f418
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
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

## <a name="configure-the-listening-port"></a>Configurar a porta de escuta
Quando o serviço de front-end VotingWeb é criado, o Visual Studio seleciona aleatoriamente uma porta para o serviço de escuta no.  O serviço de VotingWeb atua como o front-end para esta aplicação e aceita o tráfego externo, por isso, vamos vincular que o serviço para fixa e conhecer bem porta. No Explorador de soluções, abra *VotingWeb/PackageRoot/ServiceManifest.xml*.  Localizar o **Endpoint** recurso no **recursos** secção e altere o **porta** valor a 80.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Também de atualizar o valor da propriedade URL da aplicação no projeto de voto, para que um web browser abre-se para a porta correta quando depurar a utilizar 'F5'.  No Explorador de soluções, selecione o **voto** projeto e atualize o **URL da aplicação** propriedade.

![URL da aplicação](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

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