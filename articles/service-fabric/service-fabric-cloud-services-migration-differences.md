---
title: "Diferenças entre os serviços em nuvem e de recursos de infraestrutura de serviço | Microsoft Docs"
description: "Uma descrição geral conceptual para migrar as aplicações nos serviços de nuvem do Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 26c0256f6fa299551d92e9bcd058ca359d8c85b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Saiba mais sobre as diferenças entre os serviços em nuvem e de Service Fabric antes de migrar as aplicações.
Microsoft Azure Service Fabric é a plataforma de aplicação de nuvem de próxima geração para aplicações distribuídas altamente dimensionáveis e de elevada disponibilidade fiáveis. Introduz muitas funcionalidades novas para empacotamento, implementar, atualizar e gestão de aplicações distribuídas em nuvem. 

Este é um guia introdutórias para migrar as aplicações nos serviços de nuvem do Service Fabric. Concentra-se principalmente da arquitetura e as diferenças entre os serviços em nuvem e de recursos de infraestrutura do serviço de design.

## <a name="applications-and-infrastructure"></a>Aplicações e infraestruturas
Uma diferença fundamental entre serviços em nuvem e de Service Fabric é a relação entre as VMs, cargas de trabalho e aplicações. Aqui uma carga de trabalho é definida como o código de escrita para executar uma tarefa específica ou fornecer um serviço.

* **Serviços cloud é sobre a implementação de aplicações que as VMs.** O código de escrita é fortemente conjugado para uma instância VM, como uma função de trabalho ou Web. Para implementar uma carga de trabalho nos serviços em nuvem está a implementar um ou mais instâncias VM que executam a carga de trabalho. Não há nenhum separação das aplicações e as VMs e por isso, não há nenhuma definição formal de uma aplicação. Uma aplicação pode considerar como um conjunto de instâncias de Web ou função de trabalho dentro de uma implementação de serviços em nuvem ou como uma implementação de serviços em nuvem completa. Neste exemplo, uma aplicação é apresentada como um conjunto de instâncias de função.

![Aplicações de serviços em nuvem e a topologia][1]

* **Service Fabric é sobre como implementar aplicações para VMs existentes ou máquinas com o Service Fabric no Windows ou Linux.** Os serviços de escrita estão completamente desassociados da infraestrutura subjacente, que é abstracted ausente pela plataforma de aplicação do Service Fabric, pelo que uma aplicação pode ser implementada em vários ambientes. Uma carga de trabalho no Service Fabric é denominada um "serviço" e um ou mais serviços são agrupados de uma aplicação formally definido, que é executada na plataforma de aplicação de Service Fabric. Várias aplicações podem ser implementadas para um único cluster de Service Fabric.

![Topologia e de aplicações do Service Fabric][2]

Service Fabric em si é uma camada de plataforma de aplicação que é executado no Windows ou Linux, enquanto que os serviços em nuvem é um sistema de implementação de VMs geridas do Azure com cargas de trabalho anexadas.
O modelo de aplicação de Service Fabric tem várias vantagens:

* Tempos de implementação rápida. Criar instâncias de VM pode ser demorado. No Service Fabric, as VMs só são implementadas depois para formar um cluster que aloja a plataforma de aplicação de Service Fabric. Partir desse ponto, pacotes de aplicações podem ser implementados para o cluster muito rapidamente.
* Alojamento high-density. Nos serviços em nuvem, uma VM de função de trabalho aloja uma carga de trabalho. No Service Fabric, as aplicações são separadas das VMs que executá-los, que significa que pode implementar um grande número de aplicações para um pequeno número de VMs, que pode reduzir o custo global para implementações maiores.
* O Service Fabric plataforma pode ser executado em qualquer local que tem máquinas Windows Server ou Linux, quer seja Azure ou no local. A plataforma fornece uma camada de abstração através da infraestrutura subjacente para a aplicação pode ser executado em ambientes diferentes. 
* Gestão de aplicações distribuídas. Service Fabric é uma plataforma que não só anfitriões de aplicações distribuídas, mas também ajuda a gerir o ciclo de vida independentemente a VM de alojamento ou máquina ciclo de vida.

## <a name="application-architecture"></a>Arquitetura da aplicação
A arquitetura de uma aplicação de serviços em nuvem normalmente inclui várias dependências de serviço externo, como o Service Bus, tabelas do Azure e armazenamento de BLOBs, SQL, Redis e outras pessoas para gerir o estado e os dados de uma aplicação e a comunicação entre Web e funções de trabalho numa implementação de serviços em nuvem. Um exemplo de uma aplicação de serviços em nuvem concluída poderá ter o seguinte aspeto:  

