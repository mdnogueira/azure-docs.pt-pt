---
title: "Resolução de problemas de instâncias de contentor do Azure"
description: "Saiba como resolver problemas com instâncias de contentor do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ff6da0ce95d0405714602c3872da34a2bff344d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Resolver problemas de implementação com instâncias de contentor do Azure

Este artigo mostra como resolver problemas quando implementar contentores para instâncias de contentor do Azure. Também descreve alguns dos problemas comuns que poderá ter.

## <a name="getting-diagnostic-events"></a>Obter eventos de diagnóstico

Para ver registos a partir do código da aplicação num contentor, pode utilizar o [az contentor registos](/cli/azure/container#logs) comando. Mas se o contentor não implementar com êxito, terá de rever as informações de diagnóstico fornecidas pelo fornecedor de recursos de instâncias de contentor do Azure. Para ver os eventos para o contentor, execute o seguinte comando:

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

O resultado inclui as propriedades de núcleo do seu contentor, juntamente com eventos de implementação:

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>Problemas comuns de implementação

Existem alguns problemas comuns essa conta para a maioria dos erros na implementação.

### <a name="unable-to-pull-image"></a>Não é possível a imagem de solicitação

Se as instâncias de contentor do Azure não é possível solicitar a sua imagem inicialmente, repete as tentativas durante um determinado período antes de falhar, eventualmente. Se a imagem não pode ser solicitada, são apresentados eventos, como o seguinte:

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

Para resolver, eliminar o contentor e repita a implementação, pagar especial atenção se introduziu o nome da imagem corretamente.

### <a name="container-continually-exits-and-restarts"></a>Contentor continuamente sai e reinicia

Atualmente, as instâncias de contentor do Azure suporta apenas a serviços de longa execução. Se for executada no contentor de conclusão e sai, é automaticamente reiniciado e será novamente executado. Se isto acontecer, são apresentados eventos, como os seguintes. Tenha em atenção que o contentor começa com êxito, em seguida, reinicia rapidamente. A API de instâncias de contentor inclui um `retryCount` propriedade mostra quantas vezes contentor específico tiver sido reiniciado.

```bash
"events": [
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:55+00:00",
    "lastTimestamp": "2017-08-03T22:23:22+00:00",
    "message": "Pulling: pulling image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:23:23+00:00",
    "message": "Pulled: Successfully pulled image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Created: Created container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Started: Started container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Created: Created container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Started: Started container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 13,
    "firstTimestamp": "2017-08-03T22:21:59+00:00",
    "lastTimestamp": "2017-08-03T22:24:36+00:00",
    "message": "BackOff: Back-off restarting failed container",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:22:13+00:00",
    "lastTimestamp": "2017-08-03T22:22:13+00:00",
    "message": "Created: Created container with id 72e347e891290e238135e4a6b3078748ca25a1275dbbff30d8d214f026d89220",
    "type": "Normal"
  },
  ...
```

> [!NOTE]
> A maioria das imagens de contentor para as distribuições do Linux definir uma shell, tais como bash, como o comando de predefinição. Uma vez que uma shell no seu próprio não é um serviço de execução longa, estes contentores sair imediatamente e enquadram-se um ciclo de reinício.

### <a name="container-takes-a-long-time-to-start"></a>Contentor demora muito tempo a iniciar

Se o seu contentor demora muito tempo a iniciar, eventualmente, mas for bem sucedida, comece por observar o tamanho da imagem do contentor. Porque as instâncias de contentor do Azure obtém a imagem do contentor a pedido, o tempo de arranque ocorrer está diretamente relacionada com o tamanho.

Pode ver o tamanho da imagem do contentor utilizando a CLI do Docker:

```bash
docker images
```

Saída:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

A chave para manter o tamanho de imagem pequeno é garantir que a imagem final não contém tudo o que não é necessária no tempo de execução. Uma forma para fazer isto é com [fase multi compilações](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Fase multi baseia-se disponibilizar fácil de garantir que a imagem final contém apenas os artefactos que é necessário para a sua aplicação, e não quaisquer o extra de conteúdos que era necessário no momento da compilação.

É a forma como para reduzir o impacto da solicitação de imagem no tempo de arranque do contentor para alojar a imagem de contentor com o registo de contentor do Azure na mesma região em que pretende utilizar instâncias de contentor do Azure. Isto reduz o caminho de rede que a imagem do contentor tem de viajam, encurtar significativamente o tempo de transferência.

### <a name="resource-not-available-error"></a>Recurso erro não está disponível

Devido a vários recursos regional carregar no Azure, poderá receber o erro seguinte ao tentar implementar uma instância do contentor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Este erro indica que, devido a sobrecarga na região na qual está a tentar implementar, não não possível alocar os recursos especificados para o contentor nessa altura. Utilizar um ou mais o os seguintes passos de mitigação para ajudar a resolver o problema.

* Certifique-se de que as definições de implementação do contentor coincidir com parâmetros definidos na [disponibilidade de região para instâncias de contentor do Azure](container-instances-region-availability.md)
* Especificar definições de CPU e memória inferiores para o contentor
* Implementar noutra região do Azure
* Implementar numa altura posterior
