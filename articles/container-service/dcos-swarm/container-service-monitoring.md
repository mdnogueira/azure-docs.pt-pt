---
title: Cluster do Azure DC/OS do monitor - Datadog | Microsoft Docs
description: "Monitorize um cluster do serviço de contentor do Azure com Datadog. Utilize a IU da web de DC/OS para implementar os agentes de Datadog ao cluster."
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Contentores, DC/SO, o Docker Swarm, Azure
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 9dd451f994940d7cc3a59bd7fd08a8f067345e34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Monitor de um cluster do serviço de contentor do Azure DC/SO com Datadog
Neste artigo, irá implementar agentes Datadog para todos os nós de agente no seu cluster do serviço de contentor do Azure. Precisa de uma conta com Datadog para esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implemente](container-service-deployment.md) e [ligue](../container-service-connect.md) um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Aceda a [http://datadoghq.com](http://datadoghq.com) para configurar uma conta de Datadog. 

## <a name="datadog"></a>Datadog
Datadog é um serviço de monitorização que recolhe dados de monitorização dos contentores num cluster do serviço de contentor do Azure. Datadog tem um Dashboard de integração do Docker onde pode ver métricas específicas dentro os contentores. Métricas recolhidas a partir dos contentores estão organizadas por da CPU, memória, rede e e/s. Datadog divide as métricas numa contentores e imagens. Um exemplo do aspeto da IU para utilização da CPU é abaixo.

![IU Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurar uma implementação de Datadog com Marathon
Estes passos mostram como configurar e implementar aplicações de Datadog para o cluster com o Marathon. 

Aceder à IU do DC/OS, através de [http://localhost:80 /](http://localhost:80/). Uma vez na IU do DC/OS, navegue para o "universo" que se encontre na parte inferior esquerda e, em seguida, procure "Datadog" e clique em "Instalar".

![Pacote de Datadog dentro do universo de DC/OS](./media/container-service-monitoring/datadog1.png)

Agora para concluir a configuração, terá de uma conta de Datadog ou uma conta de avaliação gratuita. Assim que tem sessão iniciada aspeto de Web site Datadog à esquerda e aceda a integrações ->, em seguida, [APIs](https://app.datadoghq.com/account/settings#api). 

![Chave de Datadog API](./media/container-service-monitoring/datadog2.png)

Em seguida, introduza a chave de API para a configuração de Datadog dentro do universo de DC/OS. 

![Configuração de Datadog no universo de DC/OS](./media/container-service-monitoring/datadog3.png) 

Na configuração acima instâncias estão definidas como 10000000, pelo que, sempre que é adicionado um novo nó ao cluster Datadog irão implementar automaticamente um agente para esse nó. Esta é uma solução provisória. Depois de instalar o pacote deve navegue de volta para o Web site Datadog e localize "[Dashboards](https://app.datadoghq.com/dash/list)." A partir daí, verá personalizada e Dashboards de integração. O [Docker dashboard](https://app.datadoghq.com/screen/integration/docker) terão todas as métricas de contentor que precisa para a monitorização do seu cluster. 

