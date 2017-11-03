---
title: "Gerir serviços de análise do Azure com o PowerShell | Microsoft Docs"
description: "Gestão de Analysis Services do Azure com o PowerShell."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 52c74feacb8cf2e7005f6b284d7b55078449dc79
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerir serviços de análise do Azure com o PowerShell

Este artigo descreve os cmdlets do PowerShell utilizados para executar o servidor de Analysis Services do Azure e tarefas de gestão de base de dados. 

Tarefas de gestão de servidor, tais como criar ou eliminar um servidor, suspender ou retomar as operações do servidor ou alterar o nível de serviço (camada) utilizam cmdlets do Azure Resource Manager (AzureRM). Outras tarefas para gerir bases de dados, tais como adicionar ou remover membros da função, processamento, ou criação de partições utilizar cmdlets incluídos no mesmo módulo SqlServer como SQL Server Analysis Services.

## <a name="permissions"></a>Permissões
A maioria das tarefas de PowerShell necessitam de que ter privilégios de administrador no servidor do Analysis Services que está a gerir. Tarefas agendadas do PowerShell são operações autónomas. A conta que executa o programador tem de ter privilégios de administrador no servidor do Analysis Services. 

Para operações de servidores utilizando AzureRm cmdlets, a conta ou a conta que executa o programador tem também de pertencer à função de proprietário para o recurso no [controlo de acesso em funções do Azure (RBAC)](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Operações do servidor 
Cmdlets de Analysis Services do Azure estão incluídos no [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) módulo de componente. Para instalar os módulos do cmdlet AzureRM, consulte [cmdlets do Azure Resource Manager](/powershell/azure/overview) na galeria do PowerShell.

|Cmdlet|Descrição| 
|------------|-----------------| 
|[AzureAnalysisServicesAccount adicionar](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|Adiciona uma conta a utilizar para pedidos de cmdlet do Azure Analysis Services server autenticada.| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Obtém os detalhes de uma instância de servidor.|  
|[Novo AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Cria uma instância de servidor.|   
|[Remover AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Remove uma instância de servidor.|  
|[AzureAnalysisServicesInstance de reinício](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Reinicia uma instância do servidor do Analysis Services no ambiente atualmente com sessão iniciado; especificados no comando Adicionar AzureAnalysisServicesAccount.|  
|[Retomar AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Retoma uma instância de servidor.|  
|[AzureRmAnalysisServicesServer suspender](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Suspende uma instância de servidor.| 
|[Conjunto AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifica uma instância de servidor.|   
|[Teste AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Testa a existência de uma instância de servidor.| 

## <a name="database-operations"></a>Operações de base de dados

Operações de base de dados de Analysis Services do Azure utilizam o mesmo [SqlServer](https://www.powershellgallery.com/packages/SqlServer) módulo como SQL Server Analysis Services. No entanto, nem todos os cmdlets são suportados para o Azure Analysis Services. 

O módulo de SqlServer fornece cmdlets de gestão de base de dados específicas de tarefas, bem como o cmdlet Invoke-ASCmd para fins gerais que aceita uma consulta da linguagem de Scripting de modelo em tabela (TMSL) ou script. Os seguintes cmdlets no módulo SqlServer são suportados para o Azure Analysis Services.

  
|Cmdlet|Descrição|
|------------|-----------------| 
|[RoleMember adicionar](https://msdn.microsoft.com/library/hh510167.aspx)|Adicione um membro a uma função de base de dados.| 
|[Cópia de segurança ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Cópia de segurança uma base de dados do Analysis Services.|  
|[Remover RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Remova um membro de uma função de base de dados.|   
|[ASCmd invocar](https://msdn.microsoft.com/library/hh479579.aspx)|Execute um script TMSL.|
|[ProcessASDatabase invocar](https://msdn.microsoft.com/library/mt651773.aspx)|Processar uma base de dados.|  
|[ProcessPartition invocar](https://msdn.microsoft.com/library/hh510164.aspx)|Processar uma partição.| 
|[ProcessTable invocar](https://msdn.microsoft.com/library/mt651774.aspx)|Processo de uma tabela.|  
|[Partição de intercalação](https://msdn.microsoft.com/library/hh479576.aspx)|Uma partição de intercalação.|  
|[Restauro ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Restaure uma base de dados do Analysis Services.| 
  

## <a name="related-information"></a>Informações relacionadas

* [Transferir o módulo do PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Transferir o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo de SqlServer na galeria do PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabela modelo de programação para 1200 de nível de compatibilidade e superiores](https://msdn.microsoft.com/library/mt712541.aspx)
