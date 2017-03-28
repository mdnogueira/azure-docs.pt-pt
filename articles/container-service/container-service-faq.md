---
title: Azure Container Service - FAQ | Microsoft Docs
description: "Respostas às perguntas mais frequentes sobre o Azure Container Service, um serviço que simplifica a criação, configuração e gestão de um cluster de máquinas virtuais para executar aplicações de contentor do Docker ."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microserviços, Mesos, Azure, Kubernetes"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 682ebb212f9056f3704a6de5dde8d3a35681108f
ms.lasthandoff: 03/17/2017


---
# <a name="container-service-frequently-asked-questions"></a>Perguntas mais frequentes sobre o Container Service


## <a name="orchestrators"></a>Orquestradores

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Que orquestradores de contentor suporta no Azure Container Service? 

Existe suporte para DC/SO de código aberto, Docker Swarm e Kubernetes. Para obter mais informações, consulte [Descrição Geral](container-service-intro.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Suporta o modo Docker Swarm? 

Atualmente, o modo Swarm não é suportado, mas é no plano de serviço. 

### <a name="does-azure-container-service-support-windows-containers"></a>O Azure Container Service suporta contentores do Windows?  

Atualmente são suportados contentores de Linux com todos os orquestradores. O suporte para contentores do Windows com Kubernetes está em pré-visualização.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Recomenda um orquestrador específico no Azure Container Service? 
Geralmente não recomendamos um orquestrador específico. Se tiver experiência com um dos orquestradores suportados, pode aplicar essa experiência no Azure Container Service. As tendências de dados sugerem, no entanto, que o DC/SO seja comprovado pela produção de Macrodados e as cargas de trabalho de IoT, o Kubernetes é adequado para cargas de trabalho de cloud nativa e o Docker Swarm é conhecido pela integração com ferramentas de Docker e uma fácil uma curva de aprendizagem.

Dependendo do seu cenário, também pode criar e gerir soluções de contentor personalizadas com outros serviços do Azure. Estes serviços incluem [Máquinas Virtuais](../virtual-machines/virtual-machines-linux-azure-overview.md), [Service Fabric](../service-fabric/service-fabric-overview.md), [Aplicações Web](../app-service-web/app-service-web-overview.md) e [Batch](../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Qual é a diferença entre o Azure Container Service e o Motor de ACS? 
O Azure Container Service é um serviço do Azure apoiado pelo SLA com funcionalidades no portal do Azure, ferramentas da linha de comandos do Azure e APIs do Azure. O serviço permite-lhe implementar e gerir rapidamente clusters que executam ferramentas de orquestração do contentor padrão com um número relativamente pequeno de opções de configuração. 

O [Motor de ACS](http://github.com/Azure/acs-engine) é um projeto de código aberto que permite aos utilizadores avançados personalizarem a configuração de cluster a todos os níveis. Esta capacidade de alterar a configuração da infraestrutura e do software significa que não oferecemos SLA para o Motor de ACS. O suporte é processado através do projeto de código aberto no GitHub, em vez de através de canais oficiais da Microsoft. 

## <a name="cluster-management"></a>Gestão de clusters

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Como criar chaves SSH para o meu cluster?

Pode utilizar as ferramentas padrão no seu sistema operativo para criar um par de chaves SSH RSA público e privado para autenticação relativamente às máquinas virtuais Linux do cluster. Para obter os passos, consulte a documentação de orientação [SO X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

Se utilizar [comandos do CLI do Azure 2.0](container-service-create-acs-cluster-cli.md) para implementar um cluster de serviço do contentor, as chaves SSH podem ser geradas automaticamente para o cluster.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Como posso criar um serviço principal para o meu cluster de Kubernetes?

Também são necessários uma palavra-passe e o ID principal de serviço do Azure Active Directory para criar um cluster de Kubernetes no Azure Container Service. Para obter mais informações, veja [About the service principal for a Kubernetes cluster (Sobre o principal de serviço para um cluster de Kubernetes)](container-service-kubernetes-service-principal.md).


Se utilizar os [comandos do CLI do Azure 2.0](container-service-create-acs-cluster-cli.md) para implementar um cluster de Kubernetes, as credenciais do principal de serviço podem ser geradas automaticamente para o cluster.


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Como posso aumentar o número de modelos de estrutura mestres depois de ser criado um cluster? 
Assim que o cluster for criado, o número de modelos de estrutura mestres é fixo e não pode ser alterado. Durante a criação do cluster, idealmente deve selecionar múltiplos modelos de estrutura mestres para elevada disponibilidade.


### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Como posso aumentar o número de agentes depois de ser criado um cluster? 
Pode ampliar o número de agentes no cluster com o portal do Azure ou ferramentas da linha de comandos. Consulte [Dimensionar um cluster do Azure Container Service](container-service-scale.md).


### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Quais são os URLs dos modelos de estrutura mestres e agentes? 
Os URLs dos recursos do cluster no Azure Container Service baseiam-se no prefixo do nome DNS que fornece e no nome da região do Azure que escolheu para a implementação. Por exemplo, o nome de domínio completamente qualificado (FQDN) do nó principal está neste formato:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Pode encontrar URLs frequentemente utilizados para o seu cluster no portal do Azure, o Explorador de Recursos do Azure ou outras ferramentas do Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Como posso saber qual a versão do orquestrador em execução no meu cluster?

* DC/OS: veja a [documentação do Mesosphere](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-)
* Docker Swarm: executar `docker version`
* Kubernetes: executar `kubectl version`


### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Como posso atualizar o orquestrador após a implementação?

Atualmente, o Azure Container Service não fornece ferramentas para atualizar a versão do orquestrador implementada num cluster. Se o Container Service suportar uma versão posterior, pode implementar um novo cluster. Outra opção consiste em utilizar ferramentas específicas do orquestrador, se estiverem disponíveis para atualizar um cluster no local. Por exemplo, veja [Atualizar DC/OS](https://dcos.io/docs/1.8/administration/upgrading/).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Onde posso encontrar a cadeia de ligação SSH do meu cluster?

Pode encontrar a cadeia de ligação no portal do Azure ou através das ferramentas da linha de comandos do Azure. 

1. No portal, navegue para o grupo de recursos da implementação do cluster.  

2. Clique em **Descrição Geral** e clique na ligação das **Implementações** em **Essenciais**. 

3. No painel **Histórico de implementação**, clique na implementação que tem um nome que começa por **microsoft-acs** seguido de uma data de implementação. Exemplo: microsoft-acs-201701310000.  

4. Na página **Resumo**, em **Saídas**, as várias ligações de cluster são fornecidas. O **SSHMaster0** fornece uma cadeia de ligação SSH ao primeiro modelo de estrutura mestre no cluster de serviço do contentor. 

Como mencionado anteriormente, também pode utilizar ferramentas do Azure para encontrar o FQDN do modelo de estrutura mestre. Efetue uma ligação SSH ao modelo de estrutura mestre através do FQDN do modelo de estrutura mestre e do nome de utilizador que especificou quando criou o cluster. Por exemplo:

```bash
ssh userName@masterFQDN –A –p 22 
```

Para mais informações, consulte [Connect to an Azure Container Service cluster (Ligar a um cluster do Azure Container Service)](container-service-connect.md).




## <a name="next-steps"></a>Passos seguintes

* [Saiba mais](container-service-intro.md) sobre o Azure Container Service.
* Implementar um cluster do serviço de contentor através do [portal](container-service-deployment.md) ou do [ CLI do Azure 2.0](container-service-create-acs-cluster-cli.md).

