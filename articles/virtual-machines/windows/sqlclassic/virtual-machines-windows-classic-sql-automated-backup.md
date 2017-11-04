---
title: "Cópia de segurança automatizada para máquinas de virtuais do SQL Server (clássica) | Microsoft Docs"
description: "Explica a funcionalidade de cópia de segurança automatizada do SQL Server em execução em máquinas virtuais do Azure com o Resource Manager. "
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: f7664291c2f45c422d52f682d08dbb67ab32b099
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Cópia de segurança automatizada do SQL Server em máquinas virtuais do Azure (clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Clássico](../classic/sql-automated-backup.md)
> 
> 

Configura automaticamente a cópia de segurança automatizada [cópia de segurança gerida no Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os existentes e novas bases de dados numa VM do Azure a executar o SQL Server 2014 Standard ou Enterprise. Isto permite-lhe configurar cópias de segurança regular da base de dados que utilizam o armazenamento de Blobs do Azure durável. Cópia de segurança automatizada depende o [extensão de agente do SQL Server IaaS](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de recursos deste artigo, consulte [cópia de segurança automatizada do SQL Server no Gestor de recursos de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada, considere os seguintes pré-requisitos:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão/edição do SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016 ainda não é suportada para cópia de segurança automatizada.
> 
> 

**Configuração de base de dados**:

* Bases de dados de destino tem de utilizar o modelo de recuperação completo.

**O Azure PowerShell**:

* [Instalar os comandos do Azure PowerShell mais recentes](/powershell/azure/overview).

**Extensão de IaaS do SQL Server**:

* [Instale a extensão do SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizada. Para VMs clássicas, tem de utilizar o PowerShell para configurar estas definições.

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** |Ativar/desativar (desativada) |Ativa ou desativa a cópia de segurança automatizada para uma VM do Azure a executar o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** |1-30 dias (30 dias) |O número de dias a manter uma cópia de segurança. |
| **Conta de armazenamento** |Conta de armazenamento do Azure (a conta de armazenamento criada para a VM especificada) |Uma conta de armazenamento do Azure a utilizar para armazenar ficheiros de cópia de segurança automatizada no blob storage. Um contentor é criado nesta localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e nome da máquina. |
| **Encriptação** |Ativar/desativar (desativada) |Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada no mesmo contentor automaticbackup utilizando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, é gerado um novo certificado com essa palavra-passe, mas o certificado antigo permanece restaurar cópias de segurança anteriores. |
| **Palavra-passe** |Texto de palavra-passe (nenhum) |Uma palavra-passe para as chaves de encriptação. Isto apenas é necessário se a encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que da cópia de segurança foi efetuada. | **Bases de dados de cópia de segurança do sistema** | Ativar/desativar (desativada) | Fazer cópias de segurança completas de mestra, modelo e MSDB |
| **Configurar agenda de cópia de segurança** | Manual/automática (automatizada) | Selecione **automatizada** automaticamente demorar completa e iniciar as cópias de segurança com base em crescimento de registo. Selecione **Manual** para especificar a agenda para completo e cópias de segurança de registo. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo seguinte do PowerShell, a cópia de segurança automatizada está configurada para uma VM de 2014 de servidor existente do SQL Server. O **New-AzureVMSqlServerAutoBackupConfig** comando configura as definições de cópia de segurança automatizada para armazenar as cópias de segurança na conta do storage do Azure especificada pela variável $storageaccount. Estas cópias de segurança serão mantidas durante 10 dias. O **conjunto AzureVMSqlServerExtension** comando atualiza a VM do Azure especificada com estas definições.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode demorar alguns minutos para instalar e configurar o agente do SQL Server IaaS.

Para ativar a encriptação, modificar o script anterior para passar o parâmetro EnableEncryption juntamente com uma palavra-passe (cadeia segura) para o parâmetro CertificatePassword. O script seguinte Ativa as definições de cópia de segurança automatizada no exemplo anterior e adiciona a encriptação.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desativar a cópia de segurança automática, execute o script mesmo sem a **-ativar** parâmetro para o **New-AzureVMSqlServerAutoBackupConfig**. Tal como acontece com a instalação, pode demorar alguns minutos para desativar a cópia de segurança automatizada.

> [!NOTE]
> A desativação e desinstalar o agente do SQL Server IaaS não remove as definições de cópia de segurança gerida configuradas anteriormente. Deve desativar a cópia de segurança automatizada antes de desativar ou desinstalar o agente do SQL Server IaaS.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Cópia de segurança automatizada configura a cópia de segurança gerida em VMs do Azure. Pelo que é importante para [consulte a documentação para cópia de segurança gerida](https://msdn.microsoft.com/library/dn449496.aspx) para compreender o comportamento e implicações.

Pode encontrar a cópia de segurança adicional e restaurar as orientações para o SQL Server em VMs do Azure no seguinte tópico: [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte [extensão de agente do SQL Server IaaS](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Virtual Machines do Azure descrição-geral](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

