---
title: "Gestor de estado de fiável de recursos de infraestrutura de serviço do Azure e fiável de coleção características | Microsoft Docs"
description: "Descrição profunda sobre conceitos de coleção fiável e estrutura no Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Características de Gestor de estado de fiável de recursos de infraestrutura de serviço do Azure e fiável de coleção
Este documento delves dentro de coleções fiável e fiável Gestor de estado para ver como funcionam os componentes principais em segundo plano.

> [!NOTE]
> Este documento é trabalho em curso. Adicione comentários para este artigo para nos dizer que tópico que gostaria de saber mais sobre.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Modelo de persistência local: registo e ponto de verificação
O Gestor de estado fiável e coleções fiável siga um modelo de persistência que é chamado registo e ponto de verificação.
Neste modelo, cada alteração de estado é registada no disco pela primeira vez e, em seguida, aplicada na memória.
O estado de conclusão em si é mantido apenas ocasionalmente (a.k.a. Ponto de verificação).
A vantagem é que deltas transformados sequenciais acrescentar apenas operações de escrita no disco para um melhor desempenho.

Para melhor compreender o modelo de registo e ponto de verificação, vamos primeiro observe o cenário de disco infinito.
O Gestor de estado fiável os registos de cada operação está a replicar.
Registo permite que as coleções fiável aplicar a operação apenas na memória.
Uma vez que os registos são mantidos, mesmo quando a réplica falha e tem de ser reiniciado, o Gestor de estado fiável tem informações suficientes no respetivo registo de reprodução de todas as operações que perdeu-se a réplica.
Dado que o disco é infinito, registos nunca precisam de ser removido e a coleção fiável tem de gerir o estado de dentro da memória.

Agora vamos ver o cenário de disco finito.
Como registos acumularem, o Gestor de estado fiável será executado sem espaço em disco.
Antes do que acontece, o Gestor de estado fiável tem truncar o registo para disponibilizar espaço para os registos mais recentes.
Gestor de estado de fiável pedidos as coleções fiável para o ponto de verificação o respetivo estado de memória para o disco.
Neste momento, as coleções fiável seria manter o estado de memória.
Assim que as coleções fiável concluir os respetivos pontos de verificação, o Gestor de estado fiável pode truncar o registo para libertar espaço em disco.
Quando a réplica tem de ser reiniciado, coleções fiável recuperar o respetivo estado checkpointed e o Gestor de estado fiável recupera e reproduz todas as alterações de estado que ocorreram desde o último ponto de verificação.

Adicionar outro valor de ponto de verificação é o que melhora os tempos de recuperação em cenários comuns. Registo contém todas as operações que ocorreram desde o último ponto de verificação.
Por isso,-pode incluir várias versões de um item como vários valores para uma linha fornecido no dicionário fiável.
Por outro lado, um pontos de verificação de coleção fiável apenas a versão mais recente de cada valor de uma chave.

## <a name="next-steps"></a>Passos seguintes
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

