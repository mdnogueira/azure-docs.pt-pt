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
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 3f8ad9bf72555b9ab19af4c7a15f66b982fe66fe
ms.lasthandoff: 03/04/2017


---
# <a name="azure-sql-database-features"></a>Funcionalidades da Base de Dados SQL do Azure
Este tópico dispõe de uma descrição geral dos servidores lógicos  e bases de dados de Base de Dados SQL do Azure, e inclui uma matriz de suporte da funcionalidade com ligações de cada funcionalidade listada. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>O que é um servidor lógico da Base de Dados SQL do Azure?
Um servidor lógico da Base de Dados SQL do Azure funciona como um ponto administrativo central para várias bases de dados. Na Base de Dados SQL, um servidor é uma construção lógica distinta de uma instância do SQL Server com a qual poderá estar familiarizado no mundo no local. Especificamente, o serviço Base de Dados SQL não dá qualquer garantia relativamente à localização das bases de dados em relação aos respetivos servidores lógicos, nem expõe qualquer acesso de nível de instância ou funcionalidades. Para obter mais informações sobre servidores lógicos de SQL do Azure, veja [Servidores lógicos](sql-database-server-overview.md). 

## <a name="what-is-an-azure-sql-database"></a>O que é uma base de dados SQL do Azure?
Cada base de dados na Base de Dados SQL do Azure está associada a um servidor lógico. A base de dados pode ser:

