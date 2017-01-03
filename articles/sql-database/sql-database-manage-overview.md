---
title: "Descrição geral: ferramentas de gestão para a Base de Dados SQL | Microsoft Docs"
description: "Compara as ferramentas e as opções para gerir a Base de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 73cc9d1d02ed915466f0eac2d9a42fe8cf9e2fb9
ms.openlocfilehash: 32d0a1fd8671bbd9d450711dd686ca49c2178c16


---
# <a name="overview-management-tools-for-sql-database"></a>Descrição geral: ferramentas de gestão para a Base de Dados SQL
Este tópico explora e compara ferramentas e opções para gerir bases de dados SQL do Azure, para que possa escolher a ferramenta adequada para a tarefa, para o seu negócio e para si. Escolher a ferramenta adequada depende do número de bases de dados que gere, da tarefa e de quantas vezes uma tarefa é realizada.

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) é uma aplicação baseada na Web onde pode criar, atualizar e eliminar bases de dados e servidores lógicos e monitorizar a atividade da base de dados. Esta ferramenta é ótima se estiver a começar a trabalhar com o Azure, a gerir algumas bases de dados ou precisa de fazer algo rapidamente.

Para obter mais informações acerca da utilização do portal, consulte [Manage SQL Databases using the Azure portal (Gerir Bases de Dados SQL com o portal do Azure)](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio e SQL Server Data Tools no Visual Studio
O SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) são ferramentas de cliente que são executadas no seu computador para gerir e desenvolver a sua base de dados na cloud. Se for um programador de aplicações familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), [experimente utilizar o SSDT no Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vários administradores de bases de dados estão familiarizados com o SSMS, que pode ser utilizado com bases de dados SQL do Azure. [Transfira a versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290) e utilize sempre a versão mais recente ao trabalhar com a Base de Dados SQL do Azure. Para obter mais informações sobre a gestão de Bases de Dados SQL do Azure com SSMS, consulte [Manage SQL Databases using SSMS (Gerir Bases de Dados SQL com o SSMS)](sql-database-manage-azure-ssms.md).

> [!IMPORTANT]
> Utilize sempre a versão mais recente do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) e do [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx), para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL.
>  

## <a name="powershell"></a>PowerShell
Pode utilizar o PowerShell para gerir bases de dados e conjuntos elásticos, e para automatizar as implementações de recursos do Azure. A Microsoft recomenda esta ferramenta para gerir um grande número de bases de dados e automatizar a implementação e alterações de recursos num ambiente de produção.

Para obter mais informações, consulte [Manage SQL Database with PowerShell (Gerir Bases de Dados SQL com o PowerShell)](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Ferramentas de Bases de Dados Elásticas
Utilizar as ferramentas de bases de dados elásticas para efetuar ações como 

* Executar um script de T-SQL com um conjunto de bases de dados com uma [tarefa elástica](sql-database-elastic-jobs-overview.md)
* Mover bases de dados do modelo do multi-inquilino para um modelo do inquilino único com a [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md)
* Gerir bases de dados num modelo do inquilino único ou num modelo do multi-inquilino com a [biblioteca de cliente de escala elástica](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Recursos adicionais
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Automatização do Azure](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Dec16_HO5-->


