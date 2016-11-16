---
title: Contentores de balanceamento de carga num cluster do Azure Container Service | Microsoft Docs
description: "Balanceamento de carga entre múltiplos contentores num cluster do Azure Container Service."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Contentores, Microserviços, DC/OS, Azure"
ms.assetid: f0ab5645-2636-42de-b23b-4c3a7e3aa8bb
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cf255856302ad5bdb1f6022d231833610acbcac5


---
# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Contentores de balanceamento de carga num cluster do Azure Container Service
Neste artigo, vamos explorar como criar um balanceador de carga interno num DC/OS gerido pelo Azure Container Service com o Marathon-LB. Este procedimento vai permitir-lhe dimensionar as suas aplicações horizontalmente. Permitir-lhe-á também tirar partido dos clusters de agentes públicos e privados, ao colocar os balanceadores de carga no cluster público e os contentores aplicacionais no cluster privado.

## <a name="prerequisites"></a>Pré-requisitos
[Implemente uma instância do Serviço de Contentor do Azure](container-service-deployment.md) com o tipo de orquestrador DC/SO [e certifique-se de que o cliente pode ligar ao cluster](container-service-connect.md). 

## <a name="load-balancing"></a>Balanceamento de carga
Existem duas camadas de balanceamento de carga no cluster de Serviço de Contentor que iremos criar: 

1. O Balanceador de Carga do Azure fornece pontos de entrada pública (aqueles a que os utilizadores finais irão aceder). Isto é fornecido automaticamente pelo Serviço do Contentor do Azure e está, por predefinição, configurado para expor as portas 80, 443 e 8080.
2. O Balanceador de Carga Marathon (marathon lb) encaminha pedidos de entrada para instâncias de contentor que fornecem assistência a esses pedidos. À medida que dimensionamos os contentores que fornecem o nosso serviço Web, o marathon-lb adapta-se dinamicamente. Este balanceador de carga não é fornecido por predefinição no seu Serviço de Contentor, mas é muito fácil instalar.

## <a name="marathon-load-balancer"></a>Balanceador de Carga Marathon
O Balanceador de Carga Marathon reconfigura-se dinamicamente, com base nos contentores que tenha implementado. É também resiliente à perda de um contentor ou de um agente. Se isto ocorrer, o Apache Mesos irá reiniciar simplesmente o contentor noutro local e o marathon-lb irá adaptar-se.

Para instalar o Balanceador de carga Marathon pode utilizar a IU da Web de DC/SO ou a linha de comandos.

### <a name="install-marathonlb-using-dcos-web-ui"></a>Instalar Marathon-LB utilizando a IU da Web de DC/SO
1. Clique em «Universo»
2. Procure «Marathon-LB»
3. Clique em «Instalar»

![Instalar o marathon-lb através da Interface de Web de DC/SO](./media/dcos/marathon-lb-install.png)

### <a name="install-marathonlb-using-the-dcos-cli"></a>Instalar o Marathon-LB utilizando a CLI de DC/SO
Depois de instalar a CLI de DC/SO e garantir que consegue ligar ao cluster, execute o seguinte comando a partir do seu computador cliente:

```bash
dcos package install marathon-lb
```

Este comando instala automaticamente o balanceador de carga no cluster de agentes públicos.

## <a name="deploy-a-load-balanced-web-application"></a>Implementar uma Aplicação Web com Balanceamento de Carga
Agora que temos o pacote marathon-lb, podemos implementar o contentor aplicacional que queremos para balancear a carga. Neste exemplo, vamos implementar um servidor Web simples através da seguinte configuração:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

* Defina o valor de `HAProxy_0_VHOST` para o FQDN do balanceador de carga dos agentes. Este é o formato: `<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se criar um cluster do Serviço de Contentor denominado `myacs` na região `West US`, o FQDN seria `myacsagents.westus.cloudapp.azure.com`. Também pode encontrar isto, procurando o balanceador de carga com "agente" no nome quando estiver à procura de recursos no grupo de recursos que criou para o Serviço de Contentor no [portal do Azure](https://portal.azure.com).
* Defina servicePort como uma porta >= 10 000. Isto identifica o serviço em execução neste contentor. O marathon-lb utiliza isto para identificar os serviços que deve balancear.
* Defina a etiqueta `HAPROXY_GROUP` como "externa".
* Defina `hostPort` como 0. Isto significa que o Marathon irá atribuir de forma arbitrária uma porta disponível.
* Defina `instances` para o número de instâncias que pretende criar. Pode sempre aumentá-los na vertical e na horizontal mais tarde.

Vale a pena saber que, por predefinição, o Marathon implementará no cluster privado, o que significa que a implementação acima apenas será acessível através do seu balanceador de carga, que é normalmente o comportamento pretendido.

### <a name="deploy-using-the-dcos-web-ui"></a>Implementar utilizando a IU da Web de DC/SO
1. Visite a página de Marathon em http://localhost/marathon (depois de configurar o [túnel SSH](container-service-connect.md) e clique em `Create Appliction`
2. Na caixa de diálogo `New Application`, clique em `JSON Mode` no canto superior direito
3. Cole o JSON acima no editor
4. Clique `Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>Implementar utilizando o CLI de DC/SO
Para implementar esta aplicação com a CLI de DC/SO, basta copiar o JSON acima para um ficheiro denominado `hello-web.json` e executar:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Azure Load Balancer
Por predefinição, o Balanceador de Carga do Azure expõe as portas 80, 8080 e 443. Se utilizar uma das três seguintes portas (como podemos ver no exemplo acima) não tem de efetuar qualquer ação. Deve conseguir aceder à FQDN do balanceador de carga do agente - e sempre que atualizar, irá aceder a um dos seus três servidores Web de uma maneira round-robin. Se, no entanto, utilizar uma porta diferente, terá de adicionar uma regra round-robin e uma sonda no balanceador de carga para a porta que utilizou. Pode fazê-lo a partir da [CLI do Azure](../xplat-cli-azure-resource-manager.md), com os comandos `azure network lb rule create` e `azure network lb probe create`. Também é possível fazê-lo através do Portal do Azure.

## <a name="additional-scenarios"></a>Cenários adicionais
Pode ter um cenário em que utiliza domínios diferentes para expor os diferentes serviços. Por exemplo:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

Para tal, consulte os [anfitriões virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que proporcionam uma forma de associar domínios a caminhos do marathon-lb específicos.

Em alternativa, pode expor portas diferentes e remapeá-las para o serviço correto atrás do marathon-lb. Por exemplo:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432

## <a name="next-steps"></a>Passos seguintes
Veja a documentação de DC/SO para obter mais informações sobre o [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).




<!--HONumber=Nov16_HO2-->


