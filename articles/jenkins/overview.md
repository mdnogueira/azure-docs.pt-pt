---
title: "Descrição geral dos Jenkins e do Azure | Microsoft Docs"
description: "A compilação de Jenkins de anfitriões e implementar o servidor de automatização no Azure e utilizar recursos de computação e de armazenamento do Azure para expandir a sua integração contínua e implementação (CI/CD) pipelines."
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: daa202ddf0dc934c491ead3951ddc4fdc3dd819c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-and-jenkins"></a>O Azure e Jenkins

[Jenkins](https://jenkins.io/) é um servidor de automatização de open source popular utilizado para configurar a integração contínua e entrega (CI/CD) para os projetos de software. Pode alojar a sua implementação Jenkins no Azure ou expandir a sua configuração existente de Jenkins com recursos do Azure. O plug-ins de Jenkins também estão disponíveis para simplificar a CI/CD das suas aplicações para o Azure.

Este artigo é uma introdução à utilização do Azure com Jenkins, com detalhes sobre as principais Azure as funcionalidades disponíveis para os utilizadores de Jenkins. Para começar a utilizar com o seu próprio servidor Jenkins no Azure, consulte a nossa [início rápido](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hospedar os seus servidores Jenkins no Azure

Anfitrião Jenkins no Azure para centralizar a automatização de compilação e dimensionar a sua implantação, à medida que aumentam as necessidades dos seus projetos de software. Pode implementar Jenkins no Azure com:
 
- [O modelo de solução Jenkins](install-jenkins-solution-template.md) no Azure Marketplace.
- [Máquinas virtuais do Azure](/azure/virtual-machines/linux/overview). Consulte a nossa [tutorial](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) para criar uma instância de Jenkins numa VM.
- Num Kubernetes cluster em execução em [serviço de contentor Azure](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), consulte a nossa [procedimentos](/azure/container-service/kubernetes/container-service-kubernetes-jenkin).

Monitorizar e gerir a sua implementação do Azure Jenkins utilizando [Log Analytics](/azure/log-analytics/log-analytics-overview), [Operations Management Suite](/azure/operations-management-suite/operations-management-suite-overview)e [CLI do Azure] (/ cli / / descrição geral do azure).

## <a name="scale-your-build-automation-on-demand"></a>Dimensionar a automatização de compilação a pedido

Adicione os agentes de compilação para a sua implementação Jenkins existente ao dimensionar a capacidade de compilação Jenkins como o número de compilações e complexidade das suas tarefas e aumentam pipelines. Pode executar estes criar agentes em máquinas virtuais do Azure utilizando o [Plug-in de agentes de VM do Azure](jenkins-azure-vm-agents.md). Consulte a nossa [tutorial](/azure/jenkins/jenkins-azure-vm-agents) para obter mais detalhes.

Uma vez configurado com um [principal de serviço do Azure](/azure/azure-resource-manager/resource-group-overview), Jenkins tarefas e pipelines podem utilizar esta credencial para:

- Armazene de maneira segura e arquivo criar artefactos [Storage do Azure](/azure/storage/common/storage-introduction) utilizando o [Plug-in do Storage do Azure](https://plugins.jenkins.io/windows-azure-storage). Reveja o [Jenkins armazenamento procedimentos](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) para obter mais informações.
- Gerir e configurar recursos do Azure com o [CLI do Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Implementar o código para os serviços do Azure

Utilize o plug-ins de Jenkins para implementar as suas aplicações para o Azure como parte da sua pipelines Jenkins CI/CD. Implementar numa [App Service do Azure](/azure/app-service/) e [serviço de contentor Azure](/azure/container-service/kubernetes/) permite-lhe fase, teste e atualizações de lançamento para as suas aplicações sem gerir a infraestrutura subjacente.

 Plug-ins estão disponíveis para implementar os seguintes serviços e ambientes:

- [Aplicação Web do Azure no Linux](/azure/app-service/containers/app-service-linux-intro). Consulte o [tutorial](java-deploy-webapp-tutorial.md) para começar a utilizar.
- [Aplicação Web do Azure](/azure/app-service/app-service-web-overview). Consulte o [procedimentos](deploy-Jenkins-app-service-plugin.md) para começar a utilizar.

