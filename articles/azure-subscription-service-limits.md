---
title: "Limites de subscrição do Azure e quotas | Microsoft Docs"
description: "Fornece uma lista de subscrição do Azure comuns e limites de serviço, quotas e restrições. Isto inclui informações sobre como aumentar os limites juntamente com os valores máximos."
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: byvinyal
ms.openlocfilehash: ff5da6a07512c323cd281832d1ca8a4c0ec5a816
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Subscrição do Azure e limites de serviço, quotas e restrições
Este documento apresenta uma lista algumas dos limites mais comuns de Microsoft Azure, que também por vezes, são chamados quotas. Este documento atualmente não abrange todos os serviços do Azure. Ao longo do tempo, a lista será expandida e atualizada para cobrir mais da plataforma.

Visite [descrição geral de preços do Azure](https://azure.microsoft.com/pricing/) para obter mais informações sobre preços do Azure. Aqui, pode estimar os custos com o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) ou, visitando a página de detalhes de preços para um serviço (por exemplo, [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Para sugestões ajudar a gerir os custos, consulte [evitar custos inesperados com faturação do Azure e custos de gestão](billing/billing-getting-started.md).

> [!NOTE]
> Se pretende aumentar o limite ou acima da quota de **limite predefinido**, [abrir um pedido de suporte online do cliente, sem encargos](azure-supportability/resource-manager-core-quotas-request.md). Não podem ser gerados os limites acima o **limite máximo** valor mostrado nas seguintes tabelas. Se não houver nenhuma **limite máximo** coluna, em seguida, o recurso não têm limites ajustável.
>
> As subscrições de avaliação gratuitas não são elegíveis para o limite ou aumenta a quota. Se tiver uma versão de avaliação gratuita, pode atualizar para um [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) subscrição. Para obter mais informações, consulte [atualizar avaliação gratuita do Azure para pay as you go](billing/billing-upgrade-azure-subscription.md).
>

## <a name="limits-and-the-azure-resource-manager"></a>Limites e o Gestor de recursos do Azure
Agora é possível combinar vários recursos do Azure num único grupo de recursos do Azure. Quando utilizar grupos de recursos, os limites que uma vez foram global passará a ser geridos a um nível regional com o Azure Resource Manager. Para obter mais informações sobre grupos de recursos do Azure, consulte [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Os limites abaixo, uma nova tabela foi adicionada para refletir as diferenças nos limites ao utilizar o Azure Resource Manager. Por exemplo, não há um **limites de subscrição** tabela e um **limites de subscrição - Azure Resource Manager** tabela. Quando um limite aplica-se a ambos os cenários, só é apresentada na primeira tabela. A menos que indicado em contrário, os limites são globais em todas as regiões.

> [!NOTE]
> É importante realçar que as quotas para os recursos em grupos de recursos do Azure estão por regiões acessível através da sua subscrição e não estão por subscrição, como as quotas de gestão de serviço. Vamos utilizar vCPU quotas como exemplo. Se precisar de pedir um aumento de quota com suporte para vCPUs, terá de decidir quantos vCPUs que pretende utilizar em que regiões e, em seguida, efetue um pedido específico para quotas de vCPU do grupo de recursos do Azure para as quantidades e regiões que pretende. Por conseguinte, se precisar de utilizar 30 vCPUs na Europa Ocidental para executar a aplicação não existe, deve pedir especificamente 30 vCPUs na Europa Ocidental. Mas não terá uma quota de vCPU aumentam a qualquer outra região – apenas Europa Ocidental terão a quota de 30 vCPU.
> <!-- -->
> Como resultado, pode útil considerar a decidir o que as quotas de grupo de recursos do Azure tem de ser para a carga de trabalho em qualquer região de um e pedir que quantidade em cada região na qual estiver a considerar implementação. Consulte [resolução de problemas de implementação](resource-manager-common-deployment-errors.md) para obter mais ajuda a detetar as quotas atuais para regiões específicas.
>
>

## <a name="service-specific-limits"></a>Limites de serviço específicos
* [Active Directory](#active-directory-limits)
* [Gestão de API](#api-management-limits)
* [Serviço de Aplicações](#app-service-limits)
* [Gateway de Aplicação](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automatização](#automation-limits)
* [BD do Cosmos para o Azure](#azure-cosmos-db-limits)
* [Grelha de eventos do Azure](#azure-event-grid-limits)
* [Cache de Redis do Azure](#azure-redis-cache-limits)
* [Cópia de segurança](#backup-limits)
* [Batch](#batch-limits)
* [Serviços BizTalk](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Serviços Cloud](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [DNS](#dns-limits)
* [Hubs de Eventos](#event-hubs-limits)
* [Hub IoT](#iot-hub-limits)
* [Serviço de aprovisionamento de dispositivos do IoT Hub](#iot-hub-device-provisioning-service-limits)
* [Cofre de Chaves](#key-vault-limits)
* [Análise de registo / Operational Insights](#log-analytics-limits)
* [Serviços de Multimédia](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Serviços móveis](#mobile-services-limits)
* [Monitorizar](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Redes](#networking-limits)
* [Observador de rede](#network-watcher-limits)
* [Serviço de Hub de notificação](#notification-hub-service-limits)
* [Grupo de Recursos](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [Pesquisa](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Base de Dados SQL](#sql-database-limits)
* [Armazenamento](#storage-limits)
* [Sistema StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Subscrição](#subscription-limits)
* [Gestor de Tráfego](#traffic-manager-limits)
* [Máquinas Virtuais](#virtual-machines-limits)
* [Conjuntos de Dimensionamento de Máquinas Virtuais](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limites de subscrição
#### <a name="subscription-limits"></a>Limites de subscrição
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites de subscrição - Azure Resource Manager
Os seguintes limites aplicam-se ao utilizar o Azure Resource Manager e os grupos de recursos do Azure. Limites de utilização não mudaram com o Azure Resource Manager não estão listados abaixo. Consulte a tabela anterior para esses limites.

Para obter informações sobre como lidar com limites de pedidos de Gestor de recursos, consulte [limitação Gestor de recursos pedidos](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites do grupo de recursos
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de máquinas virtuais
#### <a name="virtual-machine-limits"></a>Limites de máquinas virtuais
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de máquinas virtuais - Azure Resource Manager
Os seguintes limites aplicam-se ao utilizar o Azure Resource Manager e os grupos de recursos do Azure. Limites de utilização não mudaram com o Azure Resource Manager não estão listados abaixo. Consulte a tabela anterior para esses limites.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limites de conjuntos de dimensionamento de máquina virtual
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limites de instâncias de contentor
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limites de registo do contentor
A tabela seguinte fornece detalhes sobre as funcionalidades e limites do básicas, Standard e Premium [escalões de serviço](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="networking-limits"></a>Limites de rede
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limites de rede
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limites de Gateway de aplicação
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limites de observador de rede
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites do Gestor de tráfego
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limites DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limites de armazenamento
Para obter detalhes adicionais sobre os limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do Storage do Azure](storage/common/storage-scalability-targets.md).

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limites de armazenamento de Blobs do Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limites de ficheiros do Azure
Para obter detalhes adicionais sobre os limites de ficheiros do Azure, consulte [metas de desempenho e escalabilidade de ficheiros do Azure](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limita a sincronização de ficheiros do Azure
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limites de armazenamento de filas do Azure
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limites de armazenamento de tabela do Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Consulte [tamanhos de Máquina Virtual](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes adicionais.

#### <a name="managed-virtual-machine-disks"></a>Discos de máquinas de virtuais gerido

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquinas de virtuais não geridos

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Limites de serviços cloud
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limites de serviço de aplicações
Os seguintes limites de serviço de aplicações incluem os limites de Web Apps, Mobile Apps, API Apps e Logic Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites de programador
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites do batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limites de serviços BizTalk
A tabela seguinte mostra os limites do Biztalk Services do Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limites de Cosmos BD do Azure
BD do Cosmos do Azure é uma base de dados de escala global em que o débito e armazenamento podem ser escalados para lidar com que a sua aplicação requer. Se tiver alguma questão sobre a escala fornece a base de dados do Azure Cosmos, envie um e-mail para askcosmosdb@microsoft.com.

### <a name="mobile-engagement-limits"></a>Limites de Mobile Engagement
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Limites de pesquisa
Escalões de preços determinam a capacidade e limites do seu serviço de pesquisa. As camadas incluem:

* *Livre* serviço multi-inquilino, partilhado com outros subscritores do Azure, concebida para avaliação e de desenvolvimento pequeno projetos.
* *Básico* fornece recursos de informática dedicados para cargas de trabalho de produção em escala mais pequena, com até três réplicas para cargas de trabalho de consulta altamente disponível.
* *Padrão (S1, S2, S3, S3 alta densidade)* é para cargas de trabalho de produção maior. Vários níveis existem dentro o escalão padrão para que possa escolher uma configuração de recursos que melhor de adeque ao seu perfil de carga de trabalho.

**Limites por subscrição**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço de pesquisa**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para obter mais informações sobre limites de um nível mais granular, tais como o tamanho do documento, consultas por segundo, chaves, pedidos e respostas, consulte o artigo [Service limites na Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limites CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Limites de monitor
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limites de serviço de Hub de notificação
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de Hubs de eventos
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites do IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limites de serviço de aprovisionamento de dispositivos IoT Hub
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limites de fábrica de dados
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites de análise do Data Lake
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limites de data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="stream-analytics-limits"></a>Limita o do Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limita do Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Limites de grelha de eventos do Azure
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="storsimple-system-limits"></a>Limites de sistema do StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Limita a análise de registos
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites de cópia de segurança
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limites do Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites do Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites de API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limites de Cache de Redis do Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites do Cofre de chaves
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites de automatização
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limites de base de dados SQL
Para os limites de base de dados SQL, consulte [dos limites de recursos de base de dados do SQL Server](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Consultar também
[Compreender os limites do Azure e aumenta](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Máquina virtual e tamanhos do serviço em nuvem do Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Tamanhos de serviços Cloud](cloud-services/cloud-services-sizes-specs.md)