- Uma base de dados individual com um [conjunto próprio de recursos](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTUs)
- Parte de um [conjunto de bases de dados](sql-database-elastic-pool.md) que [partilha um conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTUs)
- Parte de um [conjunto aumentado horizontalmente de bases de dados partilhadas](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que podem ser bases de dados individuais ou agrupadas
- Parte de um conjunto de bases de dados que participam num [padrão de estrutura de SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md), e cujas bases de dados podem ser individuais ou agrupadas (ou ambas) 

Para obter mais informações sobre as bases de dados SQL do Azure, veja [Bases de dados SQL](sql-database-overview.md).

## <a name="what-features-are-supported"></a>Que funcionalidades são suportadas?

As tabelas seguintes listam as principais funcionalidades da Base de Dados SQL do Azure e do SQL Server, especificam a sua suportabilidade e fornecem uma ligação para obter mais informações sobre a funcionalidade de cada plataforma. Para funcionalidades do Transact-SQL, siga a ligação da tabela para a categoria da funcionalidade. Veja também [Diferenças do Transact-SQL da Base de dados SQL do Azure](sql-database-transact-sql-information.md) para obter mais contexto sobre as razões para a falta de suporte de determinados tipos de funcionalidades.

Continuaremos a adicionar funcionalidades para V12. Por isso, aconselhamo-lo a visitar a nossa página Web de Atualizações de Serviço do Azure e utilizar os respetivos filtros:

* Filtrado para o [serviço da Base de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado para [anúncios](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de Disponibilidade Geral (DG) para funcionalidades de Base de Dados SQL.

> [!TIP]
> Para testar uma base de dados existente para compatibilidade com a Base de Dados SQL do Azure, consulte [Migrate a SQL Server database to Azure (Migrar uma base de dados do SQL Server para o Azure)](sql-database-cloud-migrate.md).
>

| **Funcionalidade** | **SQL Server** | **Base de Dados SQL do Azure** | 
| --- | :---: | :---: | 
| Georreplicação Ativa | Não suportado - consulte [Grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) | [Suportado](sql-database-geo-replication-overview.md)
| Sempre Encriptado | [Suportado](https://msdn.microsoft.com/library/mt163865.aspx) | [Suportado](sql-database-always-encrypted.md) |
| Grupos de Disponibilidade AlwaysOn | [Suportado](https://msdn.microsoft.com/library/hh510230.aspx) | Não suportado - consulte [Georreplicação Ativa](sql-database-geo-replication-overview.md) |
| Anexar uma base de dados | [Suportado](https://msdn.microsoft.com/library/ms190209.aspx) | Não suportado |
| Funções da aplicação | [Suportado](https://msdn.microsoft.com/library/ms190998.aspx) | [Suportado](https://msdn.microsoft.com/library/ms190998.aspx) |
| Dimensionar automaticamente | Não suportado | [Suportado](sql-database-service-tiers.md) |
| Azure Active Directory | Não suportado | [Suportado](sql-database-aad-authentication.md) |
| Azure Data Factory | Não suportado - consulte [Serviços de Integração do SQL Server (SSIS)](https://msdn.microsoft.com/library/ms141026.aspx) | [Suportado](https://azure.microsoft.com/services/data-factory/) |
| Auditoria | [Suportado](https://msdn.microsoft.com/library/cc280386.aspx) | [Suportado](sql-database-auditing.md) |
| Ficheiro BACPAC (exportação) | [Suportado](https://msdn.microsoft.com/library/hh213241.aspx) | [Suportado](sql-database-export.md) |
| Ficheiro BACPAC (importação) | [Suportado](https://msdn.microsoft.com/library/hh710052.aspx) | [Suportado](sql-database-import-portal.md) |
| Instruções de CÓPIA DE SEGURANÇA e RESTAURO | [Suportado](https://msdn.microsoft.com/library/ff848768.aspx) | Não suportado |
| Funções incorporadas | [Suportado](https://msdn.microsoft.com/library/ms174318.aspx) | [Maioria](https://msdn.microsoft.com/library/ms174318.aspx) |
| Captura de dados de alteração | [Suportado](https://msdn.microsoft.com/library/cc645937.aspx) | Não suportado |
| Monitorização de alterações | [Suportado](https://msdn.microsoft.com/library/bb933875.aspx) | [Suportado](https://msdn.microsoft.com/library/bb933875.aspx) |
| Instruções de agrupamento | [Suportado](https://msdn.microsoft.com/library/ff848763.aspx) | [Suportado](https://msdn.microsoft.com/library/ff848763.aspx) |
| Índices Columnstore | [Suportado](https://msdn.microsoft.com/library/gg492088.aspx) | [Apenas a edição Premium](https://msdn.microsoft.com/library/gg492088.aspx) |
| CLR (Common Language Runtime) | [Suportado](https://msdn.microsoft.com/library/ms131102.aspx) | Não suportado |
| Bases de dados contidas | [Suportado](https://msdn.microsoft.com/library/ff929071.aspx) | Incorporado |
| Utilizadores contidos | [Suportado](https://msdn.microsoft.com/library/ff929188.aspx) | [Suportado](sql-database-manage-logins.md#non-administrator-users) |
| Controlo de palavras-chave de linguagem de fluxo | [Suportado](https://msdn.microsoft.com/library/ms174290.aspx) | [Suportado](https://msdn.microsoft.com/library/ms174290.aspx) |
| Consultas entre bases de dados | [Suportado](https://msdn.microsoft.com/library/dn584627.aspx) | [Consultas elásticas](sql-database-elastic-query-overview.md) |
| Cursores | [Suportado](https://msdn.microsoft.com/library/ms181441.aspx) | [Suportado](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Compressão de dados | [Suportado](https://msdn.microsoft.com/library/cc280449.aspx) | [Suportado](https://msdn.microsoft.com/library/cc280449.aspx) |
| Cópias de segurança de bases de dados | [Expostos para utilizadores](https://msdn.microsoft.com/library/ms187048.aspx) | [Incorporado](sql-database-automated-backups.md) |
| Correio de base de dados | [Suportado](https://msdn.microsoft.com/library/ms189635.aspx) | Não suportado |
| Espelhamento da base de dados | [Suportado](https://msdn.microsoft.com/library/ms189852.aspx) | Não suportado |
| Opções de configuração de base de dados | [Suportado](https://msdn.microsoft.com/library/mt629158.aspx) | [Suportado](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [Suportado](https://msdn.microsoft.com/library/ff877925.aspx) | Não suportado |
| Instantâneos da base de dados | [Suportado](https://msdn.microsoft.com/library/ms175158.aspx) | Não suportado |
| Tipos de dados | [Suportado](https://msdn.microsoft.com/library/ms187752.aspx) | [Suportado](https://msdn.microsoft.com/library/ms187752.aspx) |  
| Declarações DBCC | [Todos](https://msdn.microsoft.com/library/ms188796.aspx) | [Alguns](https://msdn.microsoft.com/library/ms188796.aspx) |
| Declarações DDL | [Suportado](https://msdn.microsoft.com/library/ff848799.aspx) | [Maioria](https://msdn.microsoft.com/library/ff848799.aspx)
| Acionadores DDL | [Suportado](https://msdn.microsoft.com/library/ms175941.aspx) | [Apenas base de dados](https://msdn.microsoft.com/library/ms175941.aspx) |
| Transações distribuídas | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Apenas cenários de intra Bases de Dados SQL |
| Declarações DML | [Suportado](https://msdn.microsoft.com/library/ff848766.aspx) | [Maioria](https://msdn.microsoft.com/library/ff848766.aspx) |
| Acionadores DML | [Suportado](https://msdn.microsoft.com/library/ms178110.aspx) | [Suportado](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMVs | [Todos](https://msdn.microsoft.com/library/ms188754.aspx) | [Alguns](https://msdn.microsoft.com/library/ms188754.aspx) |
| conjuntos elásticos | Não suportado | [Suportado](sql-database-elastic-pool.md) |
| Tarefas elásticas | Não suportado - consulte [Agente do SQL Server](https://msdn.microsoft.com/library/ms189237.aspx) | [Suportado](sql-database-elastic-jobs-getting-started.md) | 
| Consultas elásticas | Não suportado - consulte [Consultas entre bases de dados](https://msdn.microsoft.com/library/dn584627.aspx) | [Suportado](sql-database-elastic-query-overview.md) |
| Notificações de eventos | [Suportado](https://msdn.microsoft.com/library/ms186376.aspx) | [Suportado](sql-database-insights-alerts-portal.md) |
| Expressões | [Suportado](https://msdn.microsoft.com/library/ms190286.aspx) | [Suportado](https://msdn.microsoft.com/library/ms190286.aspx) |
| Eventos expandidos | [Suportado](https://msdn.microsoft.com/library/bb630282.aspx) | [Alguns](sql-database-xevent-db-diff-from-svr.md) |
| Procedimentos armazenados expandidos | [Suportado](https://msdn.microsoft.com/library/ms164627.aspx) | Não suportado |
| Grupos de ficheiros | [Suportado](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Apenas principal](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Filestream | [Suportado](https://msdn.microsoft.com/library/gg471497.aspx) | Não suportado |
| Pesquisa em texto completo | [Suportado](https://msdn.microsoft.com/library/ms142571.aspx) | [Separadores de palavras de terceiros não suportados](https://msdn.microsoft.com/library/ms142571.aspx) |
| Funções | [Suportado](https://msdn.microsoft.com/library/ms174318.aspx) | [Maioria](https://msdn.microsoft.com/library/ms174318.aspx) |
| Otimização dentro da memória | [Suportado](https://msdn.microsoft.com/library/dn133186.aspx) | [Apenas a edição Premium](https://msdn.microsoft.com/library/dn133186.aspx) |
| Tarefas | [Agente do SQL Server](https://msdn.microsoft.com/library/ms189237.aspx) | [Suportado](sql-database-elastic-jobs-getting-started.md) |
| Suporte de dados JSON | [Suportado](https://msdn.microsoft.com/library/dn921897.aspx) | [Suportado](sql-database-json-features.md) |
| Elementos de linguagem | [Suportado](https://msdn.microsoft.com/library/ff848807.aspx) | [Maioria](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Servidores ligados | [Suportado](https://msdn.microsoft.com/library/ms188279.aspx) | Não suportado - consulte [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| Envio de registos | [Suportado](https://msdn.microsoft.com/library/ms187103.aspx) | Não suportado - consulte [Georreplicação Ativa](sql-database-geo-replication-overview.md) |
| Comandos de gestão | [Suportado](https://msdn.microsoft.com/library/ms190286.aspx)| [Não suportado](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [Suportado](https://msdn.microsoft.com/library/ff487003.aspx) | Não suportado |
| Registo mínimo na importação em massa | [Suportado](https://msdn.microsoft.com/library/ms190422.aspx) | Não suportado |
| Modificar dados do sistema | [Suportado](https://msdn.microsoft.com/library/ms178028.aspx) | Não suportado |
| Operações de índice online | [Suportado](https://msdn.microsoft.com/library/ms177442.aspx) | [Tamanho da transação limitado pelo escalão de serviço](https://msdn.microsoft.com/library/ms177442.aspx) |
| Operadores | [Suportado](https://msdn.microsoft.com/library/ms174986.aspx) | [Maioria](https://msdn.microsoft.com/library/ms174986.aspx) |
| Restauro de base de dados para um ponto no tempo | [Suportado](https://msdn.microsoft.com/library/ms179451.aspx) | [Suportado](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Suportado](https://msdn.microsoft.com/library/mt143171.aspx) | [Não suportado]
| Gestão baseada na política | [Suportado](https://msdn.microsoft.com/library/bb510667.aspx) | Não suportado |
| Predicados | [Suportado](https://msdn.microsoft.com/library/ms189523.aspx) | [Maioria](https://msdn.microsoft.com/library/ms189523.aspx)
| Serviços R | [Suportado](https://msdn.microsoft.com/library/mt604845.aspx)
| Governador de recursos | [Suportado](https://msdn.microsoft.com/library/bb933866.aspx) | Não suportado |
| Restaurar base de dados a partir da cópia de segurança | [Suportado](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [Apenas a partir de cópias de segurança incorporadas](sql-database-recovery-using-backups.md) |
| Segurança a Nível da Linha | [Suportado](https://msdn.microsoft.com/library/dn765131.aspx) | [Suportado](https://msdn.microsoft.com/library/dn765131.aspx) |
| Declarações de segurança | [Suportado](https://msdn.microsoft.com/library/ff848791.aspx) | [Alguns](https://msdn.microsoft.com/library/ff848791.aspx) |
| Pesquisa semântica | [Suportado](https://msdn.microsoft.com/library/gg492075.aspx) | Não suportado |
| Números de sequência | [Suportado](https://msdn.microsoft.com/library/ff878058.aspx) | [Suportado](https://msdn.microsoft.com/library/ff878058.aspx) |
| Mediador de Serviço | [Suportado](https://msdn.microsoft.com/library/bb522893.aspx) | Não suportado |
| Opções de configuração do servidor | [Suportado](https://msdn.microsoft.com/library/ms189631.aspx) | Não suportado - consulte [Opções de configuração de base de dados](https://msdn.microsoft.com/library/mt629158.aspx) |
| Definir instruções | [Suportado](https://msdn.microsoft.com/library/ms190356.aspx) | [Maioria](https://msdn.microsoft.com/library/ms190356.aspx) 
| Espacial | [Suportado](https://msdn.microsoft.com/library/bb933790.aspx) | [Suportado](https://msdn.microsoft.com/library/bb933790.aspx) |
| Agente do SQL Server | [Suportado](https://msdn.microsoft.com/library/ms189237.aspx) | Não suportado - consulte [Tarefas elásticas](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Suportado](https://msdn.microsoft.com/library/bb522607.aspx) | Não suportado - consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Suportado](https://msdn.microsoft.com/library/ms141026.aspx) | Não suportado - consulte [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| PowerShell do SQL Server | [Suportado](https://msdn.microsoft.com/library/hh245198.aspx) | [Suportado](https://msdn.microsoft.com/library/hh245198.aspx) |
| Gerador de perfis do SQL Server | [Suportado](https://msdn.microsoft.com/library/ms181091.aspx) | Não suportado - consulte [Eventos expandidos](https://msdn.microsoft.com/library/ms181091.aspx) |
| Replicação do SQL Server | [Suportado](https://msdn.microsoft.com/library/ms151198.aspx) | [Apenas subscritor de replicação de instantâneo e transacional](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Suportado](https://msdn.microsoft.com/library/ms159106.aspx) | Não suportado |
| Procedimentos armazenados | [Suportado](https://msdn.microsoft.com/library/ms190782.aspx) | [Suportado](https://msdn.microsoft.com/library/ms190782.aspx) |
| Funções armazenadas do sistema | [Suportado](https://msdn.microsoft.com/library/ff848780.aspx) | [Alguns](https://msdn.microsoft.com/library/ff848780.aspx) |
| Procedimentos armazenados do sistema | [Suportado](https://msdn.microsoft.com/library/ms187961.aspx) | [Alguns](https://msdn.microsoft.com/library/ms187961.aspx) |
| Tabelas de sistema | [Suportado](https://msdn.microsoft.com/library/ms179932.aspx) | [Alguns](https://msdn.microsoft.com/library/ms179932.aspx) |
| Vistas de sistema | [Suportado](https://msdn.microsoft.com/library/ms177862.aspx) | [Alguns](https://msdn.microsoft.com/library/ms177862.aspx)
| Criação de partições de tabela | [Suportado](https://msdn.microsoft.com/library/ms190787.aspx) | [Apenas um grupo de ficheiros primário](https://msdn.microsoft.com/library/ms190787.aspx) |
| Tabelas temporárias | [Local e global](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Só local](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Tabelas temporais | [Suportado](https://msdn.microsoft.com/library/dn935015.aspx) | [Suportado](sql-database-temporal-tables.md) |
| Instruções de transação | [Suportado](https://msdn.microsoft.com/library/ms174377.aspx) | [Suportado](https://msdn.microsoft.com/library/ms174377.aspx) |
| Variáveis | [Suportado](https://msdn.microsoft.com/library/ff848809.aspx) | | [Suportado](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Encriptação de dados transparente (TDE)  | [Suportado](https://msdn.microsoft.com/library/bb934049.aspx) | [Suportado](https://msdn.microsoft.com/dn948096.aspx) |
| Clustering de Ativação Pós-falha do Windows Server | [Suportado](https://msdn.microsoft.com/library/hh270278.aspx) | Não suportado - consulte [Georreplicação Ativa](sql-database-geo-replication-overview.md) |
| Índices XML | [Suportado](http://msdn.microsoft.com/library/bb934097.aspx) | [Suportado](http://msdn.microsoft.com/library/bb934097.aspx) |
| Declarações XML | [Suportado](https://msdn.microsoft.com/library/ff848798.aspx) | [Suportado](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o serviço Base de Dados SQL do Azure, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
- Para obter uma descrição geral dos servidores lógicos do SQL Azure, veja [Descrição geral do servidor lógico da Base de Dados SQL](sql-database-server-overview.md)
- Para obter uma descrição geral das bases de dados SQL do Azure, veja [Descrição geral da Base de Dados SQL](sql-database-overview.md)
- Para obter informações sobre o suporte de Transact-SQL e as diferenças, veja [Diferenças do Transact-SQL da Base de Dados SQL do Azure](sql-database-transact-sql-information.md).
- Para obter informações sobre quotas e limitações de recursos específicas com base na sua **camada de serviços**. Para obter uma descrição geral de camadas de serviços, veja [Camadas de serviços da Base de Dados SQL](sql-database-service-tiers.md).
- Para obter uma descrição geral da segurança, veja [Descrição Geral da Segurança da Base de Dados SQL do Azure](sql-database-security-overview.md).
- Para obter informações sobre a disponibilidade de controlador e o suporte da Base de Dados SQL, veja [Bibliotecas de Ligação para a Base de Dados SQL e o SQL Server](sql-database-libraries.md).

