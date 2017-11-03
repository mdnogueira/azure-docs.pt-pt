---
title: "Introdução ao escalão Premium de Cache de Redis do Azure | Microsoft Docs"
description: "Saiba como criar e gerir a persistência de Redis, Redis clustering e suporte VNET para as instâncias de Cache de Redis do Azure do escalão Premium"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: c7a70e74f8b275ed9e10118b0ae9e81309f97ba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introdução ao escalão Premium da Cache de Redis do Azure
Cache de Redis do Azure é uma cache distribuída, gerida que o ajuda a criar aplicações de elevada disponibilidade escaláveis e responsivas fornecendo acesso Super-obrigatórios rápido aos seus dados. 

O escalão Premium nova é uma camada pronta da empresa, que inclui todas as funcionalidades do escalão Standard e mais informações, tais como um melhor desempenho, as cargas de trabalho maiores, recuperação após desastre, para importar/exportar e a segurança avançada. Continue a ler para saber mais sobre as funcionalidades adicionais do escalão Premium cache.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Um melhor desempenho em comparação comparado o escalão Standard ou básico
**Melhor desempenho através do padrão ou básico camada.** Caches no escalão Premium são implementadas no hardware que tem processadores mais rápidos e fornece um melhor desempenho em comparação comparado o escalão Standard ou básico. Caches de escalão Premium têm um maior débito e latências inferiores. 

**Débito para a Cache do mesmo tamanho for superior nas Premium em comparação com o escalão Standard.** Por exemplo, o débito de um 53 GB P4 cache (Premium) é 250 mil pedidos por segundo em comparação com a 150 K para C6 (Standard).

