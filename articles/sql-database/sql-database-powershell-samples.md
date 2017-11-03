---
title: Exemplos de script do PowerShell do Azure para a base de dados SQL | Microsoft Docs
description: "Exemplos para ajudar a criar e gerir servidores de base de dados do Azure SQL, conjuntos elásticos, bases de dados e as firewalls do script do PowerShell do Azure."
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: On Demand
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: 1d1692cc43a7a5ec50c0689706d93a784a5fed88
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Exemplos do PowerShell do Azure para a SQL Database do Azure

A tabela seguinte inclui ligações para os scripts de Azure PowerShell de exemplo para a SQL Database do Azure.

| |  |
|---|---|
|**Criar uma base de dados e um conjunto elástico**||
| [Criar uma base de dados e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria uma base de dados SQL do Azure e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados agrupados](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria conjuntos elásticos SQL Database do Azure, move agrupados bases de dados e níveis de desempenho é alterado.|
|**Configurar a georreplicação e ativação pós-falha**||
| [Configurar e utilizar a georreplicação ativa única da base de dados de ativação pós-falha](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure e a ativação pós-falha-lo para a réplica secundária. |
| [Configurar e utilizar a georreplicação ativa agrupado da base de dados de ativação pós-falha](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure num agrupamento elástico de SQL e falha, a réplica secundária. |
| [Configurar e ativação pós-falha a ativação pós-falha de grupo para uma base de dados (pré-visualização)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura um grupo de ativação pós-falha para uma instância de servidor da SQL Database do Azure, adiciona uma base de dados para o grupo de ativação pós-falha e a ativação pós-falha-lo para o servidor secundário |
|**Dimensionar a bases de dados individuais e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este monitores de script do PowerShell as métricas de desempenho de uma base de dados SQL do Azure, dimensiona-lo para um nível de desempenho superior e cria uma regra de alerta uma das métricas de desempenho. |
| [Dimensionar o conjunto elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este monitores de script do PowerShell as métricas de desempenho de um conjunto elástico de SQL Database do Azure, dimensiona-lo para um nível de desempenho superior e cria uma regra de alerta uma das métricas de desempenho.  |
| **Auditoria e deteção de ameaças** |
| [Configurar a auditoria e deteção de ameaças](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a ameaça de auditoria e as políticas de deteção para uma base de dados SQL do Azure. |
| **Restaurar, copiar e importar uma base de dados**||
| [Restaurar uma base de dados](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell restaura uma base de dados SQL do Azure a partir de uma cópia de segurança georredundante e restaura a base de dados eliminada do SQL do Azure para a cópia de segurança mais recente. |
| [Copiar uma base de dados para o novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell cria uma cópia da base de dados SQL do Azure existente num novo servidor SQL do Azure. |
| [Importar uma base de dados de um ficheiro de bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell importa uma base de dados para um servidor de SQL do Azure a partir de um ficheiro de bacpac. |
| **Sincronizar os dados entre bases de dados**||
| [Sincronizar os dados entre bases de dados SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a sincronização de dados a sincronização entre várias bases de dados SQL do Azure. |
| [Sincronizar os dados entre a base de dados SQL e SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a sincronização de dados a sincronização entre uma base de dados SQL do Azure e uma base de dados do SQL Server no local. |
|||
|||
