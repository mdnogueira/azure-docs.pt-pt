# Descrição geral
## [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
## Funcionalidades
### [Escalões de serviço](sql-database-service-tiers.md)
### [O que é uma DTU?](sql-database-what-is-a-dtu.md)
### [Descrição geral de referência da DTU](sql-database-benchmark-overview.md)
### [Firewall da Base de Dados SQL e regras da firewall](sql-database-firewall-configure.md)
### [Ferramentas de gestão](sql-database-manage-overview.md)
## Considerações e limitações
### [Base de Dados SQL em comparação com o SQL numa VM](sql-database-paas-vs-sql-server-iaas.md)
### [Diferenças do T-SQL](sql-database-transact-sql-information.md)
### [Limites de recursos](sql-database-resource-limits.md)
### [Limitações gerais](sql-database-general-limitations.md)
## [Preços](https://azure.microsoft.com/pricing/details/sql-database/)
## [Novidades](https://azure.microsoft.com/updates/?service=sql-database)
## [FAQ da Base de Dados SQL](sql-database-faq.md)

## Benefícios
### [Aprende e adapta-se](sql-database-learn-and-adapt.md)
### [Dimensiona-se sem perder tempo](sql-database-scale-on-the-fly.md)
### [Cria aplicações multi-inquilino](sql-database-build-multi-tenant-apps.md)
### [Protege e defende](sql-database-helps-secures-and-protects.md)
### [Funciona no seu ambiente](sql-database-works-in-your-environment.md)

## Cenários
### Criar e gerir servidores, conjuntos, bases de dados e as firewalls
#### Criar e gerir conjuntos de bases de dados elásticas
#### [Quando utilizar um conjunto de bases de dados elásticas](sql-database-elastic-pool-guidance.md)
#### [Diretrizes de segurança](sql-database-security-guidelines.md)
#### [Conjuntos de bases de dados elásticas](sql-database-elastic-pool.md)
#### [Automatização](sql-database-manage-automation.md)
#### Modificar os escalões de serviço e os níveis de desempenho
##### [[Portal](sql-database-scale-up.md) do Azure
##### [PowerShell](sql-database-scale-up-powershell.md)
### Criar e gerir bases de dados aumentadas horizontalmente
#### [Descrição geral](sql-database-elastic-scale-introduction.md)
#### [Criar bases de dados de nuvem dimensionáveis](sql-database-elastic-database-client-library.md)
#### [Aumentar horizontalmente com o gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
#### [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
#### [Mover dados entre bases de dados de nuvem aumentadas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)
#### [Utilizar o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Trabalhar com o Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-perf-counters.md)
#### Tarefas e consultas em várias bases de dados
##### [Consultas em várias bases de dados](sql-database-elastic-query-overview.md)
##### [Consultas em várias bases de dados com diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md)
##### [Relatórios em várias bases de dados](sql-database-elastic-query-horizontal-partitioning.md)
##### [Tarefas em várias bases de dados](sql-database-elastic-jobs-overview.md)
##### [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
##### [Consultas em várias partições horizontais](sql-database-elastic-scale-multishard-querying.md)
##### [Segurança ao nível de linha multi-inquilino](sql-database-elastic-tools-multi-tenant-row-level-security.md)
##### [Transações distribuídas entre bases de dados de nuvem](sql-database-elastic-transactions-overview.md)
####[Glossário das ferramentas das bases de dados elásticas](sql-database-elastic-scale-glossary.md)
#### [FAQ](sql-database-elastic-scale-faq.md)
### Criar e gerir o acesso e permissões
#### [Descrição geral](sql-database-security.md)
#### [Diretrizes de segurança](sql-database-security-guidelines.md)
#### [Gerir inícios de sessão](sql-database-manage-logins.md)
#### [Centro de Segurança do Azure para a Base de Dados SQL do Azure](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
#### [Centro de Segurança do SQL](https://msdn.microsoft.com/library/azure/bb510589)
### [Programação de bases de dados e aplicações](sql-database-develop-overview.md)
### Migração de bases de dados
#### [Migrar uma bases de dados SQL Server](sql-database-cloud-migrate.md)
### Proporcionar a continuidade de negócio
#### [Descrição geral](sql-database-business-continuity.md)
#### [Cópias de segurança de bases de dados](sql-database-automated-backups.md) 
#### [Recuperação de bases de dados com a utilização de cópias de segurança](sql-database-recovery-using-backups.md)
#### [Recuperar a partir de uma falha do datacenter](sql-database-disaster-recovery.md)
#### [Requisitos de autenticação para a recuperação após desastre](sql-database-geo-replication-security-config.md)
#### [Cenários de design de continuidade de negócio](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estratégias de recuperação após desastre com os conjuntos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Implementar atualizações](sql-database-manage-application-rolling-upgrade.md)
### Monitorizar e otimizar bases de dados
#### [Bases de dados individuais](sql-database-single-database-monitor.md)
#### [Orientações para bases de dados individuais](sql-database-performance-guidance.md)
#### [Informações de cargas de trabalho no portal do Azure](sql-database-performance.md)

## Implementações do cliente
### [Daxko/CSI Software](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)

# Introdução
## Criar servidores, conjuntos, bases de dados e firewalls
### [Portal do Azure](sql-database-get-started.md)
### [PowerShell](sql-database-get-started-powershell.md)
### [C#](sql-database-get-started-csharp.md)
## Consultar dados
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
## Gerir servidores, conjuntos, bases de dados e firewalls
### [Portal do Azure](sql-database-manage-portal.md)
### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### [PowerShell](sql-database-manage-powershell.md)
## [Criar e gerir o acesso e permissões](sql-database-get-started-security.md)
## Proteger e defender dados
### [Auditoria](sql-database-auditing-get-started.md)
### [Deteção de ameaças](sql-database-threat-detection-get-started.md)
### Máscara de dados dinâmica
#### [Portal do Azure](sql-database-dynamic-data-masking-get-started-portal.md)
## Criar e gerir bases de dados aumentadas horizontalmente
### [Dimensionamento elástico](sql-database-elastic-scale-get-started.md)
### [Tarefas elásticas](sql-database-elastic-jobs-getting-started.md)
### Consultas elásticas
### [Relatórios em várias bases de dados](sql-database-elastic-query-getting-started.md)
### [Consultas em várias bases de dados](sql-database-elastic-query-getting-started-vertical.md)
## [Otimização dentro da memória](sql-database-in-memory.md)
## [Mover bases de dados](sql-database-troubleshoot-moving-data.md)
## [Sincronização de dados](sql-database-get-started-sql-data-sync.md)
##Monitorizar e otimizar bases de dados
### [Descrição geral do Assistente da Base de Dados SQL](sql-database-advisor.md)
### [Informações de cargas de trabalho no portal do Azure](sql-database-performance.md)
## [Inícios rápidos das soluções](sql-database-solution-quick-starts.md)
# Procedimento
## [Auditoria](sql-database-auditing-get-started.md)
## Criar
### Grupo de recursos
#### [PowerShell](sql-database-manage-powershell.md#create-a-resource-group)
### Servidores
#### [Portal do Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-server)
### Conjuntos de bases de dados elásticas
#### [Portal do Azure](sql-database-elastic-pool-create-portal.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
### Bases de Dados
#### Bases de dados individuais
##### [Portal do Azure](sql-database-get-started.md)
##### [C#](sql-database-get-started-csharp.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-get-started-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
#### Bases de dados em partição horizontal
##### [Utilizar o gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
##### [Dividir e intercalar a configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Trabalhar com o Dapper](sql-database-elastic-scale-working-with-dapper.md)
##### [Utilizar o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
##### [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
##### [Segurança ao nível de linha multi-inquilino](sql-database-elastic-tools-multi-tenant-row-level-security.md)
### Regras da firewall
#### Servidor
##### [Portal do Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [API REST](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Base de Dados
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Tarefas
#### [Instalação do serviço](sql-database-elastic-jobs-service-installation.md)
#### [Portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
### Inícios de sessão
#### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-logins.md)
## Programar
### [Descrição geral](https://msdn.microsoft.com/library/mt763826.aspx)
### Cenários
#### [Aplicações SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### Tabelas temporais
##### [Tabelas temporais](sql-database-temporal-tables.md)
##### [Políticas de retenção](sql-database-temporal-tables-retention-policy.md)
#### [Dados JSON](sql-database-json-features.md)
#### [Dentro da memória](sql-database-in-memory.md)
###Introdução
#### [Bibliotecas de conectividade](sql-database-libraries.md)
#### Ligar uma aplicação
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Ligar ao Visual Studio](sql-database-connect-query.md)
### Procedimento
#### Criar servidores
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Criar conjuntos elásticos
###### [PowerShell](sql-database-elastic-pool-create-powershell.md)
###### [C#](sql-database-elastic-pool-create-csharp.md)
#### Criar bases de dados
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Criar regras da firewall
###### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
###### [API REST](sql-database-configure-firewall-settings-rest.md)
#### Gerir servidores, conjuntos, bases de dados e firewalls
##### [PowerShell](sql-database-manage-powershell.md)
##### Gerir conjuntos elásticos
###### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
###### [C#](sql-database-elastic-pool-manage-csharp.md)
##### [Alterar os escalões de serviço e os níveis de desempenho](sql-database-scale-up-powershell.md)
#### Mover dados
##### [Exportar a base de dados para um ficheiro BACPAC](sql-database-export-powershell.md)
##### [Importar a base de dados de um ficheiro BACPAC](sql-database-import-powershell.md)
##### [Copiar uma base de dados para outra localização do Azure](sql-database-copy-powershell.md)
#### [Obter os valores necessários para autenticar uma aplicação](sql-database-client-id-keys.md)
#### [Tarefas elásticas](sql-database-elastic-jobs-powershell.md)
#### Restaurar e recuperar uma base de dados
##### Restaurar uma base de dados eliminada
###### [PowerShell](sql-database-restore-deleted-database-powershell.md)
##### Restauro de base de dados para um ponto no tempo
###### [PowerShell](sql-database-point-in-time-restore-powershell.md)
##### Restauro geográfico
###### [PowerShell](sql-database-geo-restore-powershell.md)
#### Replicar dados com a Georreplicação Ativa
##### Configurar
###### [PowerShell](sql-database-geo-replication-powershell.md)
##### Ativação pós-falha
###### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [Utilizar as portas para além da porta 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Trabalhar com as mensagens de erro](sql-database-develop-error-messages.md)
#### [Utilizar a criação de batches](sql-database-use-batching-to-improve-performance.md)
### Referência
#### [Transact-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
#### [Fornecedor de Dados .NET Framework para SQL Server (conceitos)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [Fornecedor de Dados .NET Framework para SQL Server (referência de API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### SQL PowerShell
##### [Cmdlets da Base de Dados SQL do Azure (Gestão de Recursos)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
##### [Cmdlets da Base de Dados SQL do Azure (Gestão de Serviços)](https://msdn.microsoft.com/library/azure/dn546723(v=azure.300\).aspx)
##### [Cmdlets do SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
#### API REST da Base de Dados SQL
##### [API REST (Gestão de Recursos)](https://msdn.microsoft.com/library/azure/mt420159)
##### [API REST (Gestão de Serviços)](https://msdn.microsoft.com/library/azure/dn505719.aspx)
#### Biblioteca de Gestão de Bases de Dados SQL
##### [Referência da Biblioteca de Gestão de Bases de Dados SQL](https://msdn.microsoft.com/library/azure/mt349017.aspx)
##### [Obter o pacote de Biblioteca de Gestão de Bases de Dados SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
#### Entity Framework
##### [Obter o pacote do Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [Controladores do SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
##### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
##### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
##### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
##### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
##### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
##### [python](https://msdn.microsoft.com/library/mt652092.aspx)
##### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
#### [Azure SDK (transferência)](https://www.visualstudio.com/vs/azure-tools/)
#### [Azure SDK (documentação)](https://azure.microsoft.com/documentation/articles/dotnet-sdk/)
### Recursos
#### [Ferramentas do SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
#### [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
#### [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
#### [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
#### [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
#### [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## Eliminar
### Base de Dados
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database)
### Servidor
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database-server)
## Detetar ameaças
### [Deteção de ameaças](sql-database-threat-detection-get-started.md)
### [Firewall](sql-database-firewall-configure.md)
## Encriptar dados
### Always Encrypted
#### [Descrição Geral de Always Encrypted](sql-database-always-encrypted.md)
#### [Cofre de chaves do Azure Always Encrypted](sql-database-always-encrypted-azure-key-vault.md)
### [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/azure/dn948096)
### [Encriptação de colunas](https://msdn.microsoft.com/library/azure/ms179331)
## Gerir
###  Autenticação
#### Autenticação do SQL
#### [Autenticação do Azure Active Directory](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Servidores
### Conjuntos elásticos
#### [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### Bases de Dados
#### Bases de dados individuais
##### [Portal do Azure](sql-database-manage-portal.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-blank)
#### Alterar os escalões de serviço e os níveis de desempenho
#### [[Portal](sql-database-scale-up.md) do Azure
#### [PowerShell](sql-database-manage-powershell.md#change-the-performance-level-of-a-sql-database)
#### Bases de dados em partição horizontal
##### [Migrar bases de dados aumentadas horizontalmente existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Gerir credenciais](sql-database-elastic-scale-manage-credentials.md)
##### [Mover dados entre bases de dados de nuvem aumentadas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)
##### [Implementar um serviço de divisão/intercalação](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
##### [Adicionar uma partição horizontal](sql-database-elastic-scale-add-a-shard.md)
##### [Utilizar a classe RecoveryManager para corrigir problemas do mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md)
### Regras da firewall
#### Servidor
##### [Portal do Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [API REST](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Base de Dados
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Tarefas
#### [Instalação do serviço](sql-database-elastic-jobs-service-installation.md)
#### [Portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Atualizar a biblioteca de clientes](sql-database-elastic-scale-upgrade-client-library.md)
### Inícios de sessão
#### [Gerir inícios de sessão](sql-database-manage-logins.md)
## Mascarar dados
### Máscara de dados dinâmica
#### [Portal do Azure](sql-database-dynamic-data-masking-get-started-portal.md)
#### [Clientes de nível inferior](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
## Migrar
### Determinar a compatibilidade
#### [Utilitário Pacote de SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Corrigir problemas de compatibilidade
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Assistente de Migração do SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Utilizar o Assistente de Migração do SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Utilizar a replicação transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### Exportar a base de dados para um ficheiro BACPAC
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilitário Pacote de SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importar a base de dados de um ficheiro BACPAC
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilitário Pacote de SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Portal do Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)

