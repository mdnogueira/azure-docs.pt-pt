---
title: "Todos os tópicos para o serviço do SQL Data Warehouse | Microsoft Docs"
description: "O nome de tabela de todos os tópicos para o serviço do Azure SQL Data Warehouse que existe no http://azure.microsoft.com/documentation/articles/, título e descrição."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
ms.author: barbkess
ms.openlocfilehash: 9fe41f12960dc099700e01573b4f03ebf63f8749
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Todos os tópicos para o serviço do Azure SQL Data Warehouse
Este tópico apresenta uma lista de cada tópico que se aplica diretamente para o **SQL Data Warehouse** serviço do Azure. Pode procurar esta página Web para palavras-chave, utilizando **Ctrl + F**, para localizar os tópicos de interesse atual.

## <a name="new"></a>novo
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 1 |[Cópias de segurança do SQL Data Warehouse](sql-data-warehouse-backups.md) |Saiba mais sobre cópias de segurança do armazém de dados do SQL Server da base de dados incorporados que lhe permite restaurar um SQL Data Warehouse do Azure para um ponto de restauro ou uma região geográfica diferentes. |

## <a name="updated-articles-sql-data-warehouse"></a>Artigos atualizados, o SQL Data Warehouse
Esta secção lista os artigos que foram atualizados recentemente, em que a atualização foi grande ou significativas. Para cada artigo atualizado, é apresentado um fragmento aproximado do texto adicionado markdown. Foram atualizados os artigos dentro do intervalo de datas de **2016-08-22** para **2016-10-05**.

