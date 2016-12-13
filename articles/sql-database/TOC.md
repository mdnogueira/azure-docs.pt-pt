# Descrição geral
## [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
### [Escalões de serviço](sql-database-service-tiers.md)
### [Unidades de Transação da Base de Dados](sql-database-what-is-a-dtu.md)
### [Descrição geral de referência da DTU](sql-database-benchmark-overview.md)
### [Limites de recursos](sql-database-resource-limits.md)
### [Funcionalidades](sql-database-features.md)
### [FAQ da Base de Dados SQL](sql-database-faq.md)
## Benefícios
### [Aprende e adapta-se](sql-database-learn-and-adapt.md)
### [Dimensiona-se sem perder tempo](sql-database-scale-on-the-fly.md)
### [Cria aplicações multi-inquilino](sql-database-build-multi-tenant-apps.md)
### [Protege e defende](sql-database-helps-secures-and-protects.md)
### [Funciona no seu ambiente](sql-database-works-in-your-environment.md)
## Comparações
### [Base de Dados SQL em comparação com o SQL numa VM](sql-database-paas-vs-sql-server-iaas.md)
### [Diferenças do T-SQL](sql-database-transact-sql-information.md)
### [SQL versus NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Ferramentas da Base de Dados SQL](sql-database-manage-overview.md)
## [Tutoriais da Base de Dados SQL](sql-database-explore-tutorials.md)
## [Inícios rápidos das soluções](sql-database-solution-quick-starts.md)
## Segurança
### [Centro de Segurança do Azure para a Base de Dados SQL do Azure](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [Centro de Segurança do SQL](https://msdn.microsoft.com/library/azure/bb510589)

