---
title: Gerir Base de Dados SQL do Azure com o Portal do Azure | Microsoft Docs
description: "Referência rápida sobre como utilizar o Portal do Azure para gerir uma base de dados relacional na cloud através do Portal do Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3a56e9de-c21a-40ba-9a35-958172cb4e5b
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.date: 09/19/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 420b2153f6115dd712d3033e30f11f79b18cd80f
ms.openlocfilehash: be89a2799af3bdc2938f73e3d54f00f81d9ab9cd


---
# <a name="manage-azure-sql-databases-using-the-azure-portal"></a>Gerir Bases de Dados SQL do Azure através do portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

O [portal do Azure](https://portal.azure.com/) permite-lhe criar, monitorizar e gerir servidores e bases de dados SQL do Azure. Este artigo fornece uma descrição rápida e ligações para os detalhes das tarefas mais comuns.

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar uma firewall baseada no servidor, ver propriedades do servidor, ligar com o SQL Server Management Studio, consultar a base de dados mestra, criar uma base de dados de exemplo e uma base de dados em branco, consultar as propriedades da base de dados, ligar com o SQL Server Management Studio e consultar a base de dados de exemplo, veja [Tutorial de Introdução](sql-database-get-started.md).

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Ver as bases de dados SQL do Azure, servidores e conjuntos
Para ver os serviços da Base de Dados SQL disponíveis, clique em **Mais serviços** e escreva **SQL** na caixa de pesquisa:

![Base de Dados SQL](./media/sql-database-manage-portal/sql-services.png)

## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Como criar ou ver bases de dados SQL do Azure?
Para abrir o painel **Bases de dados SQL**, clique em **Bases de Dados SQL**e, em seguida, clique na base de dados com que pretende trabalhar ou clique em **+Adicionar** para criar uma base de dados SQL. Para obter detalhes, consulte [Create a SQL database in minutes by using the Azure portal (Criar uma base de dados SQL em minutos utilizando o portal do Azure)](sql-database-get-started.md).

![Bases de dados SQL](./media/sql-database-manage-portal/sql-databases.png)

## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Como criar ou ver servidores SQL do Azure?
Para abrir o painel **servidores SQL**, clique em **Bases de Dados SQL**e, em seguida, clique no servidor com que pretende trabalhar ou clique em **+Adicionar** para criar um servidor SQL. Para obter detalhes, consulte [Create a SQL database in minutes by using the Azure portal (Criar uma base de dados SQL em minutos utilizando o portal do Azure)](sql-database-get-started.md).

![Servidores SQL](./media/sql-database-manage-portal/sql-servers.png)

## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Como criar ou ver conjuntos elásticos do SQL?
Para abrir o painel **conjuntos elásticos SQL**, clique em **conjuntos elásticos SQL**e, em seguida, clique no conjunto com que pretende trabalhar ou clique em **+Adicionar** para criar um conjunto. Para obter detalhes, consulte [Create an elastic pool with the Azure portal (Criar um conjunto elástico com o portal do Azure)](sql-database-elastic-pool-create-portal.md).

![Conjuntos elásticos SQL](./media/sql-database-manage-portal/elastic-pools.png)

## <a name="how-do-i-update-or-view-sql-database-settings"></a>Como atualizar ou ver definições de base de dados SQL?
Para ver ou atualizar as definições de base de dados, clique na definição pretendida no painel da base de dados SQL:

![Definições da base de dados SQL](./media/sql-database-manage-portal/settings.png)

## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Como encontrar um nome de servidor totalmente qualificado de bases de dados SQL?
Para ver o seu nome de servidor de bases de dados, clique em **Descrição geral** no painel **Base de dados SQL** e tenha em atenção o nome do servidor:

![Definições da base de dados SQL](./media/sql-database-manage-portal/server-name.png)

## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Como gerir as regras de firewall para controlar o acesso ao meu servidor SQL e à base de dados?
Para ver, criar, ou atualizar regras de firewall, clique em **Configurar firewall do servidor** no painel **Base de dados SQL**. Para obter detalhes, consulte [Configure an Azure SQL Database server-level firewall rule using the Azure portal (Configurar uma regra de firewall ao nível do servidor da Base de Dados SQL do Azure com o portal do Azure)](sql-database-configure-firewall-settings.md).

![regras de firewall](./media/sql-database-manage-portal/sql-database-firewall.png)

## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Como posso alterar o meu escalão de serviço ou o nível de desempenho da base de dados SQL?
Para atualizar o nível de desempenho ou o escalão de serviço de uma base de dados SQL, clique em **Escalão de preço (DTUs de escala)** no painel **Base de dados SQL**. Para obter mais detalhes, consulte [Change the service tier and performance level (pricing tier) of a SQL database (Alterar o escalão de serviço e o nível de desempenho (escalão de preço) de uma base de dados SQL)](sql-database-scale-up.md).

![escalões de preço](./media/sql-database-manage-portal/pricing-tier.png)

## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Como configurar a auditoria e deteção de ameaças de uma base de dados SQL?
Para configurar a auditoria e a deteção de ameaças de uma base de dados SQL, clique em **Auditoria e Deteção de ameaças** no painel **Base de dados SQL**. Para obter detalhes, consulte [Get started with SQL database auditing (Introdução à auditoria da Base de Dados SQL)](sql-database-auditing-get-started.md) e [Get started with SQL Database Threat Detection (Introdução à Deteção de Ameaças da Base de Dados SQL)](sql-database-threat-detection-get-started.md).

## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Como configurar a máscara de dados dinâmicos para uma base de dados SQL?
Para configurar a máscara de dados dinâmicos para uma base de dados SQL, clique em **Máscara de dados dinâmicos** no painel **Base de dados SQL**. Para obter detalhes, consulte [Get started with SQL Database Dynamic Data Masking (Introdução à Máscara de Dados Dinâmicos da Base de Dados SQL)](sql-database-dynamic-data-masking-get-started.md).

## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Como configurar a encriptação de dados transparente (TDE) para uma base de dados SQL?
Para configurar a encriptação de dados transparente para uma base de dados SQL, clique em **Encriptação de dados transparente** no painel **Base de dados SQL**. Para obter detalhes, consulte [Enable TDE on a database using the porta (Ativar a TDE numa base de dados através do portal)](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Como ver ou alterar o tamanho máximo de uma base de dados SQL?
Para ver ou alterar o tamanho de uma base de dados SQL, clique em **Tamanho da base de dados** no painel **Base de dados SQL**. Atualize o tamanho máximo de uma base de dados ao alterar o escalão de serviço ou o nível de desempenho. Para obter mais detalhes, consulte [Change the service tier and performance level (pricing tier) of a SQL database (Alterar o escalão de serviço e o nível de desempenho (escalão de preço) de uma base de dados SQL)](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Como monitorizar e melhorar o desempenho de uma base de dados SQL?
Para monitorizar e melhorar as características de desempenho de uma base de dados SQL, clique em **Descrição geral do desempenho** no painel **Base de dados SQL**. Para obter detalhes, consulte [SQL Database Performance Insight (Informações de desempenho da base de dados SQL)](sql-database-performance.md).

## <a name="how-do-i-configure-geo-replication"></a>Como configurar a Georreplicação?
Para configurar a Georreplicação para uma base de dados SQL, clique em **Georreplicação** no painel **Base de dados SQL**. Para obter detalhes, consulte [Configure Geo-Replication for Azure SQL Database with the Azure portal (Configurar a Georreplicação para a Base de Dados SQL do Azure com o portal do Azure)](sql-database-geo-replication-portal.md).

## <a name="how-do-i-fail-over-to-a-geo-replicated-sql-database"></a>Como posso efetuar a ativação pós-falha numa base de dados SQL georreplicada?
Para efetuar a ativação pós-falha de uma georreplicação secundária, clique em **Georreplicação** no painel **Base de dados SQL** e, em seguida, clique em **Ativação pós-falha**. Para obter detalhes, consulte [Initiate a planned or unplanned failover for Azure SQL Database with the Azure portal (Iniciar uma ativação pós-falha planeada ou não planeada para a Base de Dados SQL do Azure com o portal do Azure)](sql-database-geo-replication-failover-portal.md)

## <a name="how-do-i-copy-a-sql-database"></a>Como copiar uma base de dados SQL?
Para copiar uma base de dados SQL, clique em **Copiar** no painel **Base de dados SQL**. Para obter detalhes, consulte[Copy an Azure SQL database using the Azure portal (Copiar uma base de dados SQL do Azure através do portal do Azure)](sql-database-copy-portal.md).

![Definições da base de dados SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Como posso arquivar uma base de dados SQL do Azure para um ficheiro BACPAC?
Para criar um BACPAC de uma base de dados SQL, clique em **Exportar** no painel **Base de dados SQL**. Para obter detalhes, consulte [Archive an Azure SQL database to a BACPAC file using the Azure portal (Arquivar uma base de dados SQL do Azure para um ficheiro BACPAC, com o portal do Azure)](sql-database-export.md).

![Exportação da base de dados SQL](./media/sql-database-manage-portal/sql-database-export.png)

## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Como restaurar uma base de dados SQL para um ponto anterior no tempo?
Para restaurar uma base de dados SQL, clique em **Restaurar** no painel **Base de dados SQL**. Para obter detalhes, consulte [Restore an Azure SQL Database to a previous point in time with the Azure portal (Restaurar uma Base de Dados SQL do Azure para um ponto anterior no tempo, com o portal do Azure)](sql-database-point-in-time-restore.md).

![Definições da base de dados SQL](./media/sql-database-manage-portal/sql-database-restore.png)

## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Como posso criar uma base de dados SQL do Azure a partir de um ficheiro BACPAC?
Para criar uma base de dados SQL a partir de um ficheiro BACPAC, clique em **Importar base de dados** no painel **Servidor SQL**. Para obter detalhes, consulte [Importar um ficheiro BACPAC para criar uma base de dados SQL do Azure](sql-database-import.md).

![Servidor SQL](./media/sql-database-manage-portal/server-commands.png)

## <a name="how-do-i-restore-a-deleted-sql-database"></a>Como restaurar uma base de dados SQL eliminada?
Para restaurar uma base de dados SQL eliminada, clique em **Bases de dados eliminadas** no painel **Servidor SQL** (o servidor SQL que continha a base de dados contida que foi eliminada). Para obter detalhes, consulte [Restore a deleted Azure SQL database using the Azure portal (Restaurar uma base de dados SQL do Azure eliminada com o portal do Azure)](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Como eliminar uma base de dados SQL?
Para eliminar uma base de dados SQL, clique em **Eliminar** no painel **Base de dados SQL**. 

![Definições da base de dados SQL](./media/sql-database-manage-portal/sql-database-delete.png)

## <a name="additional-resources"></a>Recursos adicionais
* [Base de Dados SQL](sql-database-technical-overview.md)
* [Monitorizar e gerir um conjunto elástico com o portal do Azure](sql-database-elastic-pool-manage-portal.md)




<!--HONumber=Dec16_HO5-->


