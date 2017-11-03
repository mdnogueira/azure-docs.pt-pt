---
title: "Como desinstalar a ferramenta de tarefas de bases de dados elástica"
description: "Saiba como a desinstalação dos componentes de tarefas de bases de dados elásticas com o portal do Azure do PowerShell."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: Inactive
ms.custom: scale out apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 5e665ee8cc9efacbd31111dc0458ad6096e457c0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="uninstall-elastic-database-jobs-components"></a>Desinstalar componentes de tarefas da base de dados elástica
**As tarefas de base de dados elásticas** componentes podem ser desinstalados através do portal do Azure ou do PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Desinstalar componentes de tarefas de bases de dados elásticas com o portal do Azure
1. Abra o [Portal do Azure](https://portal.azure.com/).
2. Navegue para a subscrição que contém **tarefas de bases de dados elásticas** componentes, nomeadamente a subscrição na base de dados elásticas foram instalados componentes de tarefas.
3. Clique em **procurar** e clique em **grupos de recursos**.
4. Selecione o grupo de recursos com o nome "__ElasticDatabaseJob".
5. Elimine o grupo de recursos.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Desinstalar componentes de tarefas de bases de dados elásticas com o PowerShell
1. Inicie uma janela de comando do PowerShell do Microsoft Azure e navegue para o subdiretório de ferramentas na pasta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: tipo **cd ferramentas**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > Ferramentas de cd
2. Execute o script do PowerShell de.\UninstallElasticDatabaseJobs.ps1.
   
     PS c:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > ficheiro de desbloqueio.\UninstallElasticDatabaseJobs.ps1 PS c\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >.\UninstallElasticDatabaseJobs.ps1

Ou simplesmente, execute o script seguinte, partindo do princípio de predefinição valores onde utilizado numa instalação dos componentes:

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>Passos seguintes
Para instalar novamente as tarefas de bases de dados elásticas, consulte [instalar o serviço de tarefa da base de dados elástica](sql-database-elastic-jobs-service-installation.md)

Para obter uma descrição geral das tarefas de bases de dados elásticas, consulte [descrição geral de tarefas de bases de dados elásticas](sql-database-elastic-jobs-overview.md).

<!--Image references-->


