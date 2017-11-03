---
title: Cluster do Azure DC/OS do monitor - Dynatrace | Microsoft Docs
description: "Monitorize um cluster do serviço de contentor do Azure DC/SO com Dynatrace. Implemente o Dynatrace OneAgent através do dashboard de DC/OS."
services: container-service
documentationcenter: 
author: MartinGoodwell
manager: 
editor: 
tags: acs, azure-container-service
keywords: Contentores, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6fa23728680779e33eda7bb9aa8a01b9cad9a82b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>Monitor de um cluster do serviço de contentor do Azure DC/SO com Dynatrace SaaS/gerido
Neste artigo, vamos mostrar-lhe como implementar o [Dynatrace](https://www.dynatrace.com/) OneAgent para monitorizar todos os nós de agente no seu cluster do serviço de contentor do Azure. Precisa de uma conta com Dynatrace SaaS/gerida para esta configuração. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/geridos
Dynatrace é uma solução de monitorização de nuvem nativo para o contentor altamente dinâmica e ambientes de cluster. Permite-lhe otimizar o melhor a implementações de contentores e as alocações de memória utilizando dados de utilização em tempo real. Tem a capacidade para pinpointing automaticamente problemas de infraestrutura e de aplicação, fornecendo automatizada de linha de base, correlação de problema e deteção da causa raiz.

A figura seguinte mostra a IU de Dynatrace:

![IU Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Pré-requisitos 
[Implementar](container-service-deployment.md) e [ligar](./../container-service-connect.md) para um cluster configurado pelo serviço de contentor do Azure. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Aceda a [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) para configurar uma conta de Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configurar uma implementação de Dynatrace com Marathon
Estes passos mostram como configurar e implementar aplicações de Dynatrace para o cluster com o Marathon.

1. Aceder à IU do DC/OS, através de [http://localhost:80 /](http://localhost:80/). Uma vez na IU do DC/OS, navegue para o **universo** separador e, em seguida, procure **Dynatrace**.

    ![Dynatrace no universo de DC/SO](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Para concluir a configuração, precisa de uma conta de Dynatrace SaaS ou uma conta de avaliação gratuita. Uma vez que iniciar sessão no Dynatrace dashboard, selecionar **implementar Dynatrace**.

    ![Conjunto de Dynatrace a integração de PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na página, selecione **configurar a integração de PaaS**. 

    ![Token Dynatrace API](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Introduza o seu token de API para a configuração de Dynatrace OneAgent dentro do universo de DC/OS. 

    ![Configuração de Dynatrace OneAgent no universo de DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Defina as instâncias para o número de nós que pretende executar. Definir um número mais alto também funciona, mas o DC/SO irá continuar a tentar encontrar novas instâncias até esse número for atingido, na verdade. Se preferir, pode também configurar esta para um valor como 1000000. Neste caso, sempre que é adicionado um novo nó ao cluster, o Dynatrace implementa automaticamente um agente para esse nó novo, o preço do DC/SO constantemente a tentar implementar instâncias adicionais.

    ![Configuração de Dynatrace no DC/SO universo-instâncias](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Passos seguintes

Uma vez que tem instalado o pacote, navegue de volta para o dashboard Dynatrace. Pode explorar as métricas de utilização diferentes para os contentores no seu cluster. 