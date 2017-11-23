---
title: "Criar uma aplicação de Java de recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Crie uma aplicação Java do Azure utilizando o exemplo de início rápido do Service Fabric."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: c4966f3ddc95a7e7c97d09cd45abdb8443601b74
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="create-a-java-application"></a>Criar uma aplicação de Java
Azure Service Fabric é uma plataforma de sistemas distribuídos para implementar e gerir micro-serviços e contentores. 

Este guia de introdução mostra como implementar a sua primeira aplicação de Java para o Service Fabric com o IDE Eclipse numa máquina de programador do Linux. Quando tiver terminado, terá uma aplicação de voto com um Java web front-end que guarda os resultados de votos no serviço de back-end com monitorização de estado no cluster.

![Captura de ecrã da aplicação](./media/service-fabric-quickstart-java/votingapp.png)

Neste início rápido, vai aprender a:

> [!div class="checklist"]
> * Utiliza o Eclipse como uma ferramenta para as suas aplicações de Java de recursos de infraestrutura de serviço
> * Implementar a aplicação no seu cluster local 
> * Implementar a aplicação para um cluster no Azure
> * A aplicação em vários nós de escalamento horizontal

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido:
1. [Instalar o SDK do Service Fabric & Interface de linha de comandos (CLI) do Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instale o Git](https://git-scm.com/).
3. [Instalar o Eclipse](https://www.eclipse.org/downloads/)
4. [Configurar o ambiente de Java](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), efetuar se a seguir os passos opcionais para instalar o plug-in do Eclipse 

## <a name="download-the-sample"></a>Transferir o exemplo
Na janela da linha de comandos, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local.
```
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente
1. Inicie o seu cluster local executando o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    O arranque do local cluster demora algum tempo. Para confirmar que o cluster está totalmente operacional, aceder ao Service Fabric Explorer em **http://localhost:19080**. Os cinco nós em bom estado indicam que ao cluster local está a funcionar. 
    
    ![Cluster local bom estado de funcionamento](./media/service-fabric-quickstart-java/localclusterup.png)

2. Abra o Eclipse.
3. Clique em ficheiro -> Abra projetos a partir do sistema de ficheiros... 
4. Clique em diretório e escolha o `Voting` diretório do `service-fabric-java-quickstart` pasta clonado a partir do Github. Clique em Concluir. 

    ![Caixa de diálogo de importação de Eclipse](./media/service-fabric-quickstart-java/eclipseimport.png)
    
5. Tem agora o `Voting` projeto no Explorador do pacote para o Eclipse. 
6. Clique no projeto e selecione **publicar a aplicação...**  sob o **Service Fabric** pendente. Escolha **PublishProfiles/Local.json** como o perfil de destino e clique em publicar. 

    ![Publicar a caixa de diálogo Local](./media/service-fabric-quickstart-java/localjson.png)
    
7. Abra o browser Favoritos e aceder à aplicação acedendo **http://localhost:8080**. 

    ![Local de front-end de aplicação](./media/service-fabric-quickstart-java/runninglocally.png)
    
Agora pode adicionar um conjunto de opções de voto e começar a tirar votos. A aplicação é executada e armazena todos os dados no seu cluster do Service Fabric, sem a necessidade de uma base de dados separada.

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurar o Cluster do Azure Service Fabric
Para implementar a aplicação para um cluster no Azure, criar o seu próprio cluster ou utilizar um Cluster de terceiros.

Party clusters são clusters do Service Fabric gratuitos e de tempo limitado, alojados no Azure. São executados pela equipa do Service Fabric em que qualquer pessoa pode implementar aplicações e saber mais sobre a plataforma. Para obter acesso a um Party Cluster, [siga as instruções](http://aka.ms/tryservicefabric). 

Para obter informações sobre como criar o seu próprio cluster, consulte [criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> O serviço de front-end da web está configurado para escutar na porta 8080 para o tráfego de entrada. Certifique-se de que a porta está aberta no seu cluster. Se estiver a utilizar o Cluster de terceiros, esta porta é aberta.
>

### <a name="deploy-the-application-using-eclipse"></a>Implementar a aplicação utilizar Eclipse
Agora que a aplicação e o cluster estiverem prontos, pode implementá-la para o cluster diretamente a partir do Eclipse.

1. Abra o **Cloud.json** de ficheiros sob o **PublishProfiles** diretório e preencha a `ConnectionIPOrURL` e `ConnectionPort` campos adequadamente. É fornecido um exemplo: 

    ```bash
    {
         "ClusterConnectionParameters": 
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "",
            "ClientCert": ""
         }
    }
    ```

2. Clique no projeto e selecione **publicar a aplicação...**  sob o **Service Fabric** pendente. Escolha **PublishProfiles/Cloud.json** como o perfil de destino e clique em publicar. 

    ![Publicar a nuvem de caixa de diálogo](./media/service-fabric-quickstart-java/cloudjson.png)

3. Abra o browser Favoritos e aceder à aplicação acedendo **http://\<ConnectionIPOrURL >: 8080**. 

    ![Nuvem de front-end de aplicação](./media/service-fabric-quickstart-java/runningcloud.png)
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster
Podem ser escalados para serviços em cluster para acomodar uma alteração da carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster. Tem várias formas de dimensionamento os seus serviços, pode utilizar scripts ou comandos da CLI de recursos de infraestrutura de serviço (sfctl). Neste exemplo, estamos a utilizar Service Fabric Explorer.

Explorador de recursos de infraestrutura de serviço é executado em todos os clusters de Service Fabric e podem ser acedido a partir de um browser, ao navegar para a porta de gestão de clusters HTTP (19080), por exemplo, `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`.

Para dimensionar o serviço de front-end da Web, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`.
2. Clique no botão de reticências (reticências) junto a **recursos de infraestrutura: voto/VotingWeb** nó na treeview e escolha **escala serviço**.

    ![Escala de Explorador de recursos de infraestrutura do serviço](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Agora, pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no **fabric: / voto/VotingWeb** nó na vista de árvore e expanda o nó de partição (representado por um GUID).

    ![Recursos de infraestrutura Explorer escala serviço concluído](./media/service-fabric-quickstart-java/servicescaled.png)

    Agora, pode ver que o serviço tem duas instâncias e na vista de árvore ver quais as instâncias de executam os nós.

Através desta simples tarefa de gestão, duplicámos os recursos disponíveis para o nosso serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para o executar de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância de serviço é executado no cluster.

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Utiliza o Eclipse como uma ferramenta para as suas aplicações de Java de recursos de infraestrutura de serviço
> * Implementar aplicações de Java ao local cluster 
> * Implementar aplicações de Java para um cluster no Azure
> * A aplicação em vários nós de escalamento horizontal

* Saiba mais sobre [depuração serviços em Java utilizando o Eclipse](service-fabric-debugging-your-application-java.md)
* Saiba mais sobre [como configurar a sua integreation contínua & implementação utilizando Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* Consulte outro [exemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)