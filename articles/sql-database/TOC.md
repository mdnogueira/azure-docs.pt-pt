# Descrição geral
## [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
### [Escalões de serviço](sql-database-service-tiers.md)
### [DTUs e eDTUs](sql-database-what-is-a-dtu.md)
### [Descrição geral de referência da DTU](sql-database-benchmark-overview.md)
### [Limites de recursos](sql-database-resource-limits.md)
### [Funcionalidades](sql-database-features.md)
### [FAQ da Base de Dados SQL](sql-database-faq.md)
## Comparações
### [Base de Dados SQL em comparação com o SQL numa VM](sql-database-paas-vs-sql-server-iaas.md)
### [Diferenças do T-SQL](sql-database-transact-sql-information.md)
### [SQL versus NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Ferramentas da Base de Dados SQL](sql-database-manage-overview.md)
## [Tutoriais da Base de Dados SQL](sql-database-explore-tutorials.md)
## Segurança
### [Descrição geral da segurança](sql-database-security-overview.md)
### [Centro de Segurança do Azure para a Base de Dados SQL do Azure](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [Centro de Segurança do SQL](https://msdn.microsoft.com/library/azure/bb510589)
# Introdução
## Bases de dados e servidores
### Saiba mais
#### [Servidores](sql-database-server-overview.md)
#### [Bases de dados individuais](sql-database-overview.md)
#### [Várias bases de dados](sql-database-elastic-scale-introduction.md)
##### Mapeamento de inquilinos
###### [Biblioteca de clientes elástica](sql-database-elastic-database-client-library.md)
###### [Gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
###### [Encaminhamento Dependente de Dados](sql-database-elastic-scale-data-dependent-routing.md)
###### [Gerir credenciais](sql-database-elastic-scale-manage-credentials.md)
###### [Consultas em várias partições horizontais](sql-database-elastic-scale-multishard-querying.md)
##### Conjuntos elásticos (agrupamentos de recursos)
###### [O que é um conjunto elástico?](sql-database-elastic-pool.md)
###### [Quando utilizar um conjunto elástico](sql-database-elastic-pool-guidance.md)
###### [Preços do conjunto elástico](sql-database-elastic-pool-price.md)
##### Bases de dados em partição horizontal
###### [Glossário das ferramentas elásticas](sql-database-elastic-scale-glossary.md)
###### [Mover dados entre partições horizontais](sql-database-elastic-scale-overview-split-and-merge.md)
###### [FAQ das ferramentas elásticas](sql-database-elastic-scale-faq.md)
##### Consulta elástica (consultas entre bases de dados)
###### [O que é uma consulta elástica?](sql-database-elastic-query-overview.md)
##### Transações elásticas (transações distribuídas)
###### [Transações entre bases de dados na cloud](sql-database-elastic-transactions-overview.md)
##### Tarefas elásticas (tarefas entre bases de dados)
###### [O que é uma tarefa elástica?](sql-database-elastic-jobs-overview.md)
#### [Utilizar o Azure RemoteApp para ligar à Base de Dados SQL](sql-database-ssms-remoteapp.md)
#### [Gerir Bases de Dados SQL com o Serviço de Automatização do Azure](sql-database-manage-automation.md)
### O que deve fazer
#### [Criar uma base de dados individual utilizando o portal do Azure](sql-database-get-started.md)
#### [Criar uma base de dados individual utilizando o PowerShell](sql-database-get-started-powershell.md)
#### [Criar uma base de dados individual utilizando o C#](sql-database-get-started-csharp.md)
#### [Criar aplicação em partição horizontal](sql-database-elastic-scale-get-started.md)
#### [Mover dados entre partições horizontais](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Introdução às tarefas elásticas](sql-database-elastic-jobs-getting-started.md)
#### [Introdução às consultas elásticas](sql-database-elastic-query-getting-started-vertical.md)
#### [Criar relatórios com uma consulta elástica](sql-database-elastic-query-getting-started.md)
#### [Consultar bases de dados com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md)
#### [Relatórios entre bases de dados de aumento horizontal](sql-database-elastic-query-horizontal-partitioning.md)
## Migrar e mover dados
### Saiba mais
#### [Migrar uma base de dados](sql-database-cloud-migrate.md)
#### [Replicação transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronização de dados](sql-database-get-started-sql-data-sync.md)
#### [Copiar uma base de dados SQL](sql-database-copy.md)
## Regras, autenticação e autorização de Firewall
### Saiba mais
#### [Controlo de acesso](sql-database-control-access.md)
#### [Firewall](sql-database-firewall-configure.md)
#### [Gerir inícios de sessão](sql-database-manage-logins.md)
### O que deve fazer
#### [Autenticação e autorização SQL](sql-database-control-access-sql-authentication-get-started.md)
#### [Autenticação e autorização do Azure AD](sql-database-control-access-aad-authentication-get-started.md)
## Proteger e defender dados
### Saiba mais
#### Auditoria
##### [Auditoria](sql-database-auditing-get-started.md)
##### [Suporte para clientes de nível inferior e alterações de ponto final de IP para auditoria](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Deteção de ameaças](sql-database-threat-detection-get-started.md)
#### Encriptar dados
##### [Cofre de chaves do Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/azure/dn948096)
##### [Encriptação de colunas](https://msdn.microsoft.com/library/azure/ms179331)
#### Mascarar dados
##### Máscara de dados dinâmica
###### [Portal do Azure](sql-database-dynamic-data-masking-get-started.md)
### O que deve fazer
#### [Máscara de dados dinâmica com o portal do Azure](sql-database-dynamic-data-masking-get-started.md)
##### [Sempre Encriptado utilizando o arquivo de certificados do Windows](sql-database-always-encrypted.md)
## Continuidade do negócio
### Saiba mais
#### [Descrição geral](sql-database-business-continuity.md)
#### [Cópias de segurança de bases de dados](sql-database-automated-backups.md)
#### [Retenção de longa duração](sql-database-long-term-retention.md)
#### [Recuperação de bases de dados com a utilização de cópias de segurança](sql-database-recovery-using-backups.md)
#### [Recuperar uma tabela simples](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Recuperar a partir de uma falha do datacenter](sql-database-disaster-recovery.md)
#### [Requisitos de autenticação para a recuperação após desastre](sql-database-geo-replication-security-config.md)
#### [Cenários de design de continuidade de negócio](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estratégias de recuperação após desastre com os conjuntos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Implementar atualizações](sql-database-manage-application-rolling-upgrade.md)
#### [Realizar testes de recuperação após desastre](sql-database-disaster-recovery-drills.md)
#### [Descrição geral da Georreplicação Ativa](sql-database-geo-replication-overview.md)
### O que deve fazer
#### [Portal do Azure: cópia de segurança e restauro](sql-database-get-started-backup-recovery.md)
#### [PowerShell: automatizar cópia de segurança e restauro](sql-database-get-started-backup-recovery-powershell.md)
## Programação de aplicações
### Saiba mais
#### [Descrição geral da programação de aplicações de bases de dados](sql-database-develop-overview.md)
#### [Bibliotecas de conectividade](sql-database-libraries.md)
#### [Aplicações SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Dimensionamento de aplicações SaaS multi-inquilino com segurança ao nível da linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Utilizar as portas para além da porta 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Obter os valores necessários para autenticar uma aplicação](sql-database-client-id-keys.md)
### O que deve fazer
#### Ligar uma aplicação
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C e C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Ligar ao Visual Studio](sql-database-connect-query.md)
#### [Criar uma aplicação cliente](https://www.microsoft.com/sql-server/developer-get-started)
#### [Trabalhar com as mensagens de erro](sql-database-develop-error-messages.md)
#### [Utilizar o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Utilizar a biblioteca de clientes com o Dapper](sql-database-elastic-scale-working-with-dapper.md)
### Implementações do cliente
#### [Daxko/CSI Software](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Desenvolvimento de bases de dados
### Saiba mais
#### Tabelas temporais
##### [Tabelas temporais](sql-database-temporal-tables.md)
##### [Políticas de retenção](sql-database-temporal-tables-retention-policy.md)
#### [Dados JSON](sql-database-json-features.md)
#### [Otimização dentro da memória](sql-database-in-memory.md)
### O que deve fazer
#### [Desenvolvimento do SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Adotar o OLTP dentro da memória](sql-database-in-memory-oltp-migration.md)
## Monitorização e Otimização
### Saiba mais
#### [Bases de dados individuais](sql-database-single-database-monitor.md)
#### [Descrição geral do Assistente da Base de Dados SQL](sql-database-advisor.md)
#### [Orientações para bases de dados individuais](sql-database-performance-guidance.md)
#### [Informações de desempenho: portal do Azure](sql-database-performance.md)
#### [Utilizar a criação de batches](sql-database-use-batching-to-improve-performance.md)
#### [Eventos expandidos](sql-database-xevent-db-diff-from-svr.md)
## Base de Dados SQL V11
### [Edições Web e Business Sunset](sql-database-web-business-sunset-faq.md)
### [Assistente de escalão de serviço](sql-database-service-tier-advisor.md)
### [Ferramenta de avaliação do conjunto elástico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Atualizar para V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Atualizar com o portal do Azure](sql-database-upgrade-server-portal.md)
#### [Atualizar com o PowerShell](sql-database-upgrade-server-powershell.md)
# Procedimento
## Criar e gerir
### [Gerir Base de Dados SQL com o portal do Azure](sql-database-manage-portal.md)
### [Gerir Base de Dados SQL com o PowerShell](sql-database-manage-powershell.md)
### [Gerir Base de Dados SQL com o SSMS](sql-database-manage-azure-ssms.md)
### Servidores
#### [Criar servidores](sql-database-create-servers.md)
#### [Ver ou atualizar as definições do servidor](sql-database-view-update-server-settings.md)
### Bases de dados individuais
#### [Criar bases de dados individuais](sql-database-create-databases.md)
#### [Ver ou atualizar as definições da base de dados](sql-database-view-update-database-settings.md)
### Regras da firewall
#### [Criar regras de firewall com o portal do Azure](sql-database-configure-firewall-settings.md)
#### [Criar regras de firewall com o PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [Criar regras de firewall com a API REST](sql-database-configure-firewall-settings-rest.md)
#### [Criar regras de firewall com o T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Várias bases de dados
#### [Atualizar biblioteca de clientes em aplicações cliente](sql-database-elastic-scale-upgrade-client-library.md)
#### Bases de dados em partição horizontal
##### [Configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Adicionar uma partição horizontal](sql-database-elastic-scale-add-a-shard.md)
##### [Corrigir problemas de mapas de partições horizontais](sql-database-elastic-database-recovery-manager.md)
##### [Migrar bases de dados aumentadas horizontalmente existentes para Bases de dados em partição horizontal](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Criar contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-perf-counters.md)
#### Tarefas elásticas
##### [Como posso instalar o serviço de tarefas elásticas?](sql-database-elastic-jobs-service-installation.md)
##### [Criar e gerir trabalhos elásticos com o PowerShell](sql-database-elastic-jobs-powershell.md) 
##### [Criar e gerir conjuntos elásticos com o portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
##### [Como posso desinstalar as tarefas elásticas?](sql-database-elastic-jobs-uninstall.md)
#### Conjuntos elásticos
##### [Criar com o portal do Azure](sql-database-elastic-pool-create-portal.md)
##### [Criar com o PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Criar com C#](sql-database-elastic-pool-create-csharp.md)
##### [Gerir com o portal do Azure](sql-database-elastic-pool-manage-portal.md)
##### [Gerir com o PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Gerir com C#](sql-database-elastic-pool-manage-csharp.md)
##### [Gerir com T-SQL](sql-database-elastic-pool-manage-tsql.md)
##  Autenticar e autorizar
### [Autenticação do Azure AD](sql-database-aad-authentication.md)
### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
## Encriptar dados
### [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/azure/dn948096)
### [Encriptação de colunas](https://msdn.microsoft.com/library/azure/ms179331)
## Migrar bases de dados
### Determinar a compatibilidade
#### [Determinar a compatibilidade utilizando o utilitário Pacote SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [Determinar a compatibilidade utilizando o SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Corrigir problemas de compatibilidade
#### [Corrigir problemas de compatibilidade utilizando o SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [Corrigir problemas de compatibilidade utilizando o SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Corrigir problemas de compatibilidade utilizando o SMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Migrar utilizando o Assistente de Migração SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## Monitorizar e otimizar
### [Query Performance Insight](sql-database-query-performance.md)
### [Assistente da Base de Dados SQL](sql-database-advisor-portal.md)
### [DMVs](sql-database-monitoring-with-dmvs.md)
### [Níveis de compatibilidade](sql-database-compatibility-level-query-performance-130.md)
### [Sugestões de otimização do desempenho](sql-database-troubleshoot-performance.md)
### Alterar os escalões de serviço e os níveis de desempenho
#### [Alterar camadas de serviço utilizando o portal do Azure](sql-database-scale-up.md)
#### [Alterar camadas de serviço utilizando o PowerShell](sql-database-scale-up-powershell.md)
### [Criar alertas](sql-database-insights-alerts-portal.md)
#### [Monitorizar o Armazenamento OLTP Dentro da Memória](sql-database-in-memory-oltp-monitoring.md)
### Arquivo de Consultas
#### [Utilizar o Arquivo de Consultas para monitorizar o desempenho](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Cenários de utilização do Arquivo de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Trabalhar com o Arquivo de Consultas](sql-database-operate-query-store.md)
### Eventos expandidos
#### [Código de destino de ficheiro de evento](sql-database-xevent-code-event-file.md)
#### [Código de destino de memória intermédia em anel](sql-database-xevent-code-ring-buffer.md)
## Mover dados
### Copiar uma base de dados SQL
#### [Copiar com o portal do Azure](sql-database-copy-portal.md)
#### [Copiar com o PowerShell](sql-database-copy-powershell.md)
#### [Copiar com o T-SQL](sql-database-copy-transact-sql.md)
### Exportar a base de dados para um ficheiro BACPAC
#### [Exportar com o portal do Azure](sql-database-export.md)
#### [Exportar com o SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Exportar com o utilitário Pacote SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [Exportar com o PowerShell](sql-database-export-powershell.md)
### Importar a base de dados de um ficheiro BACPAC
#### [Importar com o portal do Azure](sql-database-import.md)
#### [Importar com o PowerShell](sql-database-import-powershell.md)
#### [Importar com o SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Importar com o utilitário Pacote SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Carregar a partir do ficheiro CSV com o BCP](sql-database-load-from-csv-with-bcp.md)
## Consulta
### [Consultar com o SSMS](sql-database-connect-query-ssms.md)
## Cópia de Segurança e Restauro
### Retenção de cópia de segurança de longa duração
#### [Configurar retenção de cópia de segurança de longa duração](sql-database-configure-long-term-retention.md)
#### [Ver cópias de segurança num cofre dos Serviços de Recuperação](sql-database-view-backups-in-vault.md)
#### [Restaurar a partir de retenção de cópia de segurança de longa duração](sql-database-restore-from-long-term-retention.md)
#### [Eliminar a partir de retenção de cópia de segurança de longa duração](sql-database-long-term-retention-delete.md)
### Restaurar uma base de dados eliminada
#### [Restauro eliminado através do portal do Azure](sql-database-restore-deleted-database-portal.md)
#### [Restauro eliminado através do PowerShell](sql-database-restore-deleted-database-powershell.md)
### Restauro para um ponto anterior no tempo
#### [Restauro para um ponto anterior no tempo](sql-database-point-in-time-restore.md)
#### [Ver ponto de restauro mais antigo](sql-database-view-oldest-restore-point.md)
### [Restaurar a partir de cópia de segurança geo redundante](sql-database-geo-restore.md)
## Georreplicação Ativa
### [Configurar com o portal do Azure](sql-database-geo-replication-portal.md)
### [Configurar com o PowerShell](sql-database-geo-replication-powershell.md)
### [Configurar com o T-SQL](sql-database-geo-replication-transact-sql.md)
### [Ativação pós-falha com o portal do Azure](sql-database-geo-replication-failover-portal.md)
### [Ativação pós-falha com o PowerShell](sql-database-geo-replication-failover-powershell.md)
### [Ativação pós-falha com T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Resolução de problemas
### [Problemas de ligação](sql-database-troubleshoot-common-connection-issues.md)
### [Erro de ligação transitório](sql-database-troubleshoot-connection.md)
### [Diagnosticar e prevenir](sql-database-connectivity-issues.md)
### [Permissões](sql-database-troubleshoot-permissions.md)
### [Mover bases de dados](sql-database-troubleshoot-moving-data.md)
# Referência
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (DB Elástica)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [CLI 2.0 do Azure](/cli/azure/appservice)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Relacionado
## Biblioteca de Gestão de Base de Dados SQL
### [Obter o pacote de Biblioteca de Gestão de Base de Dados SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Controladores do SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/sql-database/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?service=sql-database)
## [Ferramentas do SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
