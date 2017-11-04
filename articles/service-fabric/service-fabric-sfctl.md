---
title: Azure Service Fabric CLI - sfctl | Microsoft Docs
description: "Descreve os comandos de sfctl CLI de recursos de infraestrutura de serviço."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2dd30470ee0f6c038a8601bfca73fc97091de2fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl"></a>Sfctl 
Comandos para a gestão de clusters de Service Fabric e entidades de mensagens em fila. Esta versão é compatível com o tempo de execução do Service Fabric 6.0. Comandos seguem o padrão de verbo substantivo, consulte os subgrupos seguintes para obter mais informações.

## <a name="subgroups"></a>Subgrupos
|Subgrupos|Descrição|
| --- | --- |
| [aplicação](service-fabric-sfctl-application.md)| Criar, eliminar e gerir aplicações e tipos de aplicações.|
| [Chaos](service-fabric-sfctl-chaos.md)   | Iniciar, parar e relatórios sobre o serviço de teste chaos.|
| [cluster](service-fabric-sfctl-cluster.md) | Selecione, gerir e operar clusters de Service Fabric.|
| [Componha](service-fabric-sfctl-compose.md) | Criar, eliminar e gerir aplicações Docker Compose.|
| [é](service-fabric-sfctl-is.md)      | Consultar e enviar comandos para o serviço de infraestrutura.|
| [nó](service-fabric-sfctl-node.md)    | Gerir os nós que formam um cluster.|
| [partição](service-fabric-sfctl-partition.md)  | Consultar e gerir partições para qualquer serviço.|
| [rpm](service-fabric-sfctl-rpm.md)        | Consultar e enviar comandos para o serviço do Gestor de reparação.|
| [réplica](service-fabric-sfctl-replica.md) | Gerir as réplicas que pertencem a partições de serviço.|
| [serviço](service-fabric-sfctl-service.md) | Criar, eliminar e gerir o serviço, tipos de serviço e pacotes de serviços.|
| [arquivo](service-fabric-sfctl-store.md)   | Efetue operações de nível de ficheiro básico no arquivo de imagens de cluster.|

## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) a CLI de recursos de infraestrutura de serviço.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).