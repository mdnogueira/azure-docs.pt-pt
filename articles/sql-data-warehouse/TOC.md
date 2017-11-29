# Descrição geral

## [Acerca do SQL Data Warehouse](sql-data-warehouse-overview-what-is.md)

# Inícios rápidos

## [Criar e ligar - portal](create-data-warehouse-portal.md)

# Tutoriais
## [1 - Carregar dados do blob](load-data-from-azure-blob-storage-using-polybase.md)

# Conceitos
## Funcionalidades do serviço
### [Arquitetura de MPP](massively-parallel-processing-mpp-architecture.md)
### [Escalões de desempenho](performance-tiers.md)
### [Unidades do Data Warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Cópias de segurança do armazém de dados](sql-data-warehouse-backups.md)
### [Auditoria](sql-data-warehouse-auditing-overview.md)
### [Limites de capacidade](sql-data-warehouse-service-capacity-limits.md)
### [FAQ](sql-data-warehouse-overview-faq.md)

## Segurança
### [Descrição geral](sql-data-warehouse-overview-manage-security.md)
### [Autenticação](sql-data-warehouse-authentication.md)


## Migrar para o SQL Data Warehouse
### [Descrição geral](sql-data-warehouse-overview-migrate.md)
### [Utilitário de Migração](sql-data-warehouse-migrate-migration-utility.md)
### [Migrar esquema](sql-data-warehouse-migrate-schema.md)
### [Migrar código](sql-data-warehouse-migrate-code.md)
### [Migrar dados](sql-data-warehouse-migrate-data.md)

## Carregar e mover dados
### [Descrição geral](sql-data-warehouse-overview-load.md)
### [PolyBase](sql-data-warehouse-load-polybase-guide.md)


## Integrar
### [Descrição geral](sql-data-warehouse-overview-integrate.md)


## Monitorizar e ajustar
### [Orientações](resource-classes-for-workload-management.md)
### [Compressão Columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Monitorizar](sql-data-warehouse-manage-monitor.md)
### [Resolução de problemas](sql-data-warehouse-troubleshoot.md)

## Desenvolver armazéns de dados
### [Descrição geral](sql-data-warehouse-overview-develop.md)
### [Componentes do armazém de dados](sql-data-warehouse-overview-workload.md)

### Tabelas
#### [Descrição geral](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Tipos de dados](sql-data-warehouse-tables-data-types.md)
#### [Tabelas distribuídas](sql-data-warehouse-tables-distribute.md)
#### [Índices](sql-data-warehouse-tables-index.md)
#### [Identidade](sql-data-warehouse-tables-identity.md)
#### [Partições](sql-data-warehouse-tables-partition.md)
#### [Tabelas replicadas](design-guidance-for-replicated-tables.md)
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

# Guias de procedimentos
## Funcionalidades do serviço
### [Restaurar um armazém de dados - portal](sql-data-warehouse-restore-database-portal.md)
### [Restaurar um armazém de dados - PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [Restaurar um armazém de dados - API REST](sql-data-warehouse-restore-database-rest-api.md)

## Segurança
### [Ativar a encriptação - portal](sql-data-warehouse-encryption-tde.md)
### [Ativar a encriptação - T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Deteção de ameaças](sql-data-warehouse-security-threat-detection.md)


## Carregar e mover dados
### [AdventureWorks](sql-data-warehouse-load-sample-databases.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [PolyBase a partir do armazenamento de blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [PolyBase a partir do SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## Integrar
###  [Configurar a consulta elástica da Base de Dados SQL](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Adicionar uma tarefa do Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Configurar a consulta elástica](how-to-use-elastic-query-with-sql-data-warehouse.md)
### [Utilizar a aprendizagem automática](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Visualizar com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## Monitorizar e ajustar
### [Analisar a sua carga de trabalho](analyze-your-workload.md)

## Aumentar horizontalmente
### [Gerir a computação - portal](sql-data-warehouse-manage-compute-portal.md)
### [Gerir a computação - PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [Gerir a computação - API REST](sql-data-warehouse-manage-compute-rest-api.md)
### [Automatizar níveis de computação](manage-compute-with-azure-functions.md)


# Referência


## T-SQL
### [Referência completa](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Elementos de linguagem do SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Instruções do Armazém de Dados do SQL](sql-data-warehouse-reference-tsql-statements.md)
## [Vistas de sistema](sql-data-warehouse-reference-tsql-system-views.md)
## [Cmdlets do PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=databases)
## [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Preços](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Pedidos de funcionalidades](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Suporte](sql-data-warehouse-get-started-create-support-ticket.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Parceiros
### [Business intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Integração de dados](sql-data-warehouse-partner-data-integration.md)
### [Gestão de dados](sql-data-warehouse-partner-data-management.md)
