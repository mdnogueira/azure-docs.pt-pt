---
title: Clusters de contentor do Docker na cloud do Azure | Microsoft Docs
description: "O Azure Container Service proporciona uma forma de simplificar a criação, configuração e gestão de um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações em contentores."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microserviços, Mesos, Azure"
ms.assetid: 52da4163-1182-4b2e-be00-4951e5c1da16
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: cc3cc2dce324942f184b6a520dc4db28518a3091
ms.openlocfilehash: 0521fbd689233c0f2359a10006e24c75c8997732


---
# <a name="azure-container-service-introduction"></a>Introdução ao Azure Container Service
O Azure Container Service simplifica a criação, configuração e gestão de um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações em contentores. Utiliza uma configuração otimizada de populares ferramentas open-source de agendamento e orquestração. Isto permite-lhe utilizar as suas competências ou basear-se num corpo extenso e crescente de conhecimentos da comunidade, para implementar e gerir aplicações baseadas em contentores do Microsoft Azure.

![O Azure Container Service fornece um meio para gerir aplicações em contentores, em vários anfitriões no Azure.](./media/acs-intro/acs-cluster-new.png)

O Azure Container Service melhora o formato de contentor Docker para assegurar que os contentores das aplicações são totalmente portáteis. Também suporta a sua escolha de Marathon e DC/OS, Docker Swarm ou Kubernetes, de modo a que possa ampliar estas aplicações para milhares ou mesmo dezenas de milhares de contentores.

>[!NOTE]
> O suporte de Kubernetes no Azure Container Service está atualmente em pré-visualização.
>

Ao utilizar o Azure Container Service, pode tirar partido das funcionalidades de nível empresarial do Azure, mantendo a portabilidade da aplicação - incluindo a portabilidade nas camadas de orquestração.

## <a name="using-azure-container-service"></a>Utilizar o Azure Container Service
O nosso objetivo para o Azure Container Service é fornecer um ambiente de alojamento de contentores, utilizando ferramentas e tecnologias open-source que são atualmente populares entre os nossos clientes. Para este fim, vamos expor os pontos finais de API padrão para o orquestrador escolhido (DC/OS, Docker Swarm ou Kubernetes). Ao utilizar estes pontos finais, pode tirar partido de qualquer software que consiga comunicar com esses pontos finais. Por exemplo, no caso do ponto final de Docker Swarm, poderá optar por utilizar a interface Docker de linha de comandos (CLI). Para DC/OS, poderá optar por utilizar a CLI DCOS. Para Kubernetes, pode optar por utilizar kubectl.

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Criar um cluster de Docker com o Azure Container Service
Para começar a utilizar o Azure Container Service, implemente um cluster do Azure Container Service através do portal (pesquise “Azure Container Service”), com um modelo do Azure Resource Manager ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm), [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) ou [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)) ou com a [CLI](/articles/xplat-cli-install.md). Os modelos de início rápido fornecidos podem ser modificados para incluir configuração do Azure, adicional ou avançada. Para obter mais informações sobre a implementação de um cluster do Azure Container Service, veja [Implementar um cluster do Azure Container Service](container-service-deployment.md).

## <a name="deploying-an-application"></a>Implementar uma aplicação
O Azure Container Service permite escolher entre Docker Swarm, DC/OS ou Kubernetes para orquestração. O modo como implementa a aplicação depende da sua escolha do orquestrador.

