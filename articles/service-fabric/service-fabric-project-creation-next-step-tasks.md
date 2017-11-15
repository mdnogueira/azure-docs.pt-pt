---
title: "Próximos passos de criação do Service Fabric projeto | Microsoft Docs"
description: "Este artigo contém ligações para um conjunto de tarefas de desenvolvimento do núcleo para o Service Fabric"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: rwike77
ms.openlocfilehash: e04f9e57c65da42da73a5ee6a0b601dcbb318aaa
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>A aplicação de Service Fabric e os passos seguintes
Foi criada a sua aplicação de Service Fabric do Azure. Este artigo descreve makeup do seu projeto e alguns passos potenciais.

## <a name="your-application"></a>A aplicação
Cada nova aplicação inclui um projeto de aplicação. Pode haver um ou dois projetos adicionais, dependendo do tipo de serviço escolhido.

### <a name="the-application-project"></a>O projeto de aplicação
O projeto de aplicação é composta por:

* Um conjunto de referências para os serviços que compõem a sua aplicação.
* Publicar três perfis (1-nó Local, 5-nó Local e nuvem) que pode utilizar para manter as preferências para trabalhar com ambientes diferentes – como preferências relacionados com um ponto final de cluster e se pretende efetuar implementações de atualização por predefinição.
* Parâmetro de aplicação três ficheiros (igual ao acima) que pode utilizar para manter as configurações de aplicação específico do ambiente, como o número de partições para criar um serviço.
* Um script de implementação que pode utilizar para implementar a aplicação na linha de comandos ou como parte de um pipeline de integração e a implementação contínuo automatizado.
* O manifesto da aplicação, que descreve a aplicação. Pode encontrar o manifesto na pasta ApplicationPackageRoot.

### <a name="stateless-service"></a>Sem estado
Quando adiciona um novo serviço sem monitorização de estado, o Visual Studio adiciona um projeto de serviço à sua solução que inclui um tipo descended de `StatelessService`. O serviço incrementa uma variável local um contador.

### <a name="stateful-service"></a>Serviço com estado
Quando adiciona um novo serviço de monitorização de estado, o Visual Studio adiciona um projeto de serviço à sua solução que inclui um tipo descended de `StatefulService`. O serviço incrementa um contador no respetivo `RunAsync` método e armazena o resultado num `ReliableDictionary`.

### <a name="actor-service"></a>Serviço de ator
Quando adiciona um novo ator fiável, o Visual Studio adiciona dois projetos à sua solução: um projeto de ator e um projeto de interface.

O projeto de ator fornece métodos para definição e obter o valor de um contador que é fiável continuada no estado do ator. O projeto de interface fornece uma interface que outros serviços podem utilizar para invocar o ator.

### <a name="stateless-web-api"></a>API de Web sem monitorização de estado
O projeto Web API sem monitorização de estado fornece um serviço web básico que pode utilizar para abrir a aplicação para clientes externos. Para obter mais informações sobre como o projeto estruturado, consulte [serviços de API Web do Service Fabric com OWIN automática de alojamento](service-fabric-reliable-services-communication-webapi.md).


### <a name="aspnet-core"></a>Núcleo ASP.NET
O SDK de Service Fabric fornece o mesmo conjunto de ASP.NET Core modelos que estão disponíveis para autónomo ASP.NET Core projetos: vazio, [Web API][aspnet-webapi], e [aplicação Web][aspnet-webapp].

### <a name="guest-executables-and-guest-containers"></a>Contentores executáveis e de convidado de convidado

Um recurso de infraestrutura do serviço 'Convidado' é um serviço que não está incorporado com modelos de programação da plataforma. Pode incluir os binários para um convidado está [diretamente no pacote de aplicação](service-fabric-deploy-existing-app.md) ou [através de uma imagem de contentor](service-fabric-deploy-container.md). Em ambos os casos, o Visual Studio cria os artefactos necessários no **ApplicationPackageRoot** pasta do projeto de aplicação. Visual Studio não irá criar um novo projeto de serviço porque o código já existe noutro local. Se pretende gerir os seus projetos de convidado juntamente com o projeto de aplicação de Service Fabric, pode adicioná-los para a mesma solução Visual Studio.

## <a name="next-steps"></a>Passos seguintes
### <a name="create-an-azure-cluster"></a>Criar um cluster do Azure
O SDK de Service Fabric fornece um cluster local para desenvolvimento e testes. Para criar um cluster no Azure, consulte [configurar um cluster do Service Fabric do portal do Azure][create-cluster-in-portal].

### <a name="publish-your-application-to-azure"></a>Publicar a aplicação no Azure
Pode publicar a aplicação diretamente a partir do Visual Studio para um cluster do Azure. Para saber como, consulte [publicar a aplicação no Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Utilize o Explorador do Service Fabric para visualizar o cluster
Service Fabric Explorer proporciona uma forma fácil de visualizar o cluster, incluindo aplicações implementadas e o esquema físico. Para obter mais informações, consulte [visualizar o cluster utilizando o Service Fabric Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Versão e atualizar os serviços
Service Fabric permite o controlo de versões independente e atualização dos serviços independentes de uma aplicação. Para obter mais informações, consulte [controlo de versões e atualizar os serviços][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurar a integração contínua com o Visual Studio Team Services
Para saber como pode definir um processo de integração contínua para a sua aplicação de Service Fabric, consulte [configurar a integração contínua com o Visual Studio Team Services][ci-with-vso].

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-manage-application-in-visual-studio.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
