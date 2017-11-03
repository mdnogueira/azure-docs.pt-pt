---
title: "Gerir bases de dados do SQL do Azure através da automatização do Azure | Microsoft Docs"
description: "Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir bases de dados SQL do Azure à escala."
services: sql-database, automation
documentationcenter: 
author: jodoglevy
manager: jhubbard
editor: monicar
ms.assetid: 77c262a1-9b93-456d-b3c7-b2f23bdfcd61
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jhubbard
ms.openlocfilehash: c5f7e6da09c6ca8ddc6cc3ddcbcf7c5b53116e26
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Gerir bases de dados do SQL do Azure através da automatização do Azure
Este guia apresenta-lhe o serviço de automatização do Azure e como podem ser utilizada para simplificar a gestão das bases de dados SQL do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A automatização do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar a gestão de nuvem através de automatização de processos. Utilizar a automatização do Azure, as tarefas de longa execução, manuais, propensas ao erro e frequentemente repetidas podem ser automatizadas para aumentar a fiabilidade, a eficiência e a hora para o valor para a sua organização.

A automatização do Azure fornece um motor de execução do fluxo de trabalho altamente fiáveis e elevada disponibilidade preparada para satisfazer as suas necessidades, à medida que aumenta a sua organização. Na automatização do Azure, os processos podem ser arrancou manualmente, por sistemas de terceiros 3rd ou em intervalos agendados para que as tarefas acontecer exatamente quando necessário.

Reduzir a sobrecarga operacional e libertar IT / equipa de DevOps focar-se no trabalho que adiciona o negócio value ao mover as tarefas de gestão de nuvem a ser executada automaticamente pela automatização do Azure.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Como pode que o automatização do Azure ajuda a gerir bases de dados SQL do Azure?
Base de dados SQL do Azure podem ser gerido na automatização do Azure utilizando o [cmdlets do PowerShell de base de dados do SQL Azure](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/) que estão disponíveis no [ferramentas do Azure PowerShell](/powershell/azure/overview). A automatização do Azure tem estes cmdlets PowerShell de base de dados SQL do Azure disponíveis Box, para que possam executar todas as tarefas de gestão de BD do SQL no âmbito do serviço. Também pode ser emparelhado estes cmdlets na automatização do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em todos os serviços do Azure e 3rd sistemas de terceiros.

A automatização do Azure também tem a capacidade de comunicar com servidores SQL diretamente, ao emitir comandos SQL com o PowerShell.

O [Galeria de runbooks de automatização do Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks de Comunidade e a equipa de produto para começar a automatizar a gestão de bases de dados do Azure SQL, outros serviços do Azure e sistemas de terceiros 3rd. Galeria runbooks incluem:

* [Executar consultas do SQL Server em relação a uma base de dados do SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Aumentar verticalmente (ou reduzir verticalmente) uma base de dados do SQL do Azure com base numa agenda](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Truncar uma tabela SQL, se a sua base de dados se aproxima o tamanho máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Índice de tabelas numa base de dados SQL do Azure se estes são muito fragmentados](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas de automatização do Azure e como pode ser utilizado para gerir bases de dados SQL do Azure, siga estas ligações para saber mais sobre a automatização do Azure.

* [Descrição geral da automatização do Azure](../automation/automation-intro.md)
* [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizagem de automatização do Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [A automatização do Azure: O SQL Server Agent na nuvem](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