## Monitorizar e otimizar
### [Bases de dados individuais](sql-database-performance-guidance.md)
### Conjuntos elásticos
#### [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Query Performance Insight](sql-database-query-performance.md)
### Assistente da Base de Dados SQL
#### [Portal do Azure](sql-database-advisor-portal.md)
### Alterar os escalões de serviço e os níveis de desempenho
#### [Portal do Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### [Sugestões de otimização do desempenho](sql-database-troubleshoot-performance.md)
### OLTP dentro da memória
#### [Adotar o OLTP dentro da memória](sql-database-in-memory-oltp-migration.md)
#### [Monitorizar o Armazenamento OLTP Dentro da Memória](sql-database-in-memory-oltp-monitoring.md)
### Arquivo de Consultas
#### [Utilizar o Arquivo de Consultas para monitorizar o desempenho](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Cenários de utilização do Arquivo de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Trabalhar com o Arquivo de Consultas](sql-database-operate-query-store.md)
### [Níveis de compatibilidade](sql-database-compatibility-level-query-performance-130.md)
### [Auditoria de eventos](sql-database-auditing-get-started.md)
### [Contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-perf-counters.md)
### Eventos expandidos
#### [Eventos expandidos](sql-database-xevent-db-diff-from-svr.md)
#### [Código de destino de ficheiro de evento](sql-database-xevent-code-event-file.md)
#### [Código de destino de memória intermédia em anel](sql-database-xevent-code-ring-buffer.md)
### DMVs
#### [DMVs](sql-database-monitoring-with-dmvs.md)
#### [DMVs](sql-database-manage-azure-ssms#monitor-sql-database-using-dynamic-management-views


