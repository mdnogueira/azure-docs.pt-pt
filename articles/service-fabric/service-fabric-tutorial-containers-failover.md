---
title: "Ativação pós-falha e dimensionar uma aplicação de contentores do Azure Service Fabric | Microsoft Docs"
description: "Saiba como a ativação pós-falha é processada de uma aplicação de contentores do Azure Service Fabric.  Além disso, saiba como dimensionar os contentores e serviços em execução num cluster."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, contentores, micro-serviços, recursos de infraestrutura de serviço, do Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 21dd9dfbc90c26236c43e2c334305ca97f63d361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Demonstrar falha over e dimensionamento dos serviços de contentor com o Service Fabric

Este tutorial é parte três de uma série. Neste tutorial, saiba como a ativação pós-falha é processada em aplicações de contentor do Service Fabric. Além disso, saiba como dimensionar os contentores. Neste tutorial:

> [!div class="checklist"]
> * Saiba mais sobre a ativação pós-falha do contentor de um cluster do Service Fabric  
> * Dimensionar os contentores de front-end da web de uma appliction

## <a name="prerequisites"></a>Pré-requisitos
A aplicação do [parte 2](service-fabric-tutorial-package-containers.md) está em execução num cluster Service Fabric Active Directory.

## <a name="fail-over-a-container-in-a-cluster"></a>Efetuar a ativação pós-falha de um contentor num cluster
Certifica-se das instâncias de contentor move automaticamente a outros nós do cluster, deve ocorrer uma falha de Service Fabric. Pode também manualmente drenar um nó de contentores e movê-los corretamente para outros nós do cluster. Se tiver várias formas de dimensionar os seus serviços, neste exemplo, estamos a utilizar o Service Fabric Explorer.

Para efetuar a ativação pós-falha do contentor de front-end, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Clique em de **fabric: / TestContainer/azurevotefront** nó na vista de árvore e expanda o nó de partição (representado por um GUID). Tenha em atenção o nome de nó na treeview, que mostra os nós do contentor está atualmente em execução no - por exemplo`_nodetype_1`
3. Expanda o **nós** nó na treeview. Clique no botão de reticências (reticências) junto ao nó que esteja a executar o contentor.
1. Escolha **Reiniciar** para reiniciar esse nó e confirme a ação de reinício. O reinício provoca a ativação pós-falha do contentor para outro nó no cluster.

![noderestart][noderestart]

Tenha em atenção a como o nome de nó que indica onde os contentores de front-end execução, agora é alterado para outro nó no cluster. Após alguns instantes, deve ser capaz de procurar novamente a aplicação e ver a aplicação agora em execução num nó diferente.

## <a name="scale-containers-and-services-in-a-cluster"></a>Dimensionar a contentores e dos serviços de um cluster
Podem ser escalados para contentores de Service Fabric através de um cluster para acomodar a carga nos serviços. Dimensionar um contentor ao alterar o número de instâncias em execução no cluster.

Dimensionar web front-end, efetue os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Clique no botão de reticências (reticências) junto ao **fabric: / TestContainer/azurevotefront** nó na árvore da ver e escolha **escala serviço**.

![sfxscale][sfxscale]

Agora pode optar por aumentar o número de instâncias de web front-end.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no **fabric: / TestContainer/azurevotefront** nó na árvore ver e expanda o nó de partição (representado por um GUID).

![sfxscaledone][sfxscaledone]

Agora, pode ver que o serviço tem duas instâncias. Na vista de árvore, poderá ver quais os nós executam as instâncias.

Através desta simples tarefa de gestão, duplicámos os recursos disponíveis para o nosso serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para o executar de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância de serviço é executado no cluster.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, a ativação pós-falha do contentor foi demonstrada, bem como o dimensionamento de uma aplicação. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Saiba mais sobre a ativação pós-falha do contentor de um cluster do Service Fabric  
> * Dimensionar os contentores de front-end da web de uma appliction

Esta série tutorial, aprendeu como: 
> [!div class="checklist"]
> * Criar imagens de contentor
> * Imagens de contentor de push para o registo de contentor do Azure
> * Contentores de pacote para o Service Fabric utilizando Yeoman
> * Compilar e executar uma aplicação de recursos de infraestrutura de serviço com contentores
> * Como a ativação pós-falha e dimensionamento são processados no Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png
