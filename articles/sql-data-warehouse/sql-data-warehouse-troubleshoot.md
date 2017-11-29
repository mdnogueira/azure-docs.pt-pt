---
title: "Resolução de problemas do armazém de dados SQL do Azure | Microsoft Docs"
description: "Resolução de problemas do armazém de dados SQL do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/30/2017
ms.author: kevin;barbkess
ms.openlocfilehash: c49b49cf832097bd5fbc423a36432a3eaff9bf14
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Resolução de problemas do armazém de dados SQL do Azure
Este tópico apresenta alguns das perguntas de resolução de problemas mais comuns que recebemos dos nossos clientes.

## <a name="connecting"></a>A ligação
| Problema | Resolução |
|:--- |:--- |
| Falha ao iniciar sessão para o utilizador 'Início de sessão do NT AUTHORITY\ANONYMOUS'. (Microsoft SQL Server, erro: 18456) |Este erro ocorre quando um utilizador do AAD tenta ligar à base de dados mestra, mas não tem um utilizador no mestre.  Para corrigir este problema é especificar o armazém de dados do SQL Server que pretende ligar ao tempo de ligação ou adicione o utilizador para a base de dados mestra.  Consulte [descrição geral de segurança] [ Security overview] artigo para obter mais detalhes. |
| O servidor principal "MyUserName" não é capaz de aceder a base de dados "master" no contexto de segurança atual. Não é possível abrir a base de dados do utilizador predefinida. O início de sessão falhou. Falha ao iniciar sessão para o utilizador 'MyUserName'. (Microsoft SQL Server, erro: 916) |Este erro ocorre quando um utilizador do AAD tenta ligar à base de dados mestra, mas não tem um utilizador no mestre.  Para corrigir este problema é especificar o armazém de dados do SQL Server que pretende ligar ao tempo de ligação ou adicione o utilizador para a base de dados mestra.  Consulte [descrição geral de segurança] [ Security overview] artigo para obter mais detalhes. |
| Erro CTAIP |Este erro pode ocorrer quando tiver sido criado um início de sessão no SQL server base de dados mestra, mas não se encontra na base de dados do armazém de dados do SQL Server.  Se ocorrer este erro, observe o [descrição geral de segurança] [ Security overview] artigo.  Este artigo explica como criar criar um início de sessão e o utilizador principal e, em seguida, como criar um utilizador na base de dados do armazém de dados do SQL Server. |
| Bloqueado pela Firewall |Bases de dados SQL do Azure estão protegidos por firewalls nível servidor e base de dados para garantir conhecido apenas endereços IP têm acesso a uma base de dados. As firewalls são seguras por predefinição, o que significa que, tem de ativar explicitamente e endereço IP ou intervalo de endereços antes de poder ligar.  Para configurar a firewall para acesso, siga os passos no [configurar o acesso ao servidor de firewall para o IP de cliente] [ Configure server firewall access for your client IP] no [aprovisionamento instruções] [Provisioning instructions]. |
| Não é possível estabelecer ligação com a ferramenta ou controladores |O SQL Data Warehouse recomenda a utilização [SSMS][SSMS], [SSDT para Visual Studio][SSDT for Visual Studio], ou [sqlcmd] [ sqlcmd] para consultar os dados. Para obter mais detalhes sobre controladores e a ligação ao SQL Data Warehouse, consulte [controladores para o Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] e [ligar ao Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] artigos. |

## <a name="tools"></a>Ferramentas
| Problema | Resolução |
|:--- |:--- |
| Explorador de objetos do Visual Studio está em falta utilizadores AAD |Este é um problema conhecido.  Como solução, ver os utilizadores [sys.database_principals][sys.database_principals].  Consulte [autenticação ao Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] para obter mais informações sobre como utilizar o Azure Active Directory com o SQL Data Warehouse. |
|Manual de scripting, utilizando o Assistente de script ou ligação através do SSMS estiver lentos, bloqueados ou produção erros| Certifique-se que os utilizadores foram criados na base de dados mestra. Nas opções de scripts, certifique-se também que a edição do motor está definida como "Microsoft Azure SQL Data Warehouse Edition" e tipo de motor é "Microsoft SQL Database do Azure".|

