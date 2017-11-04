---
title: "-Máquinas virtuais do Azure - descrição-geral de grupos de disponibilidade do SQL Server | Microsoft Docs"
description: Este artigo apresenta os grupos de disponibilidade do SQL Server em virtual machines do Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 2cbb9ff3b2d13996b1b8dc64aa833807c264c877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introdução ao SQL Server Always On nos grupos de disponibilidade em máquinas virtuais do Azure #

Este artigo apresenta os grupos de disponibilidade do SQL Server em Azure Virtual Machines. 

Always On nos grupos de disponibilidade em Azure Virtual Machines são semelhantes aos Always On nos grupos de disponibilidade no local. Para obter mais informações, consulte [sempre em grupos de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um grupo de disponibilidade de servidor de SQL completa em Azure Virtual Machines.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A principal diferença para um grupo de disponibilidade em Azure Virtual Machines, é que as máquinas virtuais do Azure, requer um [Balanceador de carga](../../../load-balancer/load-balancer-overview.md). O Balanceador de carga se os endereços IP para o serviço de escuta do grupo de disponibilidade. Se tiver mais do que um grupo de disponibilidade cada grupo requer um serviço de escuta. Um balanceador de carga pode suportar vários serviços de escuta.

Quando estiver pronto para criar um aroup de disponibilidade do SQL Server em Azure Virtual Machines, consulte estes tutoriais.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Criar automaticamente um grupo de disponibilidade a partir de um modelo

[Configurar grupo de disponibilidade Always On na VM do Azure automaticamente - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Criar manualmente um grupo de disponibilidade no portal do Azure

Também pode criar as máquinas virtuais por si sem o modelo. Em primeiro lugar, concluir os pré-requisitos, em seguida, criar o grupo de disponibilidade. Consulte os tópicos seguintes: 

- [Configurar os pré-requisitos para SQL Server Always On nos grupos de disponibilidade em Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Crie sempre no grupo de disponibilidade para melhorar a disponibilidade e recuperação após desastre](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passos seguintes

[Configurar um SQL Server sempre num grupo de disponibilidade em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md).