# Introdução
## Bases de dados e servidores
### Bases de dados individuais
#### Saiba mais
##### [Tutorial do portal do Azure: Criar uma base de dados SQL do Azure com o portal do Azure](sql-database-get-started.md)
#### O que deve fazer
##### [Tutorial do portal do Azure: Criar uma base de dados SQL do Azure com o portal do Azure](sql-database-get-started.md)
##### [Tutorial do portal do Azure: Criar uma base de dados SQL do Azure com o PowerShell](sql-database-get-started-powershell.md)
##### [Tutorial do portal do Azure: Criar uma base de dados SQL do Azure com C#](sql-database-get-started-csharp.md)
### Conjuntos de bases de dados elásticas
#### Saiba mais
##### [Conjuntos de bases de dados elásticas](sql-database-elastic-pool.md)
##### [Quando utilizar um conjunto de bases de dados elásticas](sql-database-elastic-pool-guidance.md)
##### [Preços do conjunto elástico](sql-database-elastic-pool-price.md)
#### O que deve fazer
##### [Criar com o portal do Azure](sql-database-elastic-pool-create-portal.md)
##### [Criar com o PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Criar com C#](sql-database-elastic-pool-create-csharp.md)
### Aumentar horizontalmente
#### Saiba mais
##### [Aumentar horizontalmente](sql-database-elastic-scale-introduction.md)
##### [Dimensionamento elástico](sql-database-elastic-scale-get-started.md)
##### [Criar bases de dados de cloud dimensionáveis](sql-database-elastic-database-client-library.md)
##### [Tarefas em várias bases de dados](sql-database-elastic-jobs-overview.md)
##### [FAQ das ferramentas elásticas](sql-database-elastic-scale-faq.md)
##### [Glossário das ferramentas das bases de dados elásticas](sql-database-elastic-scale-glossary.md)
##### [Mover dados entre bases de dados de cloud aumentadas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)
#### O que deve fazer
##### [Tarefas elásticas](sql-database-elastic-jobs-getting-started.md)
##### [Relatórios em várias bases de dados](sql-database-elastic-query-getting-started.md)
##### [Consultas em várias bases de dados](sql-database-elastic-query-getting-started-vertical.md)
##### [Desinstalar os trabalhos elásticos](sql-database-elastic-jobs-uninstall.md)
## Migrar e mover dados
### Saiba mais
#### [Migrar uma base de dados](sql-database-cloud-migrate.md)
#### [Replicação transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronização de dados](sql-database-get-started-sql-data-sync.md)
#### [Copiar uma base de dados SQL](sql-database-copy.md)
### O que deve fazer
#### Determinar a compatibilidade da base de dados
##### [Utilitário Pacote de SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### Corrigir problemas de compatibilidade da base de dados
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [Assistente de Migração do SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [Assistente de Migração do SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### Copiar uma Base de Dados
##### [Copiar com o portal do Azure](sql-database-copy-portal.md)
##### [Copiar com o PowerShell](sql-database-copy-powershell.md)
##### [Copiar com o T-SQL](sql-database-copy-transact-sql.md)
#### Exportar a base de dados para um ficheiro BACPAC
##### [Portal do Azure](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [Utilitário Pacote de SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### Importar a base de dados de um ficheiro BACPAC
##### [Portal do Azure](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [Utilitário Pacote de SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Carregar a partir do ficheiro CSV com o BCP](sql-database-load-from-csv-with-bcp.md)
## Autenticar e conceder acesso
### Saiba mais
#### [Descrição geral](sql-database-security.md)
#### [Diretrizes de segurança](sql-database-security-guidelines.md)
#### [Gerir inícios de sessão](sql-database-manage-logins.md)
### O que deve fazer
### [Tutorial: autenticação e acesso do SQL](sql-database-get-started-security.md)
## Proteger e defender dados
### Saiba mais
#### Auditoria
##### [Auditoria](sql-database-auditing-get-started.md)
##### [Suporte para clientes de nível inferior e alterações de ponto final de IP para auditoria](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Deteção de ameaças](sql-database-threat-detection-get-started.md)
#### Encriptar dados
##### [Cofre de chaves do Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Arquivo de certificados do Windows](sql-database-always-encrypted.md)
##### [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/azure/dn948096)
##### [Encriptação de colunas](https://msdn.microsoft.com/library/azure/ms179331)
#### Mascarar dados
##### Máscara de dados dinâmica
###### [Portal do Azure](sql-database-dynamic-data-masking-get-started.md)
### O que deve fazer
#### [Máscara de dados dinâmica com o portal do Azure](sql-database-dynamic-data-masking-get-started.md)
## Continuidade do negócio
### Saiba mais
#### [Descrição geral](sql-database-business-continuity.md)
#### [Cópias de segurança de bases de dados](sql-database-automated-backups.md)
#### [Retenção de longa duração](sql-database-long-term-retention.md)
#### [Recuperação de bases de dados com a utilização de cópias de segurança](sql-database-recovery-using-backups.md)
#### [Requisitos de autenticação para a recuperação após desastre](sql-database-geo-replication-security-config.md)
#### [Cenários de design de continuidade de negócio](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estratégias de recuperação após desastre com os conjuntos elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Implementar atualizações](sql-database-manage-application-rolling-upgrade.md)
### O que deve fazer
#### Restaurar uma base de dados eliminada
##### [Portal do Azure](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### Restauro para um ponto anterior no tempo
##### [Portal do Azure](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### Bases de dados de georrestauro
##### [Portal do Azure](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Tabela individual](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Recuperar a partir de uma falha do datacenter](sql-database-disaster-recovery.md)
#### [Realizar testes de recuperação após desastre](sql-database-disaster-recovery-drills.md)
### Replicar bases de dados
#### [Descrição geral da Georreplicação Ativa](sql-database-geo-replication-overview.md)
#### Configurar a Georreplicação Ativa
##### [Portal do Azure](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### Fazer a ativação pós-falha com a Georreplicação Ativa
##### [Portal do Azure](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Programação de aplicações
### Saiba mais
#### [Descrição geral da programação de aplicações de bases de dados](sql-database-develop-overview.md)
#### [Bibliotecas de conectividade](sql-database-libraries.md)
#### [Aplicações SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md)
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
### Implementações de software como serviço do cliente
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
#### [Dentro da Memória](sql-database-in-memory.md)
### O que deve fazer
#### [Desenvolvimento do SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
## Monitorização e Otimização
### Saiba mais
#### [Bases de dados individuais](sql-database-single-database-monitor.md)
#### [Descrição geral do Assistente da Base de Dados SQL](sql-database-advisor.md)
#### [Orientações para bases de dados individuais](sql-database-performance-guidance.md)
#### [Informações de cargas de trabalho no portal do Azure](sql-database-performance.md)
#### [Utilizar a criação de batches](sql-database-use-batching-to-improve-performance.md)
## Base de Dados SQL V11
### [Edições Web e Business Sunset](sql-database-web-business-sunset-faq.md)
### [Assistente de escalão de serviço](sql-database-service-tier-advisor.md)
### [Ferramenta de avaliação do conjunto elástico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Atualizar para V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Atualizar com o portal do Azure](sql-database-upgrade-server-portal.md)
#### [Atualizar com o PowerShell](sql-database-upgrade-server-powershell.md)
#### [Recomendações de escalão de preços](sql-database-service-tier-advisor.md)

# Procedimento
## Criar e gerir
### Servidores e bases de dados
#### [Bases de dados individuais](sql-database-manage-portal.md)
#### [Portal do Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Conjuntos de bases de dados elásticas
#### [Criar com o portal do Azure](sql-database-elastic-pool-create-portal.md)
#### [Criar com o PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [Criar com C#](sql-database-elastic-pool-create-csharp.md)
#### [Gerir com o portal do Azure](sql-database-elastic-pool-manage-portal.md)
#### [Gerir com o PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [Gerir com C#](sql-database-elastic-pool-manage-csharp.md)
#### [Gerir com T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Bases de dados em partição horizontal
#### [Utilizar o gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
#### [Dividir e intercalar a configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Trabalhar com o Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Utilizar o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Encaminhamento Dependente de Dados](sql-database-elastic-scale-data-dependent-routing.md)
#### [Segurança ao nível de linha multi-inquilino](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Gerir credenciais](sql-database-elastic-scale-manage-credentials.md)
#### [Implementar um serviço de divisão/intercalação](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Adicionar uma partição horizontal](sql-database-elastic-scale-add-a-shard.md)
#### [Utilizar a classe RecoveryManager para corrigir problemas do mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md)
###  Autenticação
#### [Autenticação do Azure AD](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Regras da firewall
#### [Portal do Azure](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [API REST](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Trabalhos e automatização
#### [Instalação do serviço](sql-database-elastic-jobs-service-installation.md)
#### [Criar e gerir conjuntos elásticos no portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [Criar e gerir trabalhos elásticos com o PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Atualizar a biblioteca de clientes](sql-database-elastic-scale-upgrade-client-library.md)
#### [Gerir Bases de Dados SQL com o Serviço de Automatização do Azure](sql-database-manage-automation.md)
### [Firewall](sql-database-firewall-configure.md)
## Encriptar dados
### [Descrição geral sempre encriptada](sql-database-always-encrypted.md)
### [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/azure/dn948096)
### [Encriptação de colunas](https://msdn.microsoft.com/library/azure/ms179331)
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
### [Migrar bases de dados aumentadas horizontalmente existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
## Monitorizar e otimizar
### [Query Performance Insight](sql-database-query-performance.md)
### [Assistente da Base de Dados SQL](sql-database-advisor-portal.md)
### [DMVs](sql-database-monitoring-with-dmvs.md)
### [Níveis de compatibilidade](sql-database-compatibility-level-query-performance-130.md)
### [Contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-perf-counters.md)
### [Sugestões de otimização do desempenho](sql-database-troubleshoot-performance.md)
### Alterar os escalões de serviço e os níveis de desempenho
#### [Com o Portal do Azure](sql-database-scale-up.md)
#### [Com o PowerShell](sql-database-scale-up-powershell.md)
### [Criar alertas](sql-database-insights-alerts-portal.md)
### OLTP dentro da memória
#### [Adotar o OLTP dentro da memória](sql-database-in-memory-oltp-migration.md)
#### [Monitorizar o Armazenamento OLTP Dentro da Memória](sql-database-in-memory-oltp-monitoring.md)
### Arquivo de Consultas
#### [Utilizar o Arquivo de Consultas para monitorizar o desempenho](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Cenários de utilização do Arquivo de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Trabalhar com o Arquivo de Consultas](sql-database-operate-query-store.md)
### Eventos expandidos
#### [Eventos expandidos](sql-database-xevent-db-diff-from-svr.md)
#### [Código de destino de ficheiro de evento](sql-database-xevent-code-event-file.md)
#### [Código de destino de memória intermédia em anel](sql-database-xevent-code-ring-buffer.md)
## Mover dados
### [Copiar uma base de dados SQL](sql-database-copy.md)
#### [Portal do Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exportar a base de dados para um ficheiro BACPAC
#### [Portal do Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilitário Pacote de SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importar a base de dados de um ficheiro BACPAC
#### [Portal do Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilitário Pacote de SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Carregar a partir do ficheiro CSV com o BCP](sql-database-load-from-csv-with-bcp.md)
### [Mover dados entre bases de dados de cloud aumentadas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)
## Consulta
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Consultas em várias partições horizontais](sql-database-elastic-scale-multishard-querying.md)
### Consultas entre bases de dados
#### [Descrição geral](sql-database-elastic-query-overview.md)
#### [Consultas em várias bases de dados com diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md)
#### [Relatórios em várias bases de dados](sql-database-elastic-query-horizontal-partitioning.md)
#### [Transações distribuídas entre bases de dados de cloud](sql-database-elastic-transactions-overview.md)
## Restauro
### Restaurar uma base de dados eliminada
#### [Portal do Azure](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
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
### [Problemas de ligação](sql-database-troubleshoot-common-connection-issues.md)
### [Erro de ligação transitório](sql-database-troubleshoot-connection.md)
### [Diagnosticar e prevenir](sql-database-connectivity-issues.md)
### [Permissões](sql-database-troubleshoot-permissions.md)
### [Mover bases de dados](sql-database-troubleshoot-moving-data.md)


# Referência
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (DB Elástica)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](https://docs.microsoft.com)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [REST](/rest/api/sql/)

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


<!--HONumber=Dec16_HO1-->