## <a name="performance"></a>Desempenho
| Problema | Resolução |
|:--- |:--- |
| Resolução de problemas de desempenho de consulta |Se estiver a tentar resolver problemas de uma consulta específica, comece por [aprender a monitorizar as suas consultas][Learning how to monitor your queries]. |
| Desempenho das consultas fraco e planos é frequentemente um resultado de estatísticas em falta |A causa mais comum de desempenho fraco é a falta de estatísticas das tabelas.  Consulte [manter as estatísticas da tabela] [ Statistics] para obter detalhes sobre como criar estatísticas e por que razão são críticas para o desempenho. |
| Simultaneidade baixa / colocados em fila de consultas |Noções sobre [gestão da carga de trabalho] [ Workload management] é importante para compreender como equilibrar a atribuição de memória com a simultaneidade. |
| Como implementar as melhores práticas |O melhor local para começar a aprender formas para melhorar o desempenho das consultas é [melhores práticas do SQL Data Warehouse] [ SQL Data Warehouse best practices] artigo. |
| Como melhorar o desempenho com dimensionamento |Por vezes, a solução para melhorar o desempenho é simplesmente adicionar mais computação power para as suas consultas por [dimensionamento do SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Desempenho das consultas fraco como resultado de qualidade de índice fraco |Consultas de algumas vezes podem slowdown porque [qualidade de índice columnstore fraco][Poor columnstore index quality].  Consulte este artigo para obter mais informações e como [reconstruir índices para melhorar a qualidade de segmento][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Gestão do sistema
| Problema | Resolução |
|:--- |:--- |
| Tarifas de mensagens 40847: Não foi possível efetuar a operação porque o servidor iria exceder a quota permitida da unidade de transação de base de dados de 45000. |Reduza o [DWU] [ DWU] da base de dados está a tentar criar ou [pedir um aumento de quota][request a quota increase]. |
| Investigar a utilização de espaço |Consulte [tabela tamanhos] [ Table sizes] para compreender a utilização do espaço do sistema. |
| Ajudar na gestão de tabelas |Consulte o [descrição geral da tabela] [ Overview] artigo para obter ajuda com a gerir as tabelas.  Este artigo também inclui ligações para tópicos mais detalhadas sobre como [tipos de dados de tabela][Data types], [distribuir uma tabela][Distribute], [Uma tabela de indexação][Index], [uma tabela de criação de partições][Partition], [manter as estatísticas da tabela] [ Statistics] e [tabelas temporárias][Temporary]. |
|Barra de progresso de encriptação (TDE) transparente de dados não está a atualizar no Portal do Azure|Pode ver o estado de TDE através de [powershell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>Polybase
| Problema | Resolução |
|:--- |:--- |
| Carga falha devido à grande linhas |Atualmente o suporte de linha grande não está disponível para o Polybase.  Isto significa que, se a tabela contiver varchar (Max), nvarchar (Max) ou varbinary (Max), as tabelas externas não podem ser utilizadas para carregar os dados.  Cargas grandes linhas está atualmente só é suportada através do Azure Data Factory (com o BCP), do Azure Stream Analytics, SSIS, BCP ou a classe de .NET SQLBulkCopy. Suporte do PolyBase linhas grande será adicionado numa versão futura. |
| está a falhar carga BCP da tabela com o tipo de dados de máx. |Não há um problema conhecido que requer que varchar (Max), nvarchar (Max) ou varbinary (Max) ser colocados no fim da tabela em alguns cenários.  Tente mover as colunas máx. para o fim da tabela. |

## <a name="differences-from-sql-database"></a>Diferenças a partir da base de dados SQL
| Problema | Resolução |
|:--- |:--- |
| Funcionalidades de base de dados SQL não suportadas |Consulte [tabela funcionalidades não suportadas][Unsupported table features]. |
| Tipos de dados de base de dados SQL não suportados |Consulte [não suportada de tipos de dados][Unsupported data types]. |
| ELIMINAR e limitações de ATUALIZAÇÃO |Consulte [soluções de ATUALIZAÇÃO][UPDATE workarounds], [eliminar soluções] [ DELETE workarounds] e [CTAS a utilizar para resolver o problema não suportado ATUALIZAÇÃO e Sintaxe de eliminação][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Instrução MERGE não é suportada |Consulte [soluções de intercalação][MERGE workarounds]. |
| Limitações do procedimento armazenado |Consulte [armazenados limitações do procedimento] [ Stored procedure limitations] para compreender algumas das limitações de procedimentos armazenados. |
| UDFs não suportam instruções SELECT |Esta é uma limitação atual do nosso UDFs.  Consulte [CREATE FUNCTION] [ CREATE FUNCTION] para a sintaxe é suportada. |

## <a name="next-steps"></a>Passos seguintes
Se estiver foram não é possível encontrar uma solução para o seu problema acima, seguem-se alguns outros recursos, pode tentar.

* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da equipa CAT]
* [Criar pedido de suporte]
* [Fórum do MSDN]
* [Fórum do Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Criar pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da equipa CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