Para obter mais informações sobre o tamanho, débito e largura de banda com premium caches, consulte [FAQ de Cache de Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>A persistência de dados de redis
O escalão Premium permite-lhe manter os dados da cache numa conta do Storage do Azure. Cache Basic/Standard todos os dados são armazenados apenas na memória. Em caso de infraestrutura subjacente existir problemas podem ser potencial perda de dados. Recomendamos que utilize a funcionalidade de persistência de dados de Redis no escalão Premium para aumentar a resistência contra a perda de dados. Cache de Redis do Azure oferece RDB e AOF (brevemente) opções no [persistência de Redis](http://redis.io/topics/persistence). 

Para obter instruções sobre como configurar a persistência, veja [Como configurar a persistência para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster de redis
Se pretender criar caches superiores a 53 GB ou pretende dividir os dados em vários nós de Redis, pode utilizar o Redis clustering que está disponível no escalão Premium. Cada nó é composta por um par de cache primário/réplica gerido pelo Azure para elevada disponibilidade. 

**Clustering de redis-dimensionamento máximo e débito.** Débito de forma linear aumenta como aumentar o número de partições horizontais (nós) no cluster. Ex. Se criar um cluster de P4 de 10 shards, em seguida, o débito disponível é 250 mil * 10 = 2,5 milhões de pedidos por segundo. Consulte o [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, débito e largura de banda com premium caches.

Para começar a utilizar com o clustering, consulte o artigo [como configurar o clustering para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Segurança e isolamento otimizados
Caches criados no escalão básicas ou Standard sejam acessíveis através da internet pública. O acesso à Cache é restringido com base na chave de acesso. Com o escalão Premium, pode ainda mais de garantir que apenas os clientes dentro de uma rede especificado podem aceder à Cache. Pode implementar a Cache de Redis num [rede Virtual do Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Pode utilizar todas as funcionalidades de VNet, como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringir ainda mais o acesso ao Redis.

Para obter mais informações, veja [Como configurar o suporte da Rede Virtual de uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importação/Exportação
Importar/exportar é uma operação de gestão de dados de Cache de Redis do Azure que permite-lhe importar dados para a Cache de Redis do Azure ou exportar dados da Cache de Redis do Azure por importar e exportar um instantâneo de base de dados de Cache de Redis (RDB) de uma cache premium para um blob de página numa conta de armazenamento do Azure. Isto permite-lhe migrar entre instâncias diferentes de Cache de Redis do Azure ou povoar a cache com os dados antes de utilização.

Importação pode ser utilizada para colocar Redis compatíveis RDB ficheiros a partir de qualquer servidor Redis em execução em qualquer nuvem ou o ambiente, incluindo Redis em execução no Linux, Windows ou qualquer fornecedor de nuvem como Amazon Web Services e outras pessoas. Importação de dados é uma forma fácil de criar uma cache com dados pré-preenchidos. Durante o processo de importação, a Cache de Redis do Azure carrega os ficheiros RDB storage do Azure para a memória e, em seguida, insere as chaves para a cache.

Exportação permite-lhe exportar os dados armazenados na Cache de Redis do Azure para Redis compatíveis RDB ficheiros. Pode utilizar esta funcionalidade para mover dados de uma instância da Cache de Redis do Azure para outra ou para outro servidor de Redis. Durante o processo de exportação, um ficheiro temporário é criado na VM que aloja a instância de servidor de Cache de Redis do Azure e o ficheiro é carregado para a conta de armazenamento designada. Quando concluir a operação de exportação com o estado de sucesso ou falha, o ficheiro temporário é eliminado.

Para obter mais informações, consulte [como importar dados para e exportar dados a partir da Cache de Redis do Azure](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reiniciar
O escalão premium permite-lhe reiniciar um ou mais nós da sua cache a pedido. Isto permite-lhe testar a aplicação para resiliência em caso de falha. Pode reiniciar os seguintes nós.

* Nó principal da cache
* Nó subordinado da cache
* Nós secundários e mestre da cache
* Ao utilizar uma cache premium com clustering, pode reiniciar o mestre, multisservidor ou ambos os nós para shards individuais na cache

Para obter mais informações, consulte [reiniciar](cache-administration.md#reboot) e [reiniciar FAQ](cache-administration.md#reboot-faq).

>[!NOTE]
>Reinicie a funcionalidade está agora ativada para todos os escalões de Cache de Redis do Azure.
>
>

## <a name="schedule-updates"></a>Atualizações agendadas
A funcionalidade de atualizações agendadas permite-lhe designar uma janela de manutenção para a sua cache. Quando a janela de manutenção é especificada, quaisquer atualizações do servidor de Redis são efetuadas durante este período. Para designar uma janela de manutenção, selecione os dias pretendidos e especifique a manutenção de início da janela de hora para cada dia. Tenha em atenção que o tempo de janela de manutenção está em UTC. 

Para obter mais informações, consulte [agendar atualizações](cache-administration.md#schedule-updates) e [agendar atualizações FAQ](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Apenas Redis servidor efectuadas durante a janela de manutenção agendada. Não é aplicável a janela de manutenção para atualizações do Azure ou atualizações para o sistema operativo VM.
> 
> 

## <a name="geo-replication"></a>Georreplicação

**Replicação geográfica** fornece um mecanismo para ligar duas instâncias de Cache de Redis do Azure do escalão Premium. Uma cache está designado como cache de principal ligada e outra como a secundária cache ligada. A cache ligada secundária torna-se só de leitura e os dados escritos para a cache do principal são replicados para a cache ligada secundária. Esta funcionalidade pode ser utilizada para replicar uma cache em regiões do Azure.

Para obter mais informações, consulte [como configurar a replicação geográfica para a Cache de Redis do Azure](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Dimensionar para o escalão premium
Para dimensionar o escalão premium, simplesmente, escolha um dos escalões premium no **alteração de escalão de preço** painel. Também pode escalar a sua cache no escalão premium com o PowerShell e a CLI. Para obter instruções passo a passo, consulte [como Cache de Redis do Azure de escala](cache-how-to-scale.md) e [como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passos seguintes
Criar uma cache e explorar as novas funcionalidades do escalão premium.

* [Como configurar a persistência para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md)
* [Como configurar o suporte da Rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md)
* [Como configurar o clustering para uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md)
* [Como importar dados para e a exportação de dados da Cache de Redis do Azure](cache-how-to-import-export-data.md)
* [Como administrar a Cache de Redis do Azure](cache-administration.md)