![Arquitetura de serviços cloud][9]

Aplicações de Service Fabric também podem optar por utilizar os mesmos serviços externos numa aplicação completa. Com este exemplo arquitetura dos serviços de nuvem, é o caminho de migração mais simples de serviços em nuvem para o Service Fabric substituir apenas a implementação de serviços em nuvem com uma aplicação de Service Fabric, mantendo a arquitetura geral da mesma. O Web e funções de trabalho podem ser convertidos para serem serviços sem monitorização de estado do Service Fabric com alterações de código mínimas.

![Arquitetura do Service Fabric após a migração simple][10]

Nesta fase, o sistema deve continuar a funcionar o mesmo que antes. Pode ser internalized tendo partido das funcionalidades de monitorização de estado do Service Fabric, arquivos de estado externo, como serviços de monitorização de Estado onde for aplicável. Este é o mais envolvida que uma migração simple da Web e funções de trabalho para serviços sem monitorização de estado do Service Fabric, porque requer escrever serviços personalizados que fornecem funcionalidade equivalente à sua aplicação, como os serviços externos anteriormente. Os benefícios de fazê-lo incluem: 

* Remover as dependências externas 
* Unificá a implementação, gestão e modelos de atualização. 

Uma arquitetura de resultante de exemplo de internalizing estes serviços pode ter o seguinte aspeto:

![Arquitetura do Service Fabric após migração completa][11]

## <a name="communication-and-workflow"></a>Fluxo de trabalho e comunicação
A maioria das aplicações de serviço em nuvem é constituída por mais do que uma camada. Da mesma forma, uma aplicação de Service Fabric é composta por mais do que um serviço (normalmente, muitos serviços). Dois modelos de comunicações comuns são comunicação direta e através de um armazenamento externo durável.

### <a name="direct-communication"></a>Comunicação direta
Com comunicação direta, camadas podem comunicar diretamente através do ponto final exposta por cada camada. Em ambientes sem monitorização de estado como serviços em nuvem, esta significa selecionar uma instância de uma função VM, quer aleatoriamente ou round-robin carregar saldo e ligar diretamente ao respetivo ponto final.

![Serviços cloud direcionam comunicação][5]

 Comunicação direta é um modelo comum de comunicação no Service Fabric. A principal diferença entre o Service Fabric e serviços em nuvem é que serviços em nuvem em se ligar a uma VM, enquanto no Service Fabric ligar a um serviço. Esta é uma distinção importante por alguns motivos:

* Serviços no Service Fabric não estão vinculados às VMs que alojar; serviços podem mover-se no cluster e, na verdade, espera-se mover-se por vários motivos: recursos balanceamento, ativação pós-falha, as atualizações de aplicações e a infraestrutura e restrições de posicionamento ou de carregamento. Isto significa que pode alterar o endereço de uma instância de serviço em qualquer altura. 
* Uma VM com o Service Fabric pode alojar vários serviços, cada um com pontos finais exclusivos.

O Service Fabric fornece um mecanismo de deteção do serviço, chamado o serviço de nomenclatura, que pode ser utilizado para resolver endereços de ponto final dos serviços. 

![Comunicação direta do Service Fabric][6]

### <a name="queues"></a>Filas
Um mecanismo de comunicação comuns entre camadas em ambientes sem monitorização de estado como serviços em nuvem está a utilizar uma fila de armazenamento externo para armazenar forma durável tarefas de trabalho de um escalão para outro. Um cenário comum é uma camada web que envia tarefas para uma fila do Azure ou o Service Bus em que as instâncias de função de trabalho podem anular e processar as tarefas.

![Comunicação de fila de serviços de nuvem][7]

O mesmo modelo de comunicação pode ser utilizado no Service Fabric. Isto pode ser útil quando migrar uma aplicação de serviços em nuvem existente no Service Fabric. 

![Comunicação direta do Service Fabric][8]

## <a name="next-steps"></a>Passos Seguintes
O caminho de migração mais simples de serviços em nuvem para o Service Fabric é substitua apenas a implementação de serviços em nuvem uma aplicação de Service Fabric, mantendo a arquitetura geral da sua aplicação aproximadamente os mesmos. O artigo seguinte fornece um guia para ajudar a converter um Web ou função de trabalho a um serviço sem monitorização de estado de Service Fabric.

* [Migração Simple: um Web ou função de trabalho converter de um serviço sem monitorização de estado de Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
