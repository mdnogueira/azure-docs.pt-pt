---
title: "Criar uma aplicação de contentor do Azure Service Fabric no Linux | Microsoft Docs"
description: "Crie a sua primeira aplicação de contentor do Linux no Azure Service Fabric.  Crie uma imagem do Docker com a sua aplicação, envie-a para um registo de contentor e crie e implemente uma aplicação de contentor do Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.openlocfilehash: 42a2542b7564f1692146b3e3927792ac339f30be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Implementar uma aplicação de contentor do Linux do Azure Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis. 

Este guia de início rápido mostra como implementar contentores do Linux num cluster do Service Fabric. Após terminar, terá uma aplicação de voto constituída por um front-end da Web Python e um back-end Redis em execução num cluster do Service Fabric. 

![quickstartpic][quickstartpic]

Neste início rápido, vai aprender a:
> [!div class="checklist"]
> * Implementar contentores do Linux no Service Fabric
> * Dimensionar e efetuar a ativação pós-falha de contentores no Service Fabric

## <a name="prerequisite"></a>Pré-requisito
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/) antes de começar.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a interface de linha de comandos (CLI) localmente, certifique-se de que está a executar a versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute az - versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Obter o pacote de aplicação
Para implementar contentores no Service Fabric, precisa de um conjunto de ficheiros de manifesto (a definição da aplicação), que descrevem os contentores individuais e a aplicação.

No Cloud Shell, utilize o git para clonar uma cópia da definição da aplicação.

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

cd service-fabric-dotnet-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>Implementar os contentores num cluster do Service Fabric no Azure
Para implementar a aplicação num cluster no Azure, utilize o seu próprio cluster ou utilize um Cluster de grupo.

Party clusters são clusters do Service Fabric gratuitos e de tempo limitado, alojados no Azure. Estes são mantidos pela equipa do Service Fabric, onde qualquer pessoa pode implementar aplicações e saber mais sobre a plataforma. Para obter acesso a um Cluster de grupo, [siga as instruções](http://aka.ms/tryservicefabric). 

Para obter informações sobre como criar o seu próprio cluster, veja [Crie o seu primeiro cluster do Service Fabric no Azure](service-fabric-get-started-azure-cluster.md).

> [!Note]
> O serviço de front-end da Web está configurado para escutar tráfego de entrada na porta 80. Certifique-se de que a porta está aberta no seu cluster. Se estiver a utilizar um Cluster de grupo, esta porta estará aberta.
>

### <a name="deploy-the-application-manifests"></a>Implementar os manifestos da aplicação 
Instale a [CLI do Service Fabric (sfctl)](service-fabric-cli.md) no ambiente da CLI

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```
Ligue ao cluster do Service Fabric no Azure com a CLI do Azure. O ponto final é o ponto final de gestão do seu cluster - por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

Utilize o script de instalação fornecido para copiar a definição da aplicação de Voto para o cluster, registar o tipo de aplicação e criar uma instância da mesma.

```azurecli-interactive
./install.sh
```

Abra um browser e navegue até ao Service Fabric Explorer em http://\<my-azure-service-fabric-cluster-url>:19080/Explorer – por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Expanda o nó de Aplicações para ver que existe agora uma entrada para o tipo de aplicação de Voto e a instância que criou.

![Service Fabric Explorer][sfx]

Ligue-se ao contentor em execução.  Abra um browser, apontando para o URL do seu cluster - por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Deverá ver a aplicação de Voto no browser.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Efetuar a ativação pós-falha de um contentor num cluster
O Service Fabric garante que as instâncias de contentor são movidas automaticamente para outros nós no cluster, caso ocorra uma falha. Também pode drenar manualmente um nó para contentores e, em seguida, mover harmoniosamente para outros nós do cluster. Se tiver várias formas de dimensionar os seus serviços, neste exemplo, estamos a utilizar o Service Fabric Explorer.

Para efetuar a ativação pós-falha do contentor de front-end, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Clique no nó **fabric:/Voting/azurevotefront** na vista de árvore e expanda o nó de partição (representado por uma GUID). Tenha em atenção o nome do nó na vista de árvore, que mostra os nós nos quais o contentor está atualmente a ser executado - por exemplo `_nodetype_4`
3. Expanda o nó **Nós** na vista de árvore. Clique nas reticências (três pontos) junto ao nó que está a executar o contentor.
4. Escolha **Reiniciar** para reiniciar esse nó e confirme a ação de reinício. O reinício provoca a ativação pós-falha do contentor para outro nó no cluster.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster
Os serviços do Service Fabric podem ser facilmente dimensionados num cluster para se prepararem para a carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster.

Para dimensionar o serviço de front-end da Web, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) junto ao nó **fabric:/Voting/azurevotefront** na vista de árvore e escolha **Dimensionar Serviço**.

    ![containersquickstartscale][containersquickstartscale]

  Agora, pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/azurevotefront** na vista de árvore e expanda o nó de partição (representado por uma GUID).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Agora, pode ver que o serviço tem duas instâncias. Na vista de árvore, pode ver em que nós as instâncias são executadas.

Através desta simples tarefa de gestão, duplicámos os recursos disponíveis para o nosso serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para o executar de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância de serviço é executado no cluster.

## <a name="clean-up"></a>Limpeza
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação do cluster local e anular o registo do tipo de aplicação. Este comando demora algum tempo a limpar a instância e o comando «install'sh» não deve ser executado imediatamente a seguir a este script. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprendeu a:
> [!div class="checklist"]
> * Implementar uma aplicação de contentor do Linux no Azure
> * Efetuar a ativação pós-falha de um contentor num cluster do Service Fabric
> * Dimensionar um contentor num cluster do Service Fabric

* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
* Veja os [exemplos de código de contentor do Service Fabric ](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
