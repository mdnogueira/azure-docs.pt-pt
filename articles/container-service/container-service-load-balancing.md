<properties
   pageTitle="Balanceamento de carga de um cluster do Serviço de Contentor do Azure | Microsoft Azure"
   description="Balanceamento de carga de um cluster do Serviço de Contentor do Azure."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Balanceamento de carga de um cluster do Serviço de Contentor do Azure

Neste artigo, vamos configurar um front-end da Web que pode ser aumentado verticalmente para fornecer serviços atrás do Azure LB.


## Pré-requisitos

[Implemente uma instância do Serviço de Contentor do Azure](container-service-deployment.md) com o tipo de orquestrador DCOS [e certifique-se de que o cliente pode ligar ao cluster](container-service-connect.md) e ao [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].


## Balanceamento de carga

Existem duas camadas de balanceamento de carga num cluster do Serviço do Contentor: o Azure LB para os pontos de entrada públicos (os que os utilizadores finais irão aceder) e o marathon-lb subjacente, que redireciona os pedidos de entrada para os pedidos de serviço de instâncias de contentor. À medida que dimensionamos os contentores que fornecem o serviço, o marathon-lb irá adaptar-se dinamicamente.

## Marathon LB 

A solução Marathon LB reconfigura-se dinamicamente, com base nos contentores que implementou. Também é resiliente para a perda de um contentor ou de um agente; se isto ocorrer, o Mesos simplesmente reinicia o contentor noutro local e reconfigura o Marathon LB. 

Para instalar o Marathon LB, execute o comando seguinte no computador cliente:

```bash
dcos package install marathon-lb 
``` 

Agora que temos o pacote marathon-lb, pode implementar um servidor Web simples utilizando a configuração seguinte:


```json
{ 
  "id": "web", 
  "container": { 
    "type": "DOCKER", 
    "docker": { 
      "image": "tutum/hello-world", 
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

Estas são as partes de chave: 
  * defina o valor de HAProxy_0_VHOST para o FQDN do load balancer dos agentes. Este é o formato: `<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se criar um cluster do Serviço de Contentor denominado `myacs` na região `West US`, o FQDN seria: `myacsagents.westus.cloudapp.azure.com`. Também pode encontrar isto procurando o load balancer com “agent” no nome quando consultar os recursos no grupo de recursos que criou para o serviço de contentor no [Portal do Azure](https://portal.azure.com).
  * Defina servicePort como uma porta >= 10 000. Se o fizer, identifica o serviço em execução neste contentor; o marathon-lb utiliza-o para identificar os serviços que deve balancear.
  * Defina a etiqueta HAPROXY_GROUP para “externa”.
  * Defina hostPort para 0. Ao fazer isto, o marathon aloca de forma arbitrária uma porta disponível.

Copie este JSON para um ficheiro denominado `hello-web.json` e utilize-o para implementar um contentor: 

```bash
dcos marathon app add hello-web.json 
``` 

## Azure LB 

Por predefinição, o Azure LB expõe as portas 80, 8080 e 443. Se estiver a utilizar uma destas três portas (como no exemplo acima), não precisará de fazer nada: deve ser capaz de aceder ao FQDN do LB do agente e, sempre que atualizar, acederá a um dos três servidores Web de uma forma round robin. Se, no entanto, utilizar uma porta diferente, terá de adicionar uma regra round robin e sonda ao Azure LB para a porta que utilizou. Pode fazê-lo na [CLI XPLAT do Azure](../xplat-cli-azure-resource-manager.md) com os comandos `azure lb rule create` e `azure lb probe create`.


## Cenários adicionais

Pode ter um cenário em que utiliza domínios diferentes para expor os diferentes serviços. Por exemplo: 

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321 

Para isso, consulte os [Anfitriões Virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que proporcionam uma forma de associar domínios a caminhos do marathon-lb específico.

Em alternativa, pode expor portas diferentes e remapeá-las para o serviço correto atrás do marathon-lb. Por exemplo:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432 
 

## Passos seguintes

Consulte [esta mensagem de blogue](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) para obter mais informações sobre o Marathon LB.



<!--HONumber=Jun16_HO2-->


