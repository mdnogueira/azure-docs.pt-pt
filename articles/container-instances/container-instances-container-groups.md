---
title: "Grupos de contentor de instâncias de contentor do Azure"
description: "Compreender como funcionam os grupos de contentor em instâncias de contentor do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 25eab41c3f0c986bcce33123f86f4c9638b77191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contentor em instâncias de contentor do Azure

O recurso mais superior em instâncias de contentor do Azure é um grupo de contentor. Este artigo descreve quais são os grupos de contentor e os tipos de cenários ativar.

## <a name="how-a-container-group-works"></a>Como funciona um grupo contentor

Um grupo contentor é uma coleção de contentores que obter agendada no mesmo computador anfitrião e partilhar um ciclo de vida, rede local e volumes de armazenamento. É semelhante ao conceito de uma *pod* no [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) e [DC/SO](https://dcos.io/docs/1.9/deploying-services/pods/).

O diagrama seguinte mostra um exemplo de um grupo contentor, que inclui vários contentores.

![Exemplo de grupos de contentor][container-groups-example]

Tenha em atenção que:

- O grupo está agendado uma máquina de anfitrião único.
- O grupo expõe um único endereço IP público, com uma porta exposto.
- O grupo é constituído por dois contentores. Um contentor escuta na porta 80, enquanto o outro escuta na porta 5000.
- O grupo inclui duas partilhas de ficheiros do Azure como volume monta e cada contentor monta uma das partilhas localmente.

### <a name="networking"></a>Redes

Grupos de contentor partilham um endereço IP e um espaço de nomes de porta nesse endereço IP. Para permitir aos clientes externos atingir um contentor dentro do grupo, tem de expor a porta no endereço IP de e para o contentor. Porque contentores dentro do grupo de partilham de um espaço de nomes de porta, o mapeamento de porta não é suportado. Contentores dentro de um grupo podem aceder entre si através de localhost nas portas que possam tem expostos, mesmo que essas portas não são expostas externamente no endereço IP do grupo.

### <a name="storage"></a>Armazenamento

Pode especificar volumes externos para montar dentro de um grupo contentor. Pode mapear esses volumes em caminhos específicos dentro os contentores individuais num grupo.

## <a name="common-scenarios"></a>Cenários comuns

Os grupos de contentor multi são útil nos casos em que pretende dividir segurança uma única tarefa funcional num pequeno número de imagens de contentor, que podem ser fornecidas por equipas diferentes e tem os requisitos de recursos separado. Exemplo de utilização pode incluir:

- Um contentor de aplicação e um contentor de registo. O contentor de registo recolhe a saída de registos e as métricas pela aplicação principal e escreve-as para armazenamento de longa duração.
- Uma aplicação e um contentor de monitorização. O contentor de monitorização periodicamente faz um pedido para a aplicação para se certificar de que está em execução e corretamente a responder e gera um alerta se não estiver.
- Um contentor que serve uma aplicação web e um contentor, a extrair o conteúdo mais recente do controlo de origem.

## <a name="next-steps"></a>Passos seguintes

Saiba como [implementar um grupo contentor Multi](container-instances-multi-container-group.md) com um modelo Azure Resource Manager.

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png