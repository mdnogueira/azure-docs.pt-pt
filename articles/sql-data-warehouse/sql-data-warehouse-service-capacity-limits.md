---
title: Os limites de capacidade do SQL Data Warehouse | Microsoft Docs
description: "Valores máximos para ligações, bases de dados, tabelas e as consultas SQL do armazém de dados."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: 52026a58a5b6e26a660f9e1374e67036c67ac525
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limites de capacidade do SQL Data Warehouse
As tabelas seguintes contêm os valores máximos permitidos para vários componentes do Azure SQL Data Warehouse.

## <a name="workload-management"></a>Gestão de cargas de trabalhos
| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| [Unidades do Data Warehouse (DWU)][Data Warehouse Units (DWU)] |DWU máx. para um SQL Data Warehouse único |6000 |
| [Unidades do Data Warehouse (DWU)][Data Warehouse Units (DWU)] |DWU máx. para um único servidor SQL |6000 por predefinição<br/><br/> Por predefinição, cada SQL server (por exemplo, myserver.database.windows.net) tem uma Quota de DTU de 45.000 que permite até 6000 DWU. Esta quota é apenas um limite de segurança. Pode aumentar a quota por [criar um pedido de suporte] [ creating a support ticket] e selecionando *Quota* como o tipo de pedido.  Para calcular a DTU tem, multiplique a 7.5 pelo total que DWU necessário. Pode ver o consumo de DTU atual a partir do painel SQL Server no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. |
| Ligação de base de dados |Sessões abertas em simultâneo |1024<br/><br/>Iremos suportar um máximo de ligações ativas 1024, cada um dos quais pode submeter pedidos para uma base de dados do armazém de dados do SQL Server ao mesmo tempo. Tenha em atenção que existem limites no número de consultas que, na verdade, pode executar em simultâneo. Quando o limite de concorrência for excedido, o pedido entra uma fila interna onde deve aguardar para ser processado. |
| Ligação de base de dados |Memória máxima instruções preparado |20 MB |
| [Gestão de carga de trabalho][Workload management] |Consultas em simultâneo máximas |32<br/><br/> Por predefinição, o SQL Data Warehouse pode ser executado um máximo de 32 consultas em simultâneo e filas restantes consultas.<br/><br/>O nível de concorrência pode diminuir quando os utilizadores são atribuídos a uma classe de recursos mais elevada ou quando o SQL Data Warehouse está configurado com baixa DWU. Algumas consultas, como consultas DMV, sempre estão autorizadas a executar. |
| [Tempdb][Tempdb] |Tamanho máx. de Tempdb |399 GB por DW100. Por conseguinte em DWU1000 Tempdb é dimensionados de forma a 3.99 TB |

## <a name="database-objects"></a>Objetos de base de dados
| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Base de Dados |Tamanho máx. |240 TB comprimido no disco<br/><br/>Este espaço é independente de espaço em tempdb ou de registo e, por conseguinte, este espaço dedicado para tabelas permanentes.  Compressão columnstore em cluster estima-se em 5 X.  Esta compressão permite que a base de dados aumente aproximadamente 1 PB quando todas as tabelas columnstore em cluster (o tipo de tabela predefinido). |
| Tabela |Tamanho máx. |60 TB comprimido no disco |
| Tabela |Tabelas por base de dados |2 mil milhões |
| Tabela |Colunas por tabela |1024 colunas |
| Tabela |Bytes por coluna |Depende de coluna [tipo de dados][data type].  Limite é 8000 para tipos de dados char, 4000 para nvarchar, ou 2 GB para os tipos de dados máxima. |
| Tabela |Bytes por linha, tamanho definido |8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma forma como está para o SQL Server com compressão de página. Como o SQL Server, o armazém de dados do SQL Server suporta o armazenamento de capacidade excedida de linha que lhe permite **colunas de comprimento variável** para fazer o Push consecutivos. Quando as linhas de comprimento variável são enviadas por push consecutivos, apenas 24 bytes raiz é armazenado no registo principal. Para obter mais informações, consulte o [excesso de linha dados exceder 8 KB] [ Row-Overflow Data Exceeding 8 KB] artigo da MSDN. |
| Tabela |Partições por tabela |15,000<br/><br/>Para elevado desempenho, recomendamos a minimizar o número de partições necessário enquanto ainda suportar os requisitos de negócio. À medida que cresce o número de partições, a sobrecarga para operações de linguagem de definição de dados (DDL) e de idioma de manipulação de dados (DML) que vai crescendo e faz com que um desempenho mais lento. |
| Tabela |Carateres por valor de limite de partição. |4000 |
| Índice |Índices não em cluster por tabela. |999<br/><br/>Aplica-se apenas a tabelas rowstore. |
| Índice |Índices em cluster por tabela. |1<br><br/>Aplica-se a tabelas rowstore e columnstore. |
| Índice |Tamanho de chave do índice. |900 bytes.<br/><br/>Aplica-se apenas a índices rowstore.<br/><br/>Os índices em colunas de varchar com um tamanho máximo de 900 bytes, mais do que podem ser criados se os dados existentes nas colunas não pode exceder 900 bytes quando o índice é criado. No entanto, inserir mais tarde ou ações de ATUALIZAÇÃO em colunas que fazer com que o tamanho total exceder os 900 bytes irão falhar. |
| Índice |Colunas de chave por índice. |16<br/><br/>Aplica-se apenas a índices rowstore. Índices columnstore em cluster incluem todas as colunas. |
| Estatísticas |Tamanho dos valores da coluna combinada. |900 bytes. |
| Estatísticas |Colunas por objeto de estatísticas. |32 |
| Estatísticas |Estatísticas criadas em colunas por tabela. |30,000 |
| Procedimentos Armazenados |Níveis de máximos de aninhamento. |8 |
| Vista |Colunas por vista |1,024 |

## <a name="loads"></a>Carrega
| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| O Polybase cargas |MB por linha |1<br/><br/>O Polybase cargas estão limitadas a carregar linhas de ambas as menor que 1MB e não é possível carregar VARCHR(MAX), nvarchar (Max) ou varbinary (Max).<br/><br/> |

## <a name="queries"></a>Consultas
| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Consulta |Consultas em fila em tabelas de utilizador. |1000 |
| Consulta |Consultas em simultâneo em vistas de sistema. |100 |
| Consulta |Consultas em fila em vistas de sistema |1000 |
| Consulta |Parâmetros máximos |2098 |
| Batch |Tamanho máximo |65,536*4096 |
| SELECIONADOS resultados |Colunas por linha |4096<br/><br/>Nunca pode ter mais de 4096 colunas por linha no resultado de SELECT. Não há nenhuma garantia que pode ter sempre 4096. Se o plano de consulta necessita de uma tabela temporária, podem ser aplicadas as colunas de 1024 por tabela máxima. |
| SELECIONE |Subconsultas aninhadas |32<br/><br/>Nunca pode ter mais do que 32 subconsultas aninhadas numa instrução SELECT. Não há nenhuma garantia que possam ter sempre a 32. Por exemplo, uma associação pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível. |
| SELECIONE |Colunas por associação |1024 colunas<br/><br/>Nunca pode ter mais de 1024 colunas na União. Não há nenhuma garantia que possam ter sempre a 1024. Se o plano de associação necessita de uma tabela temporária com mais colunas do que o resultado da JUNÇÃO, o limite de 1024 aplica-se a tabela temporária. |
| SELECIONE |Total de bytes por grupo por colunas. |8060<br/><br/>As colunas na cláusula GROUP BY podem ter um máximo de 8060 bytes. |
| SELECIONE |Bytes por colunas ORDER BY |8060 bytes.<br/><br/>As colunas na cláusula ORDER BY podem ter um máximo de 8060 bytes. |
| Os identificadores e constantes por declaração |Número de identificadores referenciados e constantes. |65,535<br/><br/>O SQL Data Warehouse limita o número de identificadores e constantes que podem ser incluídas sob uma única expressão de uma consulta. Este limite é de 65535. A exceder este número resulta num erro do SQL Server 8632. Para obter mais informações, consulte [erro interno: foi atingido um limite de serviços de expressão][Internal error: An expression services limit has been reached]. |

## <a name="metadata"></a>Metadados
| Vista de sistema | Linhas máximas |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Número total de trabalhadores DMS para as mais recentes 1000 pedidos de SQL. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |Número total de passos para os pedidos SQL mais recentes de 1000 que estão armazenados no sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |O SQL mais recente de 1000 pedidos que são armazenados no sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações de referência, consulte [descrição geral de referência do SQL Data Warehouse][SQL Data Warehouse reference overview].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050