| &nbsp; | Artigo | Texto atualizado, fragmento | Atualizado quando |
| ---:|:--- |:--- |:--- |
| 2 |[Carregar dados do blob storage do Azure para o SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/-Para controlar r.command SELECIONE bytes e ficheiros, s.request_id, r.status, contagem (input_name distintos) como nbr_files, SOMA (s.bytes_processed) 1024/1024 como gb_processed partir sys.dm_pdw_exec_requests r associação interna sys.dm_pdw_dms_external_work s no r.request_id = s.request_id onde r. Etiqueta = ' CTAS: cso de carga. DimProduct ' ou r. Etiqueta = ' CTAS: cso de carga. GROUP BY r.command dos FactOnlineSales, s.request_id, r.status ORDER BY nbr_files desc, gb_processed desc; |2016-09-07 |
| 3 |[Restauro do SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |* * Posso restaurar um armazém de dados em pausa? * * para restaurar um armazém de dados que está em pausa, terá primeiro colocá-lo novamente online. Assim que o armazém de dados estiver novamente online, tem de pontos de restauro à sua escolha de sete dias. * * Restaurar para uma região georredundante * se estiver a utilizar o armazenamento georredundante, pode restaurar o armazém de dados para o seu centro de dados emparelhado numa região geográfica diferente. O armazém de dados é restaurado a partir da última cópia de segurança diária. * * Restaure linha cronológica * * pode restaurar uma base de dados para qualquer ponto de restauro nos últimos sete dias. Instantâneos de iniciar a cada quatro para oito horas e estão disponíveis para sete dias. Quando um instantâneo com mais de sete dias, expirar e o respetivo ponto de restauro já não está disponível. * * Restaure os custos * * os encargos de armazenamento para o armazém de dados restaurada é faturado a taxa de Premium Storage do Azure. Se colocar em pausa de um armazém de dados restaurada, são-lhe cobrados para armazenamento a taxa de Premium Storage do Azure. A vantagem de colocar em pausa não é que são encargos |2016-09-29 |

## <a name="get-started"></a>Introdução
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 4 |[Authentication to Azure SQL Data Warehouse (Autenticação no Azure SQL Data Warehouse)](sql-data-warehouse-authentication.md) |Authentication do Azure Active Directory (AAD) e o SQL Server para o Azure SQL Data Warehouse. |
| 5 |[Melhores práticas do Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md) |Recomendações e melhores práticas que deve saber quando desenvolve soluções para o Azure SQL Data Warehouse. Estas irão ajudá-lo a ser bem-sucedido. |
| 6 |[Controladores para o armazém de dados SQL do Azure](sql-data-warehouse-connection-strings.md) |Cadeias de ligação e controladores para o SQL Data Warehouse |
| 7 |[Ligar ao Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md) |Como encontrar o nome do servidor e a cadeia de ligação do Azure SQL Data Warehouse |
| 8 |[Analisar dados com o Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Utilize o Azure Machine Learning para criar um modelo preditivo de machine learning com base em dados armazenados no Azure SQL Data Warehouse. |
| 9 |[Consultar o Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |Consultar o Azure SQL Data Warehouse com o Utilitário de Linha de Comandos sqlcmd. |
| 10 |[Criar uma base de dados do SQL Data Warehouse, utilizando Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) |Saiba como criar um Azure SQL Data Warehouse com TSQL |
| 11 |[Como criar um pedido de suporte para o SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) |Como criar um pedido de suporte no Azure SQL Data Warehouse. |
| 12 |[Carregar dados com o Azure Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Saiba como carregar dados com o Azure Data Factory |
| 13 |[Carregar dados com o PolyBase no SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) |Saiba o que é PolyBase e como utilizá-lo em cenários de armazenamento de dados. |
| 14 |[Criar um Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) |Saiba como criar um Azure SQL Data Warehouse no Portal do Azure |
| 15 |[Criar SQL Data Warehouse com o PowerShell](sql-data-warehouse-get-started-provision-powershell.md) |Criar SQL Data Warehouse com o PowerShell |
| 16 |[Visualizar dados com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) |Visualizar dados do SQL Data Warehouse com o Power BI |
| 17 |[Consultar o Azure SQL Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Consultar o SQL Data Warehouse com o Visual Studio. |

## <a name="develop"></a>Programar
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 18 |[Otimizar as transações do SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) |Melhores práticas obter orientações sobre a escrita de atualizações de transação eficiente no Azure SQL Data Warehouse |
| 19 |[Gestão de simultaneidade e carga de trabalho do armazém de dados do SQL Server](sql-data-warehouse-develop-concurrency.md) |Compreenda a gestão de simultaneidade e carga de trabalho no Azure SQL Data Warehouse para desenvolver soluções. |
| 20 |[Create Table As Select (CTAS) no SQL Data Warehouse](sql-data-warehouse-develop-ctas.md) |Sugestões para programação com a tabela de criar como instrução de select (CTAS) no Azure SQL Data Warehouse para desenvolver soluções. |
| 21 |[Dinâmica SQL no SQL Data Warehouse](sql-data-warehouse-develop-dynamic-sql.md) |Sugestões de utilização do SQL Server dinâmico no Azure SQL Data Warehouse para desenvolver soluções. |
| 22 |[Agrupar por opções no SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) |Sugestões para implementar o grupo por opções no Azure SQL Data Warehouse para desenvolver soluções. |
| 23 |[Utilize etiquetas para consultas de instrumento no SQL Data Warehouse](sql-data-warehouse-develop-label.md) |Sugestões para utilizar etiquetas para consultas de instrumento no Azure SQL Data Warehouse para desenvolver soluções. |
| 24 |[Ciclos no SQL Data Warehouse](sql-data-warehouse-develop-loops.md) |Sugestões para ciclos de Transact-SQL e cursores substituindo no Azure SQL Data Warehouse para desenvolver soluções. |
| 25 |[Procedimentos armazenados no SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) |Sugestões para implementar os procedimentos armazenados no Azure SQL Data Warehouse para desenvolver soluções. |
| 26 |[Transações no SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) |Sugestões para implementar as transações no Azure SQL Data Warehouse para desenvolver soluções. |
| 27 |[Esquemas definido pelo utilizador no SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) |Sugestões para utilizar os esquemas de Transact-SQL no Azure SQL Data Warehouse para desenvolver soluções. |
| 28 |[Atribuir variáveis no SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) |Sugestões para atribuição de variáveis de Transact-SQL no Azure SQL Data Warehouse para desenvolver soluções. |
| 29 |[Vistas do armazém de dados do SQL Server](sql-data-warehouse-develop-views.md) |Sugestões para utilizar as vistas de Transact-SQL no Azure SQL Data Warehouse para desenvolver soluções. |
| 30 |[As decisões de conceção e técnicas de programação para o SQL Data Warehouse](sql-data-warehouse-overview-develop.md) |Conceitos de desenvolvimento, as decisões de conceção, recomendações e técnicas de programação para o SQL Data Warehouse. |

## <a name="manage"></a>Gerir
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 31 |[Gerir a capacidade de computação no Azure SQL Data Warehouse (descrição geral)](sql-data-warehouse-manage-compute-overview.md) |Desempenho ampliação capacidades no Azure SQL Data Warehouse. Aumentar horizontalmente ao ajustar as DWUs ou colocar em pausa e retomar a recursos de computação para reduzir os custos. |
| 32 |[Gerir a capacidade de computação no Azure SQL Data Warehouse (portal do Azure)](sql-data-warehouse-manage-compute-portal.md) |Tarefas de portais do Azure para gerir poder de computação. Dimensionar os recursos de computação ao ajustar as DWUs. Ou, colocar em pausa e retomar a recursos de computação para reduzir os custos. |
| 33 |[Gerir a capacidade de computação no Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |As tarefas do PowerShell para gerir poder de computação. Dimensionar os recursos de computação ao ajustar as DWUs. Ou, colocar em pausa e retomar a recursos de computação para reduzir os custos. |
| 34 |[Gerir a capacidade de computação no Azure SQL dados armazém (REST)](sql-data-warehouse-manage-compute-rest-api.md) |As tarefas do PowerShell para gerir poder de computação. Dimensionar os recursos de computação ao ajustar as DWUs. Ou, colocar em pausa e retomar a recursos de computação para reduzir os custos. |
| 35 |[Gerir a capacidade de computação no armazém de dados do Azure SQL (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |Tarefas de Transact-SQL (T-SQL) ao desempenho de escalamento horizontal ao ajustar as DWUs. Reduzir os custos ao aumentar durante o pico. |
| 36 |[Monitorizar a carga de trabalho com DMVs](sql-data-warehouse-manage-monitor.md) |Saiba como monitorizar a carga de trabalho com DMVs. |
| 37 |[Gerir bases de dados no Azure SQL Data Warehouse](sql-data-warehouse-overview-manage.md) |Descrição geral da gestão de bases de dados do armazém de dados do SQL Server. Inclui ferramentas de gestão, as DWUs e o desempenho de escalamento horizontal, resolução de problemas de desempenho das consultas, estabelecer políticas boa segurança e restaurar uma base de dados de Corrupção de dados ou a partir de uma falha regional. |
| 38 |[Monitorizar a consultas do utilizador no Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md) |Descrição geral das considerações, melhores práticas e as tarefas de monitorização de consultas do utilizador no Azure SQL Data Warehouse |
| 39 |[Restauro do SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |Descrição geral das opções de restauro de base de dados para recuperar uma base de dados no Azure SQL Data Warehouse. |
| 40 |[Restaurar um armazém de dados SQL do Azure (Portal)](sql-data-warehouse-restore-database-portal.md) |Tarefas do portais do Azure para restaurar um Azure SQL Data Warehouse. |
| 41 |[Restaurar um armazém de dados SQL do Azure (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |Tarefas do PowerShell para restaurar um Azure SQL Data Warehouse. |
| 42 |[Restaurar um armazém de dados SQL do Azure (REST API)](sql-data-warehouse-restore-database-rest-api.md) |Tarefas da REST API para restaurar um Azure SQL Data Warehouse. |

## <a name="tables-and-indexes"></a>Tabelas e índices
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 43 |[Tipos de dados de tabelas no armazém de dados do SQL Server](sql-data-warehouse-tables-data-types.md) |Introdução aos tipos de dados de tabelas do Azure SQL Data Warehouse. |
| 44 |[Distribuir a tabelas no armazém de dados do SQL Server](sql-data-warehouse-tables-distribute.md) |Introdução à distribuição de tabelas do Azure SQL Data Warehouse. |
| 45 |[A indexação de tabelas no armazém de dados do SQL Server](sql-data-warehouse-tables-index.md) |Introdução à tabela de indexação no Azure SQL Data Warehouse. |
| 46 |[Descrição geral das tabelas no armazém de dados do SQL Server](sql-data-warehouse-tables-overview.md) |Introdução ao Azure SQL tabelas de armazém de dados. |
| 47 |[A criação de partições de tabelas no armazém de dados do SQL Server](sql-data-warehouse-tables-partition.md) |Introdução à criação de partições de tabela no Azure SQL Data Warehouse. |
| 48 |[Gerir as estatísticas em tabelas no armazém de dados do SQL Server](sql-data-warehouse-tables-statistics.md) |Introdução ao estatísticas de tabelas do Azure SQL Data Warehouse. |
| 49 |[Tabelas temporárias no SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) |Introdução ao tabelas temporárias no Azure SQL Data Warehouse. |

## <a name="integrate"></a>Integrar
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 50 |[Utilize o Azure Data Factory com o SQL Data Warehouse](sql-data-warehouse-integrate-azure-data-factory.md) |Sugestões de utilização do Azure Data Factory (ADF) com o Azure SQL Data Warehouse para desenvolver soluções. |
| 51 |[Utilização do Azure Machine Learning com o SQL Data Warehouse](sql-data-warehouse-integrate-azure-machine-learning.md) |Tutorial para utilizar o Azure Machine Learning com o Azure SQL Data Warehouse para desenvolver soluções. |
| 52 |[Utilizam o Azure Stream Analytics com o SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) |Sugestões para utilizar o Azure Stream Analytics com o Azure SQL Data Warehouse para desenvolver soluções. |
| 53 |[Utilizar o Power BI com o SQL Data Warehouse](sql-data-warehouse-integrate-power-bi.md) |Sugestões para utilizar o Power BI com o Azure SQL Data Warehouse para desenvolver soluções. |
| 54 |[Tirar partido de outros serviços com o SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) |Ferramentas e parceiros com soluções que se integram com o SQL Data Warehouse. |

## <a name="load"></a>Carregar
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 55 |[Carregar dados do blob storage do Azure para o Azure SQL Data Warehouse (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Saiba como carregar dados com o Azure Data Factory |
| 56 |[Carregar dados do blob storage do Azure para o SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |Saiba como utilizar o PolyBase para carregar dados do blob storage do Azure para o SQL Data Warehouse. Carregar tabelas alguns dos dados públicos para o esquema do armazém de dados de revenda Contoso. |
| 57 |[Carregar dados do SQL Server para o Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |Utiliza o bcp para exportar dados do SQL Server para ficheiros simples, o AZCopy para importar dados para o armazenamento de blobs do Azure e o PolyBase para incorporar os dados no Azure SQL Data Warehouse. |
| 58 |[Carregar dados do SQL Server para o Azure SQL Data Warehouse (ficheiros simples)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |Para um tamanho de dados de pequena dimensão, utilize o bcp para exportar dados do SQL Server para ficheiros simples e importar os dados diretamente para o Azure SQL Data Warehouse. |
| 59 |[Carregar dados do SQL Server do armazém de dados de SQL do Azure (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |Mostra como criar um pacote de SQL Server Integration Services (SSIS) para mover dados de uma ampla variedade de origens de dados para o SQL Data Warehouse. |
| 60 |[Carregar dados com o PolyBase no SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) |Utiliza o bcp para exportar dados do SQL Server para ficheiros simples, o AZCopy para importar dados para o armazenamento de blobs do Azure e o PolyBase para incorporar os dados no Azure SQL Data Warehouse. |
| 61 |[Guia para utilizar o PolyBase no SQL Data Warehouse](sql-data-warehouse-load-polybase-guide.md) |Diretrizes e recomendações para utilizar o PolyBase em cenários de armazém de dados do SQL Server. |
| 62 |[Carregar dados de exemplo para o SQL Data Warehouse](sql-data-warehouse-load-sample-databases.md) |Carregar dados de exemplo para o SQL Data Warehouse |
| 63 |[Carregar dados com o bcp](sql-data-warehouse-load-with-bcp.md) |Saiba o que é o bcp e como utilizá-lo para cenários de armazenamento de dados. |
| 64 |[Carregar dados para o Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) |Saiba os cenários comuns para dados de carregamento para o SQL Data Warehouse. Estes incluem utilizando o PolyBase, armazenamento de Blobs do Azure, ficheiros simples e o envio de disco. Também pode utilizar ferramentas de terceiros. |

## <a name="migrate"></a>Migrar
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 65 |[Migrar o seu código SQL ao SQL Data Warehouse](sql-data-warehouse-migrate-code.md) |Sugestões para migrar o código do SQL Server para o Azure SQL Data Warehouse para desenvolver soluções. |
| 66 |[Migrar os dados](sql-data-warehouse-migrate-data.md) |Sugestões para migrar os dados para o Azure SQL Data Warehouse para desenvolver soluções. |
| 67 |[Utilitário de migração do armazém de dados (pré-visualização)](sql-data-warehouse-migrate-migration-utility.md) |Migre para o armazém de dados do SQL Server. |
| 68 |[Migrar o esquema para o SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) |Sugestões para migrar o esquema para o Azure SQL Data Warehouse para desenvolver soluções. |
| 69 |[Migre a sua solução para o SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) |Orientações de migração para colocar a sua solução de plataforma do Azure SQL Data Warehouse. |

## <a name="partners"></a>Parceiros
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 70 |[Parceiros do armazém de dados do SQL Server business intelligence](sql-data-warehouse-partner-business-intelligence.md) |Apresenta uma lista de parceiros de intelligence de negócios de terceiros com soluções que suportam o SQL Data Warehouse. |
| 71 |[Parceiros de integração de dados do armazém de dados do SQL Server](sql-data-warehouse-partner-data-integration.md) |Apresenta uma lista de parceiros terceiros com soluções de integração de dados que suportam o Azure SQL Data Warehouse. |
| 72 |[Parceiros de gestão de dados do armazém de dados do SQL Server](sql-data-warehouse-partner-data-management.md) |Apresenta uma lista de parceiros de gestão de dados de terceiros com soluções que suportam o SQL Data Warehouse. |

## <a name="reference"></a>Referência
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 73 |[Tópicos de referência para o SQL Data Warehouse](sql-data-warehouse-overview-reference.md) |Ligações de conteúdo de referência para o SQL Data Warehouse. |
| 74 |[Cmdlets do PowerShell e APIs REST para o SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) |Localize os principais cmdlets do PowerShell para o Azure SQL Data Warehouse, incluindo como colocar em pausa e retomar uma base de dados. |
| 75 |[Elementos de linguagem](sql-data-warehouse-reference-tsql-language-elements.md) |Lista de ligações para conteúdo de referência para os elementos de linguagem de Transact-SQL utilizada para o SQL Data Warehouse. |
| 76 |[Tópicos de Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) |Ligações para conteúdo de referência para os tópicos de Transact-SQL utilizada pelo SQL Data Warehouse. |
| 77 |[Vistas de sistema](sql-data-warehouse-reference-tsql-system-views.md) |As ligações ao sistema vistas conteúdo para o SQL Data Warehouse. |

## <a name="security"></a>Segurança
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 78 |[Suportem de clientes de nível inferior do armazém de dados do SQL Server - para máscara de dados de auditoria e dinâmicos](sql-data-warehouse-auditing-downlevel-clients.md) |Saiba mais sobre o suporte de clientes de nível inferior do armazém de dados do SQL Server para auditoria de dados |
| 79 |[Auditoria no armazém de dados SQL do Azure](sql-data-warehouse-auditing-overview.md) |Introdução ao Azure SQL Data Warehouse de auditoria |
| 80 |[Começar com transparente dados encriptação (TDE) no SQL Data Warehouse](sql-data-warehouse-encryption-tde.md) |Encriptação de dados transparente (TDE) no SQL Data Warehouse |
| 81 |[Começar com encriptação de dados transparente (TDE)](sql-data-warehouse-encryption-tde-tsql.md) |Encriptação de dados transparente (TDE) no armazém de dados do SQL (T-SQL) |
| 82 |[Proteger uma base de dados no armazém de dados do SQL Server](sql-data-warehouse-overview-manage-security.md) |Sugestões para proteger uma base de dados no Azure SQL Data Warehouse para desenvolver soluções. |

## <a name="miscellaneous"></a>Diversos
| &nbsp; | Título | Descrição |
| ---:|:--- |:--- |
| 83 |[Instalar Visual Studio e SSDT para SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) |Instalar o Visual Studio e SQL Server Development Tools (SSDT) para o Azure SQL Data Warehouse |
| 84 |[Migração para detalhes de armazenamento Premium](sql-data-warehouse-migrate-to-premium-storage.md) |Instruções para migrar um armazém de dados existente do SQL Server para o premium storage |
| 85 |[Começar a utilizar a deteção de ameaças](sql-data-warehouse-security-threat-detection.md) |Como começar com a deteção de ameaças |
| 86 |[Limites de capacidade do SQL Data Warehouse](sql-data-warehouse-service-capacity-limits.md) |Valores máximos para ligações, bases de dados, tabelas e as consultas SQL do armazém de dados. |
| 87 |[Resolução de problemas do armazém de dados SQL do Azure](sql-data-warehouse-troubleshoot.md) |Resolução de problemas do armazém de dados SQL do Azure. |

