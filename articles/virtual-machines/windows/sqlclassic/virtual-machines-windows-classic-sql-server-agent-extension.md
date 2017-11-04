---
title: "Automatizar tarefas de gestão em VMs do SQL Server (clássica) | Microsoft Docs"
description: "Este tópico descreve como gerir a extensão de agente do SQL Server, que automatiza as tarefas de administração do SQL Server específicas. Estes incluem a cópia de segurança automatizada, a aplicação de patches automatizada e integração do Cofre de chaves do Azure. Este tópico utiliza o modo de implementação clássica."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30fa9128cd51a7498449c991b58500ad9acdd3d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizar tarefas de gestão em Virtual Machines do Azure com a extensão de agente do SQL Server (clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../classic/sql-server-agent-extension.md)
> 
>
 
A extensão de agente IaaS (SQLIaaSAgent) do SQL Server é executado em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma descrição geral dos serviços suportada por extensão, bem como as instruções para instalação, estado e remoção.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de recursos deste artigo, consulte [extensão de agente do SQL Server para o SQL Server VMs Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados
A extensão de agente do SQL Server IaaS suporta as seguintes tarefas de administração:

| Funcionalidade de administração | Descrição |
| --- | --- |
| **Cópia de segurança automatizada do SQL** |Automatiza o agendamento de cópias de segurança para todas as bases de dados para a instância predefinida do SQL Server na VM. Para obter mais informações, consulte [cópia de segurança automatizada do SQL Server em Virtual Machines do Azure (clássica)](../classic/sql-automated-backup.md). |
| **Aplicação de patches automatizada do SQL** |Configura uma janela de manutenção durante os quais as atualizações à sua VM podem ocorrer, pelo que pode evitar atualizações durante períodos máximos para a carga de trabalho. Para obter mais informações, consulte [aplicação para o SQL Server em Virtual Machines do Azure (clássica) de patches automatizada](../classic/sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe automaticamente instalar e configurar o Cofre de chaves do Azure na sua VM do SQL Server. Para obter mais informações, consulte [configurar integração do Azure chave de cofre para SQL Server em VMs do Azure (clássica)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para utilizar a extensão de agente do SQL Server IaaS na sua VM:

### <a name="operating-system"></a>Sistema operativo:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versões do SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Transferir e configurar os comandos do Azure PowerShell mais recentes](/powershell/azure/overview).

Inicie o Windows PowerShell e ligue-o à sua subscrição do Azure com o **Add-AzureAccount** comando.

    Add-AzureAccount

Se tiver várias subscrições, utilize **Select-AzureSubscription** para selecionar a subscrição que contém o seu destino VMS clássicas.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste momento, pode obter uma lista de máquinas virtuais clássicas e os respetivos nomes de serviço associado com o **Get-AzureVM** comando.

    Get-AzureVM

## <a name="installation"></a>Instalação
Para VMs clássicas, tem de utilizar o PowerShell para instalar a extensão de agente do SQL Server IaaS e configurar os respetivos serviços associados. Utilize o **conjunto AzureVMSqlServerExtension** cmdlet do PowerShell para instalar a extensão. Por exemplo, o seguinte comando instala a extensão numa VM do Windows Server (clássica) e de nomes, "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se atualizar para a versão mais recente da extensão de agente do IaaS do SQL Server, tem de reiniciar a máquina virtual depois de atualizar a extensão.

> [!NOTE]
> As máquinas virtuais clássicas não tem uma opção para instalar e configurar a extensão de agente do SQL Server IaaS através do portal.
> 
> 

## <a name="status"></a>Estado
É uma forma de verificar se a extensão está instalada para ver o estado do agente no Portal do Azure. Selecione uma máquina virtual listada no painel da máquina virtual e, em seguida, clique em **extensões**. Deverá ver o **SQLIaaSAgent** extensões listada.

![Extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Também pode utilizar o **Get-AzureVMSqlServerExtension** cmdlet do Powershell do Azure.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Remoção
No Portal do Azure, pode desinstalar a extensão ao clicar no botão de reticências no **extensões** painel das propriedades da máquina virtual. Em seguida, clique em **desinstalação**.

![Desinstalar a extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o **remover AzureVMSqlServerExtension** cmdlet do Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Passos Seguintes
Começar a utilizar um dos serviços de suportado pela extensão. Para obter mais detalhes, consulte os tópicos referenciados no [serviços suportados pelo](#supported-services) secção deste artigo.

Para obter mais informações sobre a execução do SQL Server em Azure Virtual Machines, consulte [SQL Server em Virtual Machines do Azure descrição-geral](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

