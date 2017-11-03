---
title: Carregar saldo contentores no cluster DC/OS do Azure | Microsoft Docs
description: "O balanceamento de carga entre vários contentores num cluster DC/OS do serviço de contentor do Azure."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Contentores, Microserviços, DC/OS, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 78725c9d23e13d307821a188028ef573d1def038
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Contentores de balanceamento de carga de um cluster do serviço de contentor do Azure DC/OS
Neste artigo, vamos explorar como criar um balanceador de carga interno num serviço de contentor do Azure DC/SO gerido utilizando o Marathon-LB. Esta configuração permite-lhe aumentar horizontalmente as suas aplicações. Também permite-lhe tirar partido do agente público e privado clusters colocando os balanceadores de carga no cluster público e os contentores de aplicação no cluster privado. Neste tutorial:

> [!div class="checklist"]
> * Configurar um balanceador de carga Marathon
> * Implementar uma aplicação utilizando o Balanceador de carga
> * Configurar e Balanceador de carga do Azure

Precisa de um cluster de ACS DC/OS para concluir os passos neste tutorial. Se for necessário, [este script de exemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar uma por si.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Descrição geral do balanceamento de carga

Existem duas camadas de balanceamento de carga num cluster DC/OS do serviço de contentor do Azure: 

**Balanceador de carga do Azure** fornece pontos de entrada pública (aqueles a que os utilizadores finais acederem). Um Azure LB é fornecido automaticamente pelo serviço de contentor do Azure e está, por predefinição, configurada para expor as portas 80, 443 e 8080.

**O Balanceador de carga Marathon (marathon-lb)** pedidos para instâncias de contentor que estes pedidos de serviço de entrada de rotas. À medida que dimensionamos os contentores que fornecem o nosso serviço web, o marathon-lb feita dinamicamente. Este Balanceador de carga não é fornecido por predefinição no seu serviço de contentor, mas é fácil instalar.

## <a name="configure-marathon-load-balancer"></a>Configurar o Balanceador de carga Marathon

O Balanceador de Carga Marathon reconfigura-se dinamicamente, com base nos contentores que tenha implementado. É também resiliente à perda de um contentor ou um agente - se isto ocorrer, o Apache Mesos reinicia o contentor noutro local e feita do marathon-lb.

Execute o seguinte comando para instalar o Balanceador de carga marathon no cluster do agente público.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Implementar aplicações com balanceamento de carga

Agora que temos o pacote marathon-lb, podemos implementar o contentor aplicacional que queremos para balancear a carga. 

Em primeiro lugar, obter o FQDN dos agentes publicamente expostos.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Em seguida, crie um ficheiro denominado *Olá web.json* e copie o seguinte conteúdo. O `HAPROXY_0_VHOST` etiqueta tem de ser atualizada com o FQDN dos agentes DC/OS. 

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

Utilize a CLI de DC/OS para executar a aplicação. Por predefinição Marathon implementa o applicaton para o cluster privado. Isto significa que a implementação acima só é acessível através do seu Balanceador de carga, que, normalmente, é o comportamento pretendido.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Assim que a aplicação foi implementada, navegue para o FQDN do cluster de agente para ver as aplicações com balanceamento de carga.

![Imagem da aplicação com balanceamento de carga](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Configurar o Balanceador de carga do Azure

Por predefinição, o Balanceador de Carga do Azure expõe as portas 80, 8080 e 443. Se utilizar uma das três seguintes portas (como podemos ver no exemplo acima) não tem de efetuar qualquer ação. Deverá conseguir atingiu o FQDN do seu Balanceador de carga de agente e, sempre que atualizar, irá aceder a um dos seus três servidores web de uma maneira round-robin. 

Se utilizar uma porta diferente, terá de adicionar uma regra round-robin e sonda no balanceador de carga para a porta que utilizou. Pode fazê-lo a partir da [CLI do Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), com os comandos `azure network lb rule create` e `azure network lb probe create`.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre balanceamento de carga em ACS com balanceadores de carga Marathon tanto do Azure, incluindo as seguintes ações:

> [!div class="checklist"]
> * Configurar um balanceador de carga Marathon
> * Implementar uma aplicação utilizando o Balanceador de carga
> * Configurar e Balanceador de carga do Azure

Avançar para o próximo tutorial para saber mais sobre a integração de armazenamento do Azure com o DC/OS, no Azure.

> [!div class="nextstepaction"]
> [Azure de montar a partilha de ficheiros no cluster DC/OS](container-service-dcos-fileshare.md)