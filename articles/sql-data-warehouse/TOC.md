# Descrição geral

## [O que é o SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md)
## [Carga de trabalho do armazém de dados](sql-data-warehouse-overview-workload.md)
## [Dados distribuídos](sql-data-warehouse-distributed-data.md)
## [Perguntas mais frequentes](sql-data-warehouse-overview-faq.md)

# Introdução

## [Tutorial de principiante](sql-data-warehouse-get-started-tutorial.md)
## [Melhores práticas](sql-data-warehouse-best-practices.md)
## [Gerir](sql-data-warehouse-overview-manage.md)
## [Obter suporte](sql-data-warehouse-get-started-create-support-ticket.md)
## [Resolução de problemas](sql-data-warehouse-troubleshoot.md)

# Procedimento

## Cópia de segurança e restauro

### [Descrição Geral da Cópia de Segurança](sql-data-warehouse-backups.md)
### [Descrição Geral do Restauro](sql-data-warehouse-restore-database-overview.md)
#### [Portal do Azure](sql-data-warehouse-restore-database-portal.md)
#### [PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Ligar

### [Descrição geral](sql-data-warehouse-connect-overview.md)
### [Cadeias de ligação](sql-data-warehouse-connection-strings.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Instalar o Visual Studio](sql-data-warehouse-install-visual-studio.md)

## Criar
### [Portal do Azure](sql-data-warehouse-get-started-provision.md)
### [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## Programar

### [Descrição geral](sql-data-warehouse-overview-develop.md)

### Tabelas

#### [Descrição geral](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Tipos de dados](sql-data-warehouse-tables-data-types.md)
#### [Tabelas distribuídas](sql-data-warehouse-tables-distribute.md)
#### [Índices](sql-data-warehouse-tables-index.md)
#### [Partições](sql-data-warehouse-tables-partition.md)
#### [Estatísticas](sql-data-warehouse-tables-statistics.md)
#### [Temporário](sql-data-warehouse-tables-temporary.md)

### Consultas

#### [Dynamic SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [Agrupar por opções](sql-data-warehouse-develop-group-by-options.md)
#### [Etiquetas](sql-data-warehouse-develop-label.md)

### Elementos de linguagem do T-SQL

#### [Ciclos](sql-data-warehouse-develop-loops.md)
#### [Procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
#### [Transações](sql-data-warehouse-develop-transactions.md)
#### [Melhores práticas das transações](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Esquemas definidos pelo utilizador](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Atribuição de variáveis](sql-data-warehouse-develop-variable-assignment.md)
#### [Vistas](sql-data-warehouse-develop-views.md)

## Integrar

### [Descrição geral](sql-data-warehouse-overview-integrate.md)
### [Data Factory](sql-data-warehouse-integrate-azure-data-factory.md)
### [Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Tutorial do Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Visualização do Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)

## Carregar

### [Descrição geral](sql-data-warehouse-overview-load.md)
### [Dados de exemplo](sql-data-warehouse-load-sample-databases.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [AZCopy](sql-data-warehouse-load-from-sql-server-with-azcopy.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
### [Documentação de orientação de PolyBase](sql-data-warehouse-load-polybase-guide.md)
### [PolyBase a partir do armazenamento de blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [PolyBase a partir do SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Migrar

### [Descrição geral](sql-data-warehouse-overview-migrate.md)
### [Utilitário de Migração](sql-data-warehouse-migrate-migration-utility.md)
### [Migrar esquema](sql-data-warehouse-migrate-schema.md)
### [Migrar código](sql-data-warehouse-migrate-code.md)
### [Migrar dados](sql-data-warehouse-migrate-data.md)
### [Migrar para o Armazenamento Premium](sql-data-warehouse-migrate-to-premium-storage.md)

## Gerir a computação

### [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
### [Portal do Azure](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [API REST](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)

## Desempenho

### [Descrição geral](sql-data-warehouse-overview-manage-user-queries.md)
### [Compressão Columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Consultas](sql-data-warehouse-manage-monitor.md)
### [Carga de trabalho](sql-data-warehouse-develop-concurrency.md)

## Segurança

### [Descrição geral](sql-data-warehouse-overview-manage-security.md)
### [Auditoria](sql-data-warehouse-auditing-overview.md)
### [Auditoria para clientes de nível inferior](sql-data-warehouse-auditing-downlevel-clients.md)
### [Autenticação](sql-data-warehouse-authentication.md)
### [Encriptação](sql-data-warehouse-encryption-tde.md)
### [Encriptação com T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Deteção de ameaças](sql-data-warehouse-security-threat-detection.md)

# Referência

## [Limites de capacidade](sql-data-warehouse-service-capacity-limits.md)
## [Elementos de linguagem do T-SQL](sql-data-warehouse-reference-tsql-language-elements.md)
## [Declarações do T-SQL](sql-data-warehouse-reference-tsql-statements.md)
## [Vistas de sistema do T-SQL](sql-data-warehouse-reference-tsql-system-views.md)
## [Cmdlets do PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Parceiros
### [Business intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Integração de dados](sql-data-warehouse-partner-data-integration.md)
### [Gestão de dados](sql-data-warehouse-partner-data-management.md)
