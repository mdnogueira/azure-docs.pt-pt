---
title: "Descrição Geral das Funcionalidades da Base de Dados SQL do Azure | Microsoft Docs"
description: "Esta página dispõe de uma descrição geral dos servidores lógicos e bases de dados de Base de Dados SQL do Azure, e inclui uma matriz de suporte da funcionalidade com ligações de cada funcionalidade listada."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/25/2017
ms.author: carlrab
ms.openlocfilehash: 1505b85a8828cd64045a982ece48e49d85ea3314
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-features"></a>Funcionalidades da Base de Dados SQL do Azure

Base de dados SQL do Azure partilha uma base de código comuns com o SQL Server e, ao nível da base de dados, suporta a maioria das mesmas funcionalidades. São as diferenças de funcionalidade principais entre SQL Database do Azure e SQL Server ao nível da instância. 

Continuaremos a adicionar funcionalidades à Base de Dados SQL do Azure. Por isso, aconselhamo-lo a visitar a nossa página Web de Atualizações de Serviço do Azure e utilizar os respetivos filtros:

* Filtrado para o [serviço da Base de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado para [anúncios](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de Disponibilidade Geral (DG) para funcionalidades de Base de Dados SQL.

## <a name="sql-server-and-sql-database-feature-support"></a>Suporte de funcionalidades do SQL Server e base de dados SQL

A tabela seguinte lista as principais funcionalidades do SQL Server e fornece informações sobre se cada funcionalidade específica é suportada e uma hiperligação para obter mais informações sobre a funcionalidade. Para as diferenças do Transact-SQL considerarem a migração de uma solução de base de dados existente, consulte [Resolving Transact-SQL differences during migration to SQL Database (Resolver diferenças do Transact-SQL durante a migração para a Base de Dados SQL)](sql-database-transact-sql-information.md).


| **Funcionalidade de servidor do SQL Server** | **Suportado na base de dados SQL do Azure** | 
| --- | --- |  
| [Sempre encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sim - consulte [arquivo de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md)|
| [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Elevada disponibilidade está incluída com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a SQL Database do Azure](sql-database-geo-replication-overview.md) |
| [Anexar uma base de dados](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Não |
| [Funções de aplicação](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Sim |
|[Auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Sim](sql-database-auditing.md)|
| [A otimização automática](/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sim](sql-database-automatic-tuning.md)|
| [Ficheiro BACPAC (exportação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sim - consulte [exportação de base de dados SQL](sql-database-export.md) |
| [Ficheiro BACPAC (importar)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sim - consulte [importar da base de dados SQL](sql-database-import.md) |
| [Comando de cópia de segurança](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Não - consulte [cópias de segurança automatizadas](sql-database-automated-backups.md) |
| [Funções incorporadas](https://docs.microsoft.com/sql/t-sql/functions/functions) | Consulte mais - funções individuais |
| [Captura de dados de alteração](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Não |
| [Registo de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Sim |
| [Instruções de agrupamento](https://docs.microsoft.com/sql/t-sql/statements/collations) | Sim |
| [Índices Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Sim - [apenas a edição Premium](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [Common language runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Não |
| [Bases de dados contidas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Sim |
| [Utilizadores contidos](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Sim |
| [Controlo de palavras-chave de idioma de fluxo](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Sim |
| [Consultas de base de dados em vários locais](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Parcial - consulte [Consultas elásticas](sql-database-elastic-query-overview.md) |
| [Cursores](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Sim | 
| [Compressão de dados](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Sim |
| [Correio de base de dados](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Não |
| [O espelhamento da base de dados](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Não |
| [Definições de configuração de base de dados](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Sim |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Não |
| [Instantâneos da base de dados](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Não |
| [Tipos de dados](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Sim |  
| [Instruções de DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Consulte mais - instruções individuais |
| [Instruções DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Sim |
| [Acionadores DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Apenas a base de dados |
| [Transações distribuídas - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Não - consulte [transações elásticas](sql-database-elastic-transactions-overview.md) |
| [Instruções de DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Sim |
| [Acionadores DML](https://docs.microsoft.com/en-us/sql/relational-databases/triggers/create-dml-triggers) | Consulte mais - instruções individuais | 
| [DMVs](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Algumas - Consulte DMVs individuais |
|[Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)|[Sim](sql-database-dynamic-data-masking-get-started.md)|
| [Notificações de eventos](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Não - consulte [alertas](sql-database-insights-alerts-portal.md) |
| [Expressões](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Sim |
| [Eventos expandidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Algumas - consulte [expandido eventos na base de dados SQL](sql-database-xevent-db-diff-from-svr.md) |
| [Procedimentos armazenados expandidos](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Não |
| [Ficheiros e grupos de ficheiros](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Grupo de ficheiros primário apenas |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Não |
| [Pesquisa em texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | Breakers palavras de terceiros não são suportados |
| [Funções](https://docs.microsoft.com/sql/t-sql/functions/functions) | Consulte mais - funções individuais |
| [Processamento de gráfico](/sql/relational-databases/graphs/sql-graph-overview) | Sim |
| [Otimização dentro da memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Sim - [apenas a edição Premium](sql-database-in-memory.md) |
| [Suporte de dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Sim |
| [Elementos de linguagem](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Consulte mais - elementos individuais |  
| [Servidores ligados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não - consulte [consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| [Envio de registo](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Elevada disponibilidade está incluída com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a SQL Database do Azure](sql-database-geo-replication-overview.md) |
| [Dados do Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Não |
| [Registo mínimo na importação em volume](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Não |
| [Modificar os dados de sistema](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Não |
| [Operações de índice online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Sim |
| [Operadores](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Consulte mais - operadores individuais |
| [Ponto de restauro de base de dados do tempo](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Sim - consulte [recuperação de base de dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Não |
| [Gestão baseada em políticas](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Não |
| [Predicados de](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Sim |
| [Serviços R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Versão de pré-visualização consulte [Novidades no machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [Governador de recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Não |
| [RESTAURAR instruções](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Não | 
| [Restaurar a base de dados da cópia de segurança](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | De incorporada cópias de segurança só - consulte [recuperação de base de dados SQL](sql-database-recovery-using-backups.md) |
| [Segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Sim |
| [Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Não |
| [Números de sequência](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Sim |
| [Mediador de serviço](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Não |
| [Definições de configuração do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Não |
| [Conjunto de instruções](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Consulte mais - instruções individuais 
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Sim |
| [Agente do SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Não - consulte [as tarefas elásticas](sql-database-elastic-jobs-getting-started.md) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Consulte [do Analysis Services do Azure](https://azure.microsoft.com/services/analysis-services/) |
| [Auditoria do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Não - consulte [auditoria de base de dados SQL](sql-database-auditing.md) |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Consulte [fábrica de dados do Azure](https://azure.microsoft.com/services/data-factory/) |
| [SQL Server do PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Sim |
| [O gerador de perfis do SQL Server](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Não - consulte [alargadas de eventos](sql-database-xevent-db-diff-from-svr.md) |
| [Replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Apenas subscritor de replicação de instantâneo e transacional](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Não |
| [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Sim |
| [Funções de sistema armazenados](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Algumas - Consulte funções individuais |
| [Procedimentos armazenados de sistema](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Algumas - Consulte individuais procedimentos armazenados |
| [Tabelas de sistema](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Algumas - Consulte tabelas individuais |
| [Vistas de catálogo de sistema](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Algumas - Consulte vistas individuais |
| [Criação de partições de tabela](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Sim - apenas uma grupo de ficheiros primário |
| [Tabelas temporárias](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | Locais e no âmbito da base de dados globais tabelas temporárias apenas |
| [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Sim |
| [Variáveis](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Sim | 
| [Encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Sim |
| [Windows Server Clustering de ativação pós-falha](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Elevada disponibilidade está incluída com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a SQL Database do Azure](sql-database-geo-replication-overview.md) |
| [Índices XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Sim |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o serviço Base de Dados SQL do Azure, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre o suporte e as diferenças do Transact-SQL, veja [Resolving Transact-SQL differences during migration to SQL Database (Resolver as diferenças do Transact-SQL durante a migração para a Base de Dados SQL)](sql-database-transact-sql-information.md).
