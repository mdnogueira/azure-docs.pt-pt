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
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a1ede93b5aacf0d8a5bcf83f208f72be328ee72f
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-features"></a>Funcionalidades da Base de Dados SQL do Azure

As tabelas seguintes listam as funcionalidades principais da Base de Dados SQL do Azure e as funcionalidades equivalentes do SQL Server - e fornecem informações sobre se é suportada cada funcionalidade específica e uma ligação para obter mais informações sobre a funcionalidade de cada plataforma. Para as diferenças do Transact-SQL considerarem a migração de uma solução de base de dados existente, consulte [Resolving Transact-SQL differences during migration to SQL Database (Resolver diferenças do Transact-SQL durante a migração para a Base de Dados SQL)](sql-database-transact-sql-information.md).

Continuaremos a adicionar funcionalidades à Base de Dados SQL do Azure. Por isso, aconselhamo-lo a visitar a nossa página Web de Atualizações de Serviço do Azure e utilizar os respetivos filtros:

* Filtrado para o [serviço da Base de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado para [anúncios](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de Disponibilidade Geral (DG) para funcionalidades de Base de Dados SQL.

| **Funcionalidade** | **SQL Server** | **Base de Dados SQL do Azure** | 
| --- | :---: | :---: | 
| Georreplicação Ativa | Não suportado - consulte [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Suportado](sql-database-geo-replication-overview.md)
| Sempre Encriptado | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Suportado - consulte [Arquivo de cert](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md)|
| Grupos de Disponibilidade AlwaysOn | [Suportado](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Não suportado - consulte [Georreplicação Ativa](sql-database-geo-replication-overview.md) |
| Anexar uma base de dados | [Suportado](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Não suportado |
| Funções da aplicação | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Dimensionar automaticamente | Não suportado | Suportado - consulte [Escalões de serviços](sql-database-service-tiers.md) |
| Azure Active Directory | Não suportado | [Suportado](sql-database-aad-authentication.md) |
| Azure Data Factory | [Suportado](../data-factory/data-factory-introduction.md) | [Suportado](../data-factory/data-factory-introduction.md) |
| Auditoria | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Suportado](sql-database-auditing.md) |
| Ficheiro BACPAC (exportação) | [Suportado](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Suportado](sql-database-export.md) |
| Ficheiro BACPAC (importação) | [Suportado](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Suportado](sql-database-import.md) |
| CÓPIA DE SEGURANÇA | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Não suportado |
| Funções incorporadas | [Suportado](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - consulte [funções individuais] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Captura de dados de alteração | [Suportado](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Não suportado |
| Monitorização de alterações | [Suportado](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Suportado](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Instruções de agrupamento | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Índices Columnstore | [Suportado](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Apenas a edição Premium](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| CLR (Common Language Runtime) | [Suportado](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Não suportado |
| Bases de dados contidas | [Suportado](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Suportado](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Utilizadores contidos | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Suportado](sql-database-manage-logins.md#non-administrator-users) |
| Controlo de palavras-chave de linguagem de fluxo | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Consultas entre bases de dados | [Suportado](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Parcial - consulte [Consultas elásticas](sql-database-elastic-query-overview.md) |
| Cursores | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Compressão de dados | [Suportado](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Suportado](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Cópias de segurança de bases de dados | [Gerido pelo utilizador](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Gerido pelo serviço de Base de Dados SQL](sql-database-automated-backups.md) |
| Correio de base de dados | [Suportado](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Não suportado |
| Espelhamento da base de dados | [Suportado](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Não suportado |
| Definições de configuração de base de dados | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [Suportado](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Não suportado |
| Instantâneos da base de dados | [Suportado](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Não suportado |
| Tipos de dados | [Suportado](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Suportado](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| Declarações DBCC | [Suportado](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Maioria - consulte [declarações individuais](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| Declarações DDL | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/statements) | Maioria - consulte [Declarações individuais](https://docs.microsoft.com/sql/t-sql/statements/statements)
| Acionadores DDL | [Suportado](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Apenas base de dados](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Transações distribuídas | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Apenas cenários de intra Bases de Dados SQL |
| Declarações DML | [Suportado](https://docs.microsoft.com/sql/t-sql/queries/queries) | Consulte - mais [declarações individuais]] (https://docs.microsoft.com/sql/t-sql/queries/queries) |
| Acionadores DML | [Suportado](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Suportado](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMVs | [Todos](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Alguns - consulte [DMVs individuais](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| Conjuntos elásticos | Não suportado | [Suportado](sql-database-elastic-pool.md) |
| Tarefas elásticas | Não suportado - consulte [Agente do SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [Suportado](sql-database-elastic-jobs-getting-started.md) | 
| Consultas elásticas | Não suportado - consulte [Consultas entre bases de dados](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [Suportado](sql-database-elastic-query-overview.md) |
| Notificações de eventos | [Suportado](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Suportado](sql-database-insights-alerts-portal.md) |
| Expressões | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Eventos expandidos | [Suportado](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Alguns - consulte [Eventos individuais](sql-database-xevent-db-diff-from-svr.md) |
| Procedimentos armazenados expandidos | [Suportado](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Não suportado |
| Ficheiros e grupos de ficheiros | [Suportado](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Apenas principal](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Filestream | [Suportado](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Não suportado |
| Pesquisa em texto completo | [Suportado](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [Separadores de palavras de terceiros não suportados](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Funções | [Suportado](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - consulte [Funções individuais](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Otimização dentro da memória | [Suportado](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Apenas a edição Premium](sql-database-in-memory.md) |
| Tarefas | Consulte [Agente do SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Consulte [Tarefas elásticas](sql-database-elastic-jobs-getting-started.md) |
| Suporte de dados JSON | [Suportado](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Suportado](sql-database-json-features.md) |
| Elementos de linguagem | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Maioria - consulte [Elementos individuais](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| Servidores ligados | [Suportado](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não suportado - consulte [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| Envio de registos | [Suportado](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Não suportado - consulte [Georreplicação Ativa](sql-database-geo-replication-overview.md) |
| Master Data Services (MDS) | [Suportado](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Não suportado |
| Registo mínimo na importação em massa | [Suportado](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Não suportado |
| Modificar dados do sistema | [Suportado](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Não suportado |
| Operações de índice online | [Suportado](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Suportado - tamanho da transação limitado pelo escalão de serviço](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Operadores | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Maioria - consulte [Operadores individuais](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| Restauro de base de dados para um ponto no tempo | [Suportado](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Suportado](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Suportado](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Não suportado
| Gestão baseada na política | [Suportado](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Não suportado |
| Predicados | [Suportado](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Maioria - consulte [Predicados individuais](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| Serviços R | [Suportado](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Governador de recursos | [Suportado](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Não suportado |
| Instruções de RESTAURO | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Não suportado | 
| Restaurar base de dados a partir da cópia de segurança | [Suportado](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [Apenas a partir de cópias de segurança incorporadas](sql-database-recovery-using-backups.md) |
| Segurança a Nível da Linha | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Pesquisa semântica | [Suportado](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Não suportado |
| Números de sequência | [Suportado](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Suportado](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Mediador de Serviço | [Suportado](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Não suportado |
| Definições de configuração de servidor | [Suportado](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Não suportado - consulte [Opções de configuração de base de dados](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Definir instruções | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Maioria - consulte [Declarações individuais](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| Espacial | [Suportado](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Suportado](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| Agente do SQL Server | [Suportado](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Não suportado - consulte [Tarefas elásticas](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Suportado](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Não suportado - consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Suportado](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Não suportado - consulte [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| PowerShell do SQL Server | [Suportado](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Suportado](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| Gerador de perfis do SQL Server | [Suportado](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Não suportado - consulte [Eventos expandidos](sql-database-xevent-db-diff-from-svr.md) |
| Replicação do SQL Server | [Suportado](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Apenas subscritor de replicação de instantâneo e transacional](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Suportado](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Não suportado |
| Procedimentos armazenados | [Suportado](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Suportado](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| Funções armazenadas do sistema | [Suportado](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Alguns - consulte [Função individual](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| Procedimentos armazenados do sistema | [Suportado](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Alguns - consulte [Procedimento individual armazenado](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Tabelas de sistema | [Suportado](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Alguns - consulte [Tabela individual](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Vistas de catálogo de sistema | [Suportado](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Alguns - consulte [Vistas individuais](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| Criação de partições de tabela | [Suportado](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Suportado - [Apenas um grupo de ficheiros primário](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| Tabelas temporárias | [Local e global](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Só local](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Tabelas temporais | [Suportado](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Suportado](sql-database-temporal-tables.md) |
| Transações | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| Variáveis | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Suportado](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Encriptação de dados transparente (TDE)  | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Suportado](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Clustering de Ativação Pós-falha do Windows Server | [Suportado](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Não suportado - consulte [Georreplicação Ativa](sql-database-geo-replication-overview.md) |
| Índices XML | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Suportado](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o serviço Base de Dados SQL do Azure, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre o suporte e as diferenças do Transact-SQL, veja [Resolving Transact-SQL differences during migration to SQL Database (Resolver as diferenças do Transact-SQL durante a migração para a Base de Dados SQL)](sql-database-transact-sql-information.md).