## Mover dados
### Copiar uma base de dados SQL
#### [Descrição geral](sql-database-copy.md)
#### [Portal do Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exportar a base de dados para um ficheiro BACPAC
#### [Portal do Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importar a base de dados de um ficheiro BACPAC
#### [Portal do Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
### [Sincronização de dados](sql-database-get-started-sql-data-sync.md)
### [Carregar a partir do ficheiro CSV com o BCP](sql-database-load-from-csv-with-bcp.md)

## Consulta
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Consultas em várias partições horizontais](sql-database-elastic-scale-multishard-querying.md)
### Consultas em várias bases de dados
#### [Consultas em várias bases de dados com diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md)
#### [Relatórios em várias bases de dados](sql-database-elastic-query-horizontal-partitioning.md)
#### [Transações distribuídas entre bases de dados de nuvem](sql-database-elastic-transactions-overview.md)
#### [Atualizar a biblioteca de clientes](sql-database-elastic-scale-upgrade-client-library.md)
#### [Consultas em várias partições horizontais](sql-database-elastic-scale-multishard-querying.md)

## Restauro
### Restaurar uma base de dados eliminada
### [Portal do Azure](sql-database-restore-deleted-database-portal.md)
### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Restauro para um ponto anterior no tempo
#### [Portal do Azure](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Restauro geográfico
#### [Portal do Azure](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Tabela individual](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Recuperar a partir de uma falha do datacenter](sql-database-disaster-recovery.md)
### [Realizar testes de recuperação após desastre](sql-database-disaster-recovery-drills.md)

## Replicar
### [Descrição geral da Georreplicação Ativa](sql-database-geo-replication-overview.md)
### Configurar
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Ativação pós-falha
#### [Portal do Azure](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Resolução de problemas
### Conectividade
#### [Problemas de ligação](sql-database-troubleshoot-common-connection-issues.md)
#### [Erro de ligação transitório](sql-database-troubleshoot-connection.md)
#### [Diagnosticar e prevenir](sql-database-connectivity-issues.md)
### [Permissões](sql-database-troubleshoot-permissions.md)

# Referência
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## SQL PowerShell
### [Cmdlets da Base de Dados SQL do Azure (Gestão de Recursos)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
### [Cmdlets do SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## API REST da Base de Dados SQL
### [API REST (Gestão de Recursos)](https://msdn.microsoft.com/library/azure/mt420159)
## Biblioteca de Gestão de Bases de Dados SQL
### [Referência da Biblioteca de Gestão de Bases de Dados SQL](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Obter o pacote de Biblioteca de Gestão de Bases de Dados SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Controladores do SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Recursos
## [Ferramentas do SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)

<!--HONumber=Nov16_HO2-->


