---
title: Monitorizar um cluster do Azure DC/SO - ELK pilha | Microsoft Docs
description: "Monitorize um cluster DC/OS em cluster do serviço de contentor do Azure com ELK (Elasticsearch, Logstash e Kibana)."
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Contentores, DC/OS, Azure, monitorização, elk"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcfa277cdd0f3cebc0fbbb23e771fb23ffbe2ca6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Monitor de um cluster do serviço de contentor do Azure com ELK
Neste artigo, vamos demonstrar como implementar a pilha ELK (Elasticsearch Logstash, Kibana) num cluster DC/SO no serviço de contentor do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
[Implementar](container-service-deployment.md) e [ligar](../container-service-connect.md) um cluster do DC/OS configurado pelo serviço de contentor do Azure. Explorar o dashboard de DC/OS e os serviços do Marathon [aqui](container-service-mesos-marathon-ui.md). Instalar também o [Balanceador de carga Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Pilha ELK é uma combinação de Elasticsearch, Logstash e Kibana que fornece uma pilha de ponto a ponto que pode ser utilizada para monitorizar e analisar os registos do cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurar a pilha ELK num cluster do DC/OS
Aceder à IU do DC/OS, através de [http://localhost:80 /](http://localhost:80/) uma vez na IU do DC/OS, navegue para **universo**. Procurar e instalar Elasticsearch, Logstash e Kibana o universo de DC/OS e por essa ordem específica. Pode saber mais sobre a configuração se pode aceder a **avançadas instalação** ligação.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Depois dos contentores ELK e são operacional, tem de ativar Kibana para poderem ser acedidos através do Marathon-LB. Navegue para **serviços** > **kibana**e clique em **editar** conforme mostrado abaixo.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Alternar a **modo JSON** e desloque-se para baixo para a secção de etiquetas.
Tem de adicionar um `"HAPROXY_GROUP": "external"` entrada aqui como mostrado abaixo.
Assim que clicar em **implementar alterações**, os reinícios do contentor.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se pretender verificar que Kibana está registado como um serviço no dashboard do HAPROXY, terá de abrir a porta 9090 no cluster de agente como HAPROXY é executado na porta 9090.
Por predefinição, iremos abrir as portas 80, 8080 e 443 no cluster de agente DC/OS.
São fornecidas instruções para abrir uma porta e fornecer público avaliar [aqui](container-service-enable-public-access.md).

Para aceder ao dashboard HAPROXY, abra a interface de administração do Marathon-LB em: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Depois de navegar para o URL, deverá ver o dashboard HAPROXY conforme mostrado abaixo e deverá ver uma entrada de serviço para Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Para aceder ao dashboard Kibana, que é implementado na porta 5601, terá de abrir a porta 5601. Siga as instruções [aqui](container-service-enable-public-access.md). Em seguida, abra o dashboard Kibana em: `http://localhost:5601`.

## <a name="next-steps"></a>Passos seguintes

* Para o registo de sistema e aplicação de reencaminhamento e configuração, consulte [gestão do registo no DC/SO com ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Para filtrar os registos, consulte [filtragem de registos com ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

