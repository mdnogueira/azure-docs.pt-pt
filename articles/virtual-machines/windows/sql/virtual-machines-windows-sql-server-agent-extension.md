---
title: "Automatizar tarefas de gestão em VMs do SQL Server (Resource Manager) | Microsoft Docs"
description: "Este tópico descreve como gerir a extensão de agente do SQL Server, que automatiza as tarefas de administração do SQL Server específicas. Estes incluem a cópia de segurança automatizada, a aplicação de patches automatizada e integração do Cofre de chaves do Azure. Este tópico utiliza o modo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7152d184bb6d1d4b81aeb47e2c7c9160ada36023
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizar tarefas de gestão em Virtual Machines do Azure com a extensão de agente do SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../classic/sql-server-agent-extension.md)
> 
> 

A extensão de agente IaaS (SQLIaaSExtension) do SQL Server é executado em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma descrição geral dos serviços suportada por extensão, bem como as instruções para instalação, estado e remoção.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte [extensão de agente do servidor de SQL Server para o SQL Server VMs clássico](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados
A extensão de agente do SQL Server IaaS suporta as seguintes tarefas de administração:

| Funcionalidade de administração | Descrição |
| --- | --- |
| **Cópia de segurança automatizada do SQL** |Automatiza o agendamento de cópias de segurança para todas as bases de dados para a instância predefinida do SQL Server na VM. Para obter mais informações, consulte [cópia de segurança automatizada do SQL Server em Virtual Machines do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicação de patches automatizada do SQL** |Configura uma janela de manutenção durante os quais as atualizações à sua VM podem ocorrer, pelo que pode evitar atualizações durante períodos máximos para a carga de trabalho. Para obter mais informações, consulte [aplicação para o SQL Server em Virtual Machines do Azure (Resource Manager) de patches automatizada](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe automaticamente instalar e configurar o Cofre de chaves do Azure na sua VM do SQL Server. Para obter mais informações, consulte [configurar integração do Azure chave de cofre para SQL Server em VMs do Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Depois de instalado e em execução, a extensão de agente do SQL Server IaaS disponibiliza estas funcionalidades de administração no painel do SQL Server da máquina virtual no Portal do Azure e através do PowerShell do Azure para imagens do marketplace do SQL Server e através do Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para utilizar a extensão de agente do SQL Server IaaS na sua VM:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versões do SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**O Azure PowerShell**:

* [Transferir e configurar os comandos do Azure PowerShell mais recentes](/powershell/azure/overview)

## <a name="installation"></a>Instalação
A extensão de agente do IaaS do SQL Server é instalada automaticamente quando Aprovisiona uma das imagens de Galeria de máquina virtual do SQL Server. Se tiver de reinstalar a extensão manualmente destas VMs de SQL Server, utilize o seguinte comando do PowerShell:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

Também é possível instalar a extensão de agente do SQL Server IaaS numa máquina virtual apenas de SO Windows Server. Isto só é suportado se tiver instalado manualmente do SQL Server em que a máquina. Em seguida, instale a extensão manualmente utilizando o mesmo **conjunto AzureVMSqlServerExtension** cmdlet do PowerShell.

> [!NOTE]
> Se instalar manualmente a extensão de agente do IaaS do SQL Server numa VM apenas de SO Windows Server, não pode gerir as definições de configuração do SQL Server através do portal do Azure. Neste cenário, tem de se todas as alterações com o PowerShell.

## <a name="status"></a>Estado
É uma forma de verificar se a extensão está instalada para ver o estado do agente no Portal do Azure. Selecione **todas as definições** no painel da máquina virtual e, em seguida, clique em **extensões**. Deverá ver o **SQLIaaSExtension** extensões listada.

![Extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Também pode utilizar o **Get-AzureVMSqlServerExtension** cmdlet do Powershell do Azure.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

O comando anterior confirma que o agente está instalado e fornece informações de estado geral. Também pode obter informações de estado específicas sobre a cópia de segurança automatizada e Patching com os seguintes comandos.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Remoção
No Portal do Azure, pode desinstalar a extensão ao clicar no botão de reticências no **extensões** painel das propriedades da máquina virtual. Em seguida, clique em **eliminar**.

![Desinstalar a extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o **remover AzureRmVMSqlServerExtension** cmdlet do Powershell.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Passos Seguintes
Começar a utilizar um dos serviços de suportado pela extensão. Para obter mais detalhes, consulte os tópicos referenciados no [serviços suportados pelo](#supported-services) secção deste artigo.

Para obter mais informações sobre a execução do SQL Server em Azure Virtual Machines, consulte [SQL Server em Virtual Machines do Azure descrição-geral](virtual-machines-windows-sql-server-iaas-overview.md).

