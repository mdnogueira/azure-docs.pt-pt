---
title: "VMs de SQL Server (Resource Manager) para a aplicação de patches automatizada | Microsoft Docs"
description: "Explica a funcionalidade de aplicação de patches automatizada para o SQL Server máquinas virtuais em execução no Azure utilizando o Gestor de recursos."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 7d501ab45a85010a8dbfd6135d77f18f1743354e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Aplicação de Patches Automatizada para o SQL Server nas Máquinas Virtuais do Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Clássico](../classic/sql-automated-patching.md)
> 
> 

Patching automatizada estabelece uma janela de manutenção para uma Máquina Virtual do Azure com o SQL Server. As atualizações automáticas só podem ser instaladas durante este período de manutenção. Para o SQL Server, este rescriction garante atualizações do sistema e os reinícios associados ocorrerem o momento melhor possíveis para a base de dados. Patching automatizada depende o [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte [aplicação de patches automatizada para o SQL Server no modelo clássico de máquinas virtuais do Azure](../classic/sql-automated-patching.md).

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

* [Instalar os comandos do Azure PowerShell mais recentes](/powershell/azure/overview) se pretender configurar a aplicação de patches automatizada com o PowerShell.

> [!NOTE]
> Patching automatizada baseia-se na extensão de agente do SQL Server IaaS. Imagens de galeria da máquina virtual SQL atuais adicionar esta extensão, por predefinição. Para obter mais informações, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para a aplicação de patches automatizada. Os passos de configuração reais variam dependendo se utilizar o portal do Azure ou os comandos do Azure Windows PowerShell.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/desativar (desativada) |Ativa ou desativa a aplicação de patches automatizada para uma máquina virtual do Azure. |
| **Agenda de manutenção** |Todos os dias, segunda, Terça, quarta-feira, Quinta-feira, sexta-feira, Sábado, Domingo |A agenda para transferir e instalar atualizações do Windows, o SQL Server e o Microsoft para a máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitido para concluir a transferência e instalação de atualizações. |
| **Categoria de correção** |Importante |A categoria de atualizações a transferir e instalar. |

## <a name="configuration-in-the-portal"></a>Configuração no Portal do
Pode utilizar o portal do Azure para configurar a aplicação de patches automatizada durante o aprovisionamento de ou para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Utilize o portal do Azure para configurar a aplicação de patches automatizada quando cria uma nova máquina de Virtual do SQL Server no modelo de implementação Resource Manager.

No **definições do SQL Server** painel, selecione **aplicação de patches automatizada**. A seguinte captura de ecrã de portal do Azure mostra o **aplicação de patches de automatizada do SQL Server** painel.

![SQL Server aplicação de patches automatizada no portal do Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para o contexto, consulte o tópico completo sobre [aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais do SQL Server existentes, selecione a máquina virtual do SQL Server. Em seguida, selecione o **configuração do SQL Server** secção o **definições** painel.

![SQL automática aplicação de patches para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

No **configuração do SQL Server** painel, clique em de **editar** botão no automatizada secção de aplicação de patches.

![Configurar a aplicação de patches de automatizada do SQL Server para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Quando terminar, clique em de **OK** botão na parte inferior do **configuração do SQL Server** painel para guardar as alterações.

Se pretende ativar a aplicação de patches automatizada pela primeira vez, o Azure configura o agente do SQL Server IaaS em segundo plano. Durante este período, o portal do Azure poderá não mostrar que a aplicação de patches automatizada está configurada. Aguarde alguns minutos para que o agente ser instalado, configurado. Depois de que o portal do Azure reflete as novas definições.

> [!NOTE]
> Também pode configurar a aplicação de patches automatizada através de um modelo. Para obter mais informações, consulte [modelo de início rápido do Azure para a aplicação de patches automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
Após o aprovisionamento da VM do SQL Server, utilize o PowerShell para configurar a aplicação de patches automatizada.

No exemplo seguinte, o PowerShell é utilizado para configurar a aplicação de patches automatizada numa VM do SQL Server existente. O **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig** comando configura uma nova janela de manutenção para atualizações automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

Com base neste exemplo, a tabela seguinte descreve o efeito prático no destino da VM do Azure:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados cada Quinta-feira. |
| **MaintenanceWindowStartingHour** |Atualizações de begin às 11:00. |
| **MaintenanceWindowsDuration** |Correções de erros tem de ser instaladas em 120 minutos. Com base na hora de início, tem de concluir por 1:00 pm. |
| **PatchCategory** |A definição apenas possível para este parâmetro é **importante**. |

Pode demorar alguns minutos para instalar e configurar o agente do SQL Server IaaS.

Para desativar a aplicação de patches automatizada, execute o script mesmo sem a **-ativar** parâmetro para o **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**. A ausência de **-ativar** parâmetro sinalizar o comando para desativar esta funcionalidade.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras tarefas de automatização disponíveis, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Virtual Machines do Azure descrição-geral](virtual-machines-windows-sql-server-iaas-overview.md).

