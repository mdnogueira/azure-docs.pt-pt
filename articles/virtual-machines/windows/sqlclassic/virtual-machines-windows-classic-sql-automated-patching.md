---
title: "Aplicação de patches para VMs de SQL Server (clássica) automatizada | Microsoft Docs"
description: "Explica a funcionalidade de aplicação de patches automatizada para o SQL Server máquinas virtuais em execução no Azure utilizando o modo de implementação clássica."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 1959871141f196ba80ffd7b37e62e5ea5b42dba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Aplicação para o SQL Server em máquinas virtuais do Azure (clássica) de patches automatizada
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Clássico](../classic/sql-automated-patching.md)
> 
> 

Patching automatizada estabelece uma janela de manutenção para uma Máquina Virtual do Azure com o SQL Server. As atualizações automáticas só podem ser instaladas durante este período de manutenção. Para o SQL Server, isto garante que atualizações do sistema e os reinícios associados ocorrerem o momento melhor possíveis para a base de dados. Patching automatizada depende o [extensão de agente do SQL Server IaaS](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de recursos deste artigo, consulte [aplicação de patches automatizada para o SQL Server no Gestor de recursos de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a aplicação de patches automatizada, considere os seguintes pré-requisitos:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão do SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**O Azure PowerShell**:

* [Instalar os comandos do Azure PowerShell mais recentes](/powershell/azure/overview).

**Extensão de IaaS do SQL Server**:

* [Instale a extensão do SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para a aplicação de patches automatizada. Para VMs clássicas, tem de utilizar o PowerShell para configurar estas definições.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/desativar (desativada) |Ativa ou desativa a aplicação de patches automatizada para uma máquina virtual do Azure. |
| **Agenda de manutenção** |Todos os dias, segunda, Terça, quarta-feira, Quinta-feira, sexta-feira, Sábado, Domingo |A agenda para transferir e instalar atualizações do Windows, o SQL Server e o Microsoft para a máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitido para concluir a transferência e instalação de atualizações. |
| **Categoria de correção** |Importante |A categoria de atualizações a transferir e instalar. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo seguinte, o PowerShell é utilizado para configurar a aplicação de patches automatizada numa VM do SQL Server existente. O **New-AzureVMSqlServerAutoPatchingConfig** comando configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela seguinte descreve o efeito prático no destino da VM do Azure:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados cada Quinta-feira. |
| **MaintenanceWindowStartingHour** |Atualizações de begin às 11:00. |
| **MaintenanceWindowsDuration** |Correções de erros tem de ser instaladas em 120 minutos. Com base na hora de início, tem de concluir por 1:00 pm. |
| **PatchCategory** |A definição apenas possível para este parâmetro é "Importante". |

Pode demorar alguns minutos para instalar e configurar o agente do SQL Server IaaS.

Para desativar a aplicação de patches automatizada, execute o mesmo script sem-ativar um parâmetro para o novo-AzureVMSqlServerAutoPatchingConfig. Tal como acontece com a instalação, pode demorar alguns minutos para desativar a aplicação de patches automatizada.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras tarefas de automatização disponíveis, consulte [extensão de agente do SQL Server IaaS](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Virtual Machines do Azure descrição-geral](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