### <a name="using-dcos"></a>Utilizar DC/OS
O DC/OS é um sistema operativo distribuído, baseado no kernel Apache Mesos de sistemas distribuídos. O Apache Mesos tem a sua sede na Apache Software Foundation e tem alguns dos [maiores nomes de TI](http://mesos.apache.org/documentation/latest/powered-by-mesos/) como utilizadores e contribuintes.

![O Azure Container Service configurado para Swarm, a mostrar agentes e mestres.](media/acs-intro/dcos.png)

O DC/OS e o Apache Mesos incluem um conjunto impressionante de funcionalidades:

* Escalabilidade comprovada
* Mestre e subordinados replicados com tolerância a falhas, utilizando o Apache ZooKeeper
* Suporte para contentores formatados para Docker
* Isolamento nativo entre tarefas com contentores Linux
* Agendamento de vários recursos (memória, CPU, disco e portas)
* APIs Java, Python e C++ para o desenvolvimento de novas aplicações paralelas
* Uma IU da Web para ver o estado do cluster

Por predefinição, o DC/OS que está em execução no Azure Container Service inclui a plataforma de orquestração Marathon para cargas de trabalho de agendamento. No entanto, a implementação do DC/OS de ACS inclui o Universo Mesosphere dos serviços que podem ser adicionados ao seu serviço, incluindo Spark, Hadoop, Cassandra e muito mais.

![Universo DC/OS no Azure Container Service](media/dcos/universe.png)

#### <a name="using-marathon"></a>Utilizar o Marathon
O Marathon é uma unidade ampla de cluster e controlo de sistema em grupos ou, no caso do Azure Container Service, contentores formatados para Docker. O Marathon fornece uma IU da Web a partir da qual pode implementar as aplicações. Pode aceder-lhe com um URL semelhante a `http://DNS_PREFIX.REGION.cloudapp.azure.com`, em que DNS\_PREFIX e REGION são definidos no momento da implementação. Como é óbvio, também pode fornecer o seu nome DNS. Para obter mais informações sobre como executar um contentor utilizando a IU da Web do Marathon, veja [Gestão de contentores através da IU da Web](container-service-mesos-marathon-ui.md).

![Lista de Aplicações Marathon](media/dcos/marathon-applications-list.png)

Também pode utilizar as APIs REST para comunicar com o Marathon. Existem várias bibliotecas de cliente disponíveis para cada ferramenta. Abrangem uma variedade de idiomas - e, obviamente, pode utilizar o protocolo HTTP em qualquer idioma. Além disso, muitas ferramentas DevOps populares fornecem suporte para o Marathon. Isto proporciona flexibilidade máxima para a sua equipa de operações quando estiver a trabalhar com um cluster do Azure Container Service. Para obter mais informações sobre como executar um contentor utilizando a API REST do Marathon, veja [Gestão de contentores com a API REST](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>Utilizar o Docker Swarm
O Docker Swarm fornece clustering nativo para o Docker. Uma vez que o Docker Swarm serve a API padrão do Docker, qualquer ferramenta que já comunique com um daemon Docker pode utilizar o Swarm para dimensionar de forma transparente para vários anfitriões no Azure Container Service.

![O Azure Container Service configurado para utilizar DC/OS, mostrando a jumpbox, agentes e mestres.](media/acs-intro/acs-swarm2.png)

As ferramentas suportadas para a gestão de contentores num cluster do Swarm incluem, mas não se limitam às seguintes:

* Dokku
* CLI do Docker e Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>Utilizar Kubernetes
O Kubernetes é uma ferramenta popular de orquestrador de contentor de nível de produção e de código aberto para implementação automatizada de contentores, dimensionamento e gestão de aplicações contentorizadas. Uma vez que é uma solução de código aberto e é controlada pela comunidade de código aberto, é executada de forma totalmente integrada no Azure Container Service e pode ser utilizada para implementar contentores em escala no serviço.

![Azure Container Service configurado para utilizar Kubernets, que mostra agentes e principais.](media/acs-intro/kubernetes.png)

Tem um conjunto avançado de funcionalidades, incluindo:
* Dimensionamento horizontal
* Deteção do serviço e balanceamento de carga
* Gestão de segredos e configuração
* Implementações e reversões automáticas com base em API
* Autorrecuperação
* e muitas outras.



## <a name="videos"></a>Vídeos
Introdução ao Azure Container Service (101):  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

Criar Aplicações com o Azure Container Service (Compilação 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>



<!--HONumber=Feb17_HO1-->


