---
title: "Códigos de erro do SQL Server - erro de ligação de base de dados | Microsoft Docs"
description: "Saiba mais sobre os códigos de erro SQL para aplicações de cliente da base de dados SQL, como erros de ligação de base de dados comuns, problemas de cópia da base de dados e erros gerais. "
keywords: "Erro de ligação de base de dados do sql de acesso do código de erro do SQL Server, códigos de erro do SQL Server"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 2a23e4ca-ea93-4990-855a-1f9f05548202
ms.service: sql-database
ms.custom: develop apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: sstein
ms.openlocfilehash: 34e7142b5ca13ad8de5a4dbd380377abdf055c04
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Códigos de erro do SQL Server para as aplicações de cliente de base de dados SQL: base de dados de erros de ligação e outros problemas

Este artigo apresenta uma lista de códigos de erro SQL para aplicações de cliente da base de dados SQL, incluindo erros de ligação de base de dados, erros transitórios (também denominados falhas transitórias), erros de governação de recursos, problemas de cópia da base de dados, conjunto elástico e outros erros. A maioria das categorias são específicas para a SQL Database do Azure e não se aplicam ao Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erros de ligação de base de dados, erros transitórios e outros erros temporários
A tabela seguinte abrange os códigos de erro do SQL Server para erros de perda de ligação e outros erros transitórios que poderão surgir quando a aplicação tenta aceder à base de dados SQL. Para obter tutoriais de introdução no como ligar à SQL Database do Azure, consulte [ligar à SQL Database do Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erros de falhas transitórias e erros de ligação de base de dados mais comuns
A infraestrutura do Azure tem a capacidade de reconfigurar os servidores de forma dinâmica quando surgem cargas de trabalho pesadas no serviço de base de dados SQL.  Este comportamento dinâmico pode fazer com que o seu programa cliente percam a ligação à base de dados do SQL Server. Este tipo de condição de erro é chamado um *erro transitório*.

É vivamente recomendado que o seu programa cliente tem lógica de repetição para que este foi restabelecer uma ligação depois de dar a hora do erro transitório para corrigir si próprio.  Recomendamos-se que atrase para 5 segundos antes de repetir a primeira. Repetir após um atraso mais curto que riscos de 5 segundos overwhelming o serviço em nuvem. Para cada repetição subsequente o atraso deve aumentar exponencialmente, até um máximo de 60 segundos.

Erros de falhas transitórias normalmente manifestam como uma das seguintes mensagens de erro dos programas de cliente:

* Base de dados &lt;db_name&gt; no servidor &lt;Azure_instance&gt; não está atualmente disponível. Tente novamente a ligação mais tarde. Se o problema persistir, contacte o suporte ao cliente e forneça o ID de rastreio de sessão de &lt;session_id&gt;
* Base de dados &lt;db_name&gt; no servidor &lt;Azure_instance&gt; não está atualmente disponível. Tente novamente a ligação mais tarde. Se o problema persistir, contacte o suporte ao cliente e forneça o ID de rastreio de sessão de &lt;session_id&gt;. (Microsoft SQL Server, erro: 40613)
* Uma ligação existente foi forçado a fechar pelo anfitrião remoto.
* System.Data.Entity.Core.EntityCommandExecutionException: Ocorreu um erro ao executar a definição de comando. Consulte a exceção interna para obter mais detalhes. ---> System.Data.SqlClient.SqlException: Ocorreu um erro ao nível de transporte ao receber os resultados do servidor. (fornecedor: fornecedor de sessão, erro: 19 - ligação física não é utilizável)
* Uma tentativa de ligação para uma base de dados secundário falhou porque a base de dados está no processo reconfguration e este estiver ocupado aplicar novas páginas ao meio de um transation Active Directory na base de dados primária. 

Para obter exemplos de código de lógica de repetição, consulte:

* [Bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md) 
* [Ações para corrigir os erros de ligação e erros transitórios na base de dados do SQL Server](sql-database-connectivity-issues.md)

Ver um debate do *período de bloqueio* para clientes que utilizam ADO.NET está disponível no [SQL Server ligação agrupamento (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de erro de erro transitório
Os seguintes erros estão transitórios e devem ser repetidos na lógica da aplicação: 

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir a base de dados "%. & #x2a; ls" pedida pelo início de sessão. O início de sessão falha. |
| 40197 |17 |O serviço encontrou um erro ao processar o pedido. Tente novamente. Código de erro %d.<br/><br/>Recebe este erro quando o serviço está desativado devido a software ou atualizações de hardware, falhas de hardware ou outros problemas de ativação pós-falha. O código de erro (%d) incorporado a mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou a ativação pós-falha que ocorreram. Alguns exemplos de erro estão incorporados códigos de mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Restabelecer a ligação ao servidor da SQL Database automaticamente liga-o para uma cópia de bom estado de funcionamento da base de dados. A aplicação tem de detetar o código de erro incorporados (%d) dentro da mensagem para resolução de problemas no registo de 40197, erros e tente restabelecer a ligação à base de dados do SQL Server até que os recursos estão disponíveis e a ligação é estabelecida novamente. |
| 40501 |20 |O serviço está atualmente ocupado. Repita o pedido após 10 segundos. ID do incidente: %ls. Código: %d.<br/><br/>Para obter mais informações, consulte:<br/>• [Dos limites de recursos de base de dados do azure SQL](sql-database-service-tiers.md). |
| 40613 |17 |Base de dados '%. & #x2a; ls' no servidor '%. & #x2a; ls' não está atualmente disponível. Tente novamente a ligação mais tarde. Se o problema persistir, contacte o suporte ao cliente e forneça o ID de rastreio de sessão de '%. & #x2a; ls'. |
| 49918 |16 |Não é possível processar o pedido. Não existem recursos suficientes para processar o pedido.<br/><br/>O serviço está atualmente ocupado. Repita o pedido mais tarde. |
| 49919 |16 |Não é possível o processo de criar ou atualizar o pedido. Demasiado muitas criar ou atualizar operações em curso da subscrição "% ld".<br/><br/>O serviço está ocupado a processar vários criar ou atualizar pedidos para a sua subscrição ou o servidor. Pedidos estão atualmente bloqueados para a otimização de recursos. Consulta [operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Aguarde até pendentes criar ou pedidos de atualização estiverem concluídos ou elimine uma das suas pedidos pendentes e repita o pedido mais tarde. |
| 49920 |16 |Não é possível processar o pedido. Existem demasiadas operações em curso da subscrição "% ld".<br/><br/>O serviço está ocupado a processar vários pedidos para esta subscrição. Pedidos estão atualmente bloqueados para a otimização de recursos. Consulta [operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para o estado da operação. Aguarde até que os pedidos pendentes são concluída ou elimine uma das suas pedidos pendentes e repetir o pedido mais tarde. |
| 4221 |16 |Início de sessão para leitura secundária falhou devido a tempo espera 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. A réplica não está disponível para início de sessão porque as versões de linha estão em falta para transações que estavam em trânsito quando a réplica foi reciclada. É possível resolver o problema por reverter ou consolidar as transações ativas na réplica primária. As ocorrências esta condição podem ser minimizadas através de evitando transações de escrita longo principal. |

## <a name="database-copy-errors"></a>Erros de cópia da base de dados
Podem ser encontrados os seguintes erros ao copiar uma base de dados na base de dados do Azure SQL. Para mais informações, consulte [Copiar uma Base de Dados SQL do Azure](sql-database-copy.md).

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |Cliente com o endereço IP '%. & #x2a; ls' está temporariamente desativada. |
| 40637 |16 |Criar cópia da base de dados está atualmente desativada. |
| 40561 |16 |Falha na cópia da base de dados. Base de dados de origem ou de destino não existe. |
| 40562 |16 |Falha na cópia da base de dados. A base de dados de origem foi removida. |
| 40563 |16 |Falha na cópia da base de dados. A base de dados de destino foi removido. |
| 40564 |16 |Falha na cópia da base de dados devido a um erro interno. Remova a base de dados de destino e tente novamente. |
| 40565 |16 |Falha na cópia da base de dados. É permitida mais do que 1 cópia de base de dados em simultâneo a mesma origem. Remova a base de dados de destino e tente novamente mais tarde. |
| 40566 |16 |Falha na cópia da base de dados devido a um erro interno. Remova a base de dados de destino e tente novamente. |
| 40567 |16 |Falha na cópia da base de dados devido a um erro interno. Remova a base de dados de destino e tente novamente. |
| 40568 |16 |Falha na cópia da base de dados. Base de dados de origem ficou indisponível. Remova a base de dados de destino e tente novamente. |
| 40569 |16 |Falha na cópia da base de dados. Base de dados de destino ficou indisponível. Remova a base de dados de destino e tente novamente. |
| 40570 |16 |Falha na cópia da base de dados devido a um erro interno. Remova a base de dados de destino e tente novamente mais tarde. |
| 40571 |16 |Falha na cópia da base de dados devido a um erro interno. Remova a base de dados de destino e tente novamente mais tarde. |

## <a name="resource-governance-errors"></a>Erros de governação de recursos
Os seguintes erros são causados por utilização excessiva de recursos ao trabalhar com a SQL Database do Azure. Por exemplo:

* Uma transação foi aberta há demasiado tempo.
* Uma transação é que contém demasiados bloqueios.
* Uma aplicação está a consumir demasiada memória.
* Uma aplicação está a consumir muito `TempDb` espaço.

Tópicos relacionados:

* Informações mais detalhadas estão disponíveis aqui: [dos limites de recursos de base de dados do Azure SQL](sql-database-service-tiers.md).

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |ID de recurso: %d. O limite de %s para a base de dados é %d e foi atingido. Para obter mais informações, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>O ID de recurso indica o recurso que atingiu o limite. Para threads de trabalho, o ID do recurso = 1. Para sessões, o ID do recurso = 2.<br/><br/>Para obter mais informações sobre este erro e como resolvê-lo, consulte:<br/>• [Dos limites de recursos de base de dados do azure SQL](sql-database-service-tiers.md). |
| 10929 |20 |ID de recurso: %d. A garantia mínima %s é %d, limite máximo é %d e a utilização atual da base de dados é %d. No entanto, o servidor está atualmente demasiado ocupado para suportar pedidos de maiores %d para esta base de dados. Para obter mais informações, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Caso contrário, volte a tentar novamente mais tarde.<br/><br/>O ID de recurso indica o recurso que atingiu o limite. Para threads de trabalho, o ID do recurso = 1. Para sessões, o ID do recurso = 2.<br/><br/>Para obter mais informações sobre este erro e como resolvê-lo, consulte:<br/>• [Dos limites de recursos de base de dados do azure SQL](sql-database-service-tiers.md). |
| 40544 |20 |A base de dados atingiu a quota de tamanho. Particione ou elimine dados, remova índices ou consulte a documentação para encontrar possíveis resoluções. |
| 40549 |16 |Sessão foi terminada porque tem uma transação de longa execução. Tente encurtar a transação. |
| 40550 |16 |A sessão foi terminada porque obteve demasiados bloqueios. Tente ler ou modificar algumas linhas numa única transação. |
| 40551 |16 |A sessão foi terminada devido a excessivo `TEMPDB` utilização. Tente modificar a consulta para reduzir a utilização de espaço da tabela temporária.<br/><br/>Se estiver a utilizar objetos temporários, poupar espaço no `TEMPDB` base de dados, arrastando objetos temporários depois de já não são necessários por sessão. |
| 40552 |16 |A sessão foi terminada devido a utilização do espaço de registo de transações excessiva. Tente modificar algumas linhas numa única transação.<br/><br/>Se efetuar em massa insere utilizando o `bcp.exe` utilitário ou `System.Data.SqlClient.SqlBulkCopy` classe, tente utilizar o `-b batchsize` ou `BatchSize` opções para limitar o número de linhas copiado para o servidor em cada transação. Se estiver a reconstruir um índice com o `ALTER INDEX` declaração, tente utilizar o `REBUILD WITH ONLINE = ON` opção. |
| 40553 |16 |A sessão foi terminada devido a utilização de memória excessiva. Tente modificar a processar menos linhas da sua consulta.<br/><br/>Reduzir o número de `ORDER BY` e `GROUP BY` operações no seu código de Transact-SQL reduz os requisitos de memória da sua consulta. |

## <a name="elastic-pool-errors"></a>Erros de agrupamento elástico
Para criar e utilizar conjuntos elásticos relacionados com os seguintes erros:

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |O conjunto elástico atingiu o limite de armazenamento. A utilização do armazenamento do conjunto elástico não pode exceder (%d) MB. |Limite de espaço do conjunto elástico em MB. |A tentar escrever dados para uma base de dados quando for alcançado o limite de armazenamento do conjunto elástico. |É aconselhável aumentar as DTUs do conjunto elástico se possível, para aumentar o limite de armazenamento, reduzir o armazenamento utilizado pelas bases de dados individuais dentro do conjunto elástico ou remover bases de dados do conjunto elástico. |
| 10929 |EX_USER |A garantia mínima %s é %d, limite máximo é %d e a utilização atual da base de dados é %d. No entanto, o servidor está atualmente demasiado ocupado para suportar pedidos de maiores %d para esta base de dados. Consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obter assistência. Caso contrário, volte a tentar novamente mais tarde. |Mínimo de DTUS por base de dados; Máximo de DTUS por base de dados |O número total de trabalhadores em simultâneo (pedidos) em todas as bases de dados no agrupamento elástico tentou exceder o limite do conjunto. |É aconselhável aumentar as DTUs do conjunto elástico se possível, para aumentar o limite de trabalho ou remover bases de dados do conjunto elástico. |
| 40844 |EX_USER |Base de dados '%ls' no servidor '%ls' é uma base de dados de edição '%ls' num conjunto elástico e não pode ter uma relação a cópia contínua. |nome, edição, nome do servidor de base de dados |Um comando StartDatabaseCopy é emitido para uma base de dados não premium num agrupamento elástico. |Brevemente |
| 40857 |EX_USER |Conjunto elástico não encontrado para o servidor: '%ls', nome do conjunto elástico: '%ls'. |nome do servidor; nome do conjunto elástico |Agrupamento elástico especificado não existe no servidor especificado. |Forneça um nome de conjunto elástico válido. |
| 40858 |EX_USER |Agrupamento elástico de '%ls' já existe no servidor: '%ls' |nome do conjunto elástico, nome do servidor |Agrupamento elástico especificado já existe no servidor lógico especificado. |Forneça o nome do novo conjunto elástico. |
| 40859 |EX_USER |Conjunto elástico não suporta a camada de serviço '%ls'. |camada de serviço do conjunto elástico |Camada de serviço especificado não é suportada para o aprovisionamento do conjunto elástico. |Forneça a edição correta ou deixe em branco para utilizar o escalão de serviço predefinido a camada de serviço. |
| 40860 |EX_USER |Combinação de objetivo '%ls' de '%ls' e o serviço do conjunto elástico é inválida. |nome do conjunto elástico; nome de objetivo de nível de serviço |Elástico objetivo de agrupamento e serviço pode ser especificado em conjunto, apenas se o objetivo de serviço está especificado como 'ElasticPool'. |Especifique a combinação correta de conjunto elástico e o objetivo de serviço. |
| 40861 |EX_USER |A edição da base de dados ' %. *ls não podem ser diferentes da camada de serviço de conjunto elástico que é ' %.* ls'. |edição de base de dados, o escalão de serviço do conjunto elástico |A edição da base de dados é diferente da camada de serviço do conjunto elástico. |Não especifique uma edição de base de dados que é diferente de camada de serviço de conjunto elástico.  Tenha em atenção que a edição da base de dados não tem de ser especificado. |
| 40862 |EX_USER |Nome do conjunto elástico têm de ser especificado se o objetivo de serviço do conjunto elástico está especificado. |Nenhuma |Objetivo de serviço do conjunto elástico não identifica exclusivamente um conjunto elástico. |Especifique o nome do conjunto elástico se utilizar o objetivo de serviço do conjunto elástico. |
| 40864 |EX_USER |As DTUs do conjunto elástico tem de ser, pelo menos, (%d) DTUs no escalão de serviço ' %. * ls'. |DTUs do conjunto elástico; camada de serviço do conjunto elástico. |A tentar definir as DTUs do conjunto elástico abaixo o limite mínimo. |Volte a tentar definir as DTUs para elástico agrupamento para, pelo menos, o limite mínimo. |
| 40865 |EX_USER |As DTUs do conjunto elástico não podem exceder (%d) DTUs no escalão de serviço ' %. * ls'. |DTUs do conjunto elástico; camada de serviço do conjunto elástico. |A tentar definir as DTUs do conjunto elástico supera o limite máximo. |Repita a definição as DTUs do conjunto elástico não maior do que o limite máximo. |
| 40867 |EX_USER |O máximo de DTUS por base de dados tem de ser, pelo menos, (%d) no escalão de serviço ' %. * ls'. |Máximo de DTUS por base de dados; camada de serviço do conjunto elástico |A tentar definir o máximo de DTUS por base de dados abaixo do limite suportado. | onsider utilizando a camada de serviço do conjunto elástico que suporta a definição pretendida. |
| 40868 |EX_USER |O máximo de DTUS por base de dados não pode exceder (%d) no escalão de serviço ' %. * ls'. |Máximo de DTUS por base de dados; camada de serviço do conjunto elástico. |A tentar definir o máximo de DTUS por base de dados para além do limite suportado. | Considere utilizar a camada de serviço do conjunto elástico que suporta a definição pretendida. |
| 40870 |EX_USER |O mínimo de DTUS por base de dados não pode exceder (%d) no escalão de serviço ' %. * ls'. |Mínimo de DTUS por base de dados; camada de serviço do conjunto elástico. |A tentar definir o mínimo de DTUS por base de dados para além do limite suportado. | Considere utilizar a camada de serviço do conjunto elástico que suporta a definição pretendida. |
| 40873 |EX_USER |O número de bases de dados (%d) e o mínimo de DTUS por base de dados (%d) não pode exceder as DTUs do conjunto elástico (%d). |Número de bases de dados num conjunto elástico; Mínimo de DTUS por base de dados; DTUs do conjunto elástico. |A tentar especificar mínimo de DTUS para bases de dados no conjunto elástico excede as DTUs do conjunto elástico. | É aconselhável aumentar as DTUs do conjunto elástico ou diminuir o mínimo de DTUS por base de dados para reduzir o número de bases de dados no agrupamento elástico. |
| 40877 |EX_USER |Não é possível eliminar o conjunto elástico, a menos que não contém quaisquer bases de dados. |Nenhuma |O conjunto elástico contém um ou mais bases de dados e, por conseguinte, não pode ser eliminado. |Remova bases de dados do conjunto elástico para eliminá-lo. |
| 40881 |EX_USER |O conjunto elástico "%. * ls atingiu o limite de contagem de base de dados.  O limite de contagem de base de dados para o conjunto elástico não pode exceder (%d) para um agrupamento elástico com as DTUs (%d). |Nome do conjunto elástico; limite de contagem de base de dados do conjunto elástico; eDTUs de conjunto de recursos. |Tentativa de criar ou adicionar base de dados do agrupamento elástico quando for alcançado o limite de contagem de base de dados do conjunto elástico. | É aconselhável aumentar as DTUs do conjunto elástico se possível, para aumentar o limite de base de dados ou remova as bases de dados do conjunto elástico. |
| 40889 |EX_USER |As DTUs ou o limite de armazenamento para o conjunto elástico "%. * ls não podem ser diminuídos, uma vez que isso não forneceria espaço de armazenamento suficiente para as suas bases de dados. |Nome do conjunto elástico. |A tentar diminuir o limite de armazenamento do conjunto elástico abaixo respetiva utilização de armazenamento. | Considere a possibilidade de reduzir a utilização do armazenamento de bases de dados individuais num conjunto elástico ou remover bases de dados do agrupamento para reduzir o respetivo DTUs ou o limite de armazenamento. |
| 40891 |EX_USER |O mínimo de DTUS por base de dados (%d) não pode exceder o máximo de DTUS por base de dados (%d). |Mínimo de DTUS por base de dados; Máximo de DTUS por base de dados. |A tentar definir o mínimo de DTUS por base de dados superior ao máximo de DTUS por base de dados. |Certifique-se de que o mínimo de DTUS por bases de dados não pode exceder o máximo de DTUS por base de dados. |
| TBD |EX_USER |O tamanho de armazenamento para uma base de dados individual num conjunto elástico não pode exceder o tamanho máximo permitido por ' %. * ls agrupamento elástico de camada de serviço. |camada de serviço do conjunto elástico |O tamanho máximo para a base de dados excede o tamanho máximo permitido pela camada de serviço de conjunto elástico. |Defina o tamanho máximo da base de dados dentro dos limites de tamanho máximo permitido pela camada de serviço de conjunto elástico. |

Tópicos relacionados:

* [Criar um conjunto elástico (c#)](sql-database-elastic-pool-manage-csharp.md) 
* [Gerir um conjunto elástico (c#)](sql-database-elastic-pool-manage-csharp.md). 
* [Criar um conjunto elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Monitorizar e gerir um conjunto elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Erros gerais
Os seguintes erros não enquadram-se em qualquer categorias anteriores.

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) não é um nome válido porque contém carateres inválidos. |
| 18452 |14 |Falha ao iniciar sessão. O início de sessão é proveniente de um domínio não fidedigno e não pode ser utilizado com o Windows authentication.%. & #x2a; ls (inícios de sessão do Windows não são suportados nesta versão do SQL Server.) |
| 18456 |14 |Falha de início de sessão para o utilizador ' %. & #x2a;ls'.%. & #x2a % ls. & #x2a; ls (falha de início de sessão do utilizador "%. & #x2a; ls". A alteração da palavra-passe falhou. Alteração de palavra-passe durante o início de sessão não é suportada nesta versão do SQL Server.) |
| 18470 |14 |Falha de início de sessão para o utilizador '%. & #x2a; ls'. Razão: A conta é disabled.%. & #x2a; ls |
| 40014 |16 |Não não possível utilizar várias bases de dados na mesma transação. |
| 40054 |16 |As tabelas sem um índice em cluster não são suportadas nesta versão do SQL Server. Criar um índice em cluster e tente novamente. |
| 40133 |15 |Esta operação não é suportada nesta versão do SQL Server. |
| 40506 |16 |O SID especificado é inválido para esta versão do SQL Server. |
| 40507 |16 |' %. & #x2a; ls não não possível invocar com parâmetros nesta versão do SQL Server. |
| 40508 |16 |A instrução USE não é suportada para alternar entre bases de dados. Utilize uma nova ligação para ligar a uma base de dados diferente. |
| 40510 |16 |Instrução '%. & #x2a; ls' não é suportada nesta versão do SQL Server |
| 40511 |16 |A função incorporada '%. & #x2a; ls' não é suportada nesta versão do SQL Server. |
| 40512 |16 |Funcionalidade despromovida '%ls' não é suportada nesta versão do SQL Server. |
| 40513 |16 |Servidor variável '%. & #x2a; ls' não é suportada nesta versão do SQL Server. |
| 40514 |16 |'%ls' não é suportada nesta versão do SQL Server. |
| 40515 |16 |Referência ao nome de base de dados e/ou servidor em '%. & #x2a; ls' não é suportada nesta versão do SQL Server. |
| 40516 |16 |Os objetos temp globais não são suportados nesta versão do SQL Server. |
| 40517 |16 |Opção de palavra-chave ou instrução '%. & #x2a; ls' não é suportada nesta versão do SQL Server. |
| 40518 |16 |O comando DBCC '%. & #x2a; ls' não é suportada nesta versão do SQL Server. |
| 40520 |16 |A classe com capacidade de segurança '% S_MSG' não é suportada nesta versão do SQL Server. |
| 40521 |16 |A classe com capacidade de segurança '% S_MSG' não é suportada no âmbito do servidor nesta versão do SQL Server. |
| 40522 |16 |Tipo de base de dados principal '%. & #x2a; ls' não é suportado nesta versão do SQL Server. |
| 40523 |16 |Criação de utilizador implícito '%. & #x2a; ls' não é suportada nesta versão do SQL Server. Crie explicitamente o utilizador antes de o utilizar. |
| 40524 |16 |Tipo de dados '%. & #x2a; ls' não é suportada nesta versão do SQL Server. |
| 40525 |16 |COM '%.ls' não é suportada nesta versão do SQL Server. |
| 40526 |16 |' %. & #x2a; o fornecedor de conjunto de linhas dos ls não é suportado nesta versão do SQL Server. |
| 40527 |16 |Servidores ligados não são suportados nesta versão do SQL Server. |
| 40528 |16 |Os utilizadores não podem ser mapeados para certificados, chaves assimétricas ou inícios de sessão do Windows nesta versão do SQL Server. |
| 40529 |16 |A função incorporada '%. & #x2a; ls' em representação contexto não é suportado nesta versão do SQL Server. |
| 40532 |11 |Não é possível abrir o servidor "%. & #x2a; ls" pedida pelo início de sessão. O início de sessão falha. |
| 40553 |16 |A sessão foi terminada devido a utilização de memória excessiva. Tente modificar a processar menos linhas da sua consulta.<br/><br/> Reduzir o número de `ORDER BY` e `GROUP BY` operações no seu código de Transact-SQL ajuda a reduzir os requisitos de memória da sua consulta. |
| 40604 |16 |Foi não CREATE/ALTER DATABASE porque iria exceder a quota do servidor. |
| 40606 |16 |A expor bases de dados não é suportada nesta versão do SQL Server. |
| 40607 |16 |Inícios de sessão do Windows não são suportados nesta versão do SQL Server. |
| 40611 |16 |Os servidores podem ter no máximo 128 regras de firewall definidas. |
| 40614 |16 |O endereço IP inicial da regra da firewall não pode exceder o endereço IP final. |
| 40615 |16 |Não é possível abrir o servidor '{0}' pedido pelo início de sessão. Cliente com o endereço IP '{1}' não tem permissão para aceder ao servidor.<br /><br />Para ativar o acesso, utilize o Portal de base de dados do SQL Server ou execute SP2\_definir\_firewall\_regra na base de dados mestra para criar uma regra de firewall para este endereço IP ou intervalo de endereços. Pode demorar até cinco minutos para que esta alteração tenha efeito. |
| 40617 |16 |O firewall nome da regra que começa com (nome da regra) é demasiado longo. Comprimento máximo é 128. |
| 40618 |16 |O nome da regra de firewall não pode estar vazio. |
| 40620 |16 |Falha de início de sessão do utilizador "%. & #x2a; ls". A alteração da palavra-passe falhou. Alteração de palavra-passe durante o início de sessão não é suportada nesta versão do SQL Server. |
| 40627 |20 |A operação no servidor '{0}' e da base de dados '{1}' está em curso. Aguarde alguns minutos antes de tentar novamente. |
| 40630 |16 |Falha na validação da palavra-passe. A palavra-passe não cumpre os requisitos de política porque é demasiado pequeno. |
| 40631 |16 |A palavra-passe que especificou é demasiado longa. A palavra-passe deve ter mais do que 128 carateres. |
| 40632 |16 |Falha na validação da palavra-passe. A palavra-passe não cumpre os requisitos de política porque não é suficientemente complexa. |
| 40636 |16 |Não é possível utilizar um nome de base de dados reservado '%. & #x2a; ls' nesta operação. |
| 40638 |16 |Id de subscrição inválido (id de subscrição). Subscrição não existe. |
| 40639 |16 |Pedido não é compatível com o esquema: (erro do esquema). |
| 40640 |20 |O servidor encontrou uma exceção inesperada. |
| 40641 |16 |A localização especificada é inválida. |
| 40642 |17 |O servidor está atualmente demasiado ocupado. Tente novamente mais tarde. |
| 40643 |16 |O valor de cabeçalho x-ms-version especificado é inválido. |
| 40644 |14 |Falha ao autorizar o acesso à subscrição especificada. |
| 40645 |16 |ServerName (servername) não pode estar vazio nem ser nulo. -Pode apenas ser constituído por letras minúsculas 'um'-'z', números 0-9 e o hífen. O hífen poderá não colocado no início ou o nome. |
| 40646 |16 |ID de subscrição não pode estar vazio. |
| 40647 |16 |Subscrição (id de subscrição) não tem o servidor (servername). |
| 40648 |17 |Demasiados pedidos por tem sido executados. Tente novamente mais tarde. |
| 40649 |16 |Foi especificado um tipo de conteúdo inválido. Application/xml só é suportado. |
| 40650 |16 |Subscrição (id de subscrição) não existe ou não está pronta para a operação. |
| 40651 |16 |Falha ao criar o servidor porque a subscrição (id de subscrição) está desativada. |
| 40652 |16 |Não é possível mover ou criar o servidor. Subscrição (id de subscrição) irá exceder a quota do servidor. |
| 40671 |17 |Falha de comunicação entre o gateway e o serviço de gestão. Tente novamente mais tarde. |
| 40852 |16 |Não é possível abrir a base de dados ' %. \*ls' no servidor ' %. \*ls pedida pelo início de sessão. Acesso à base de dados só é permitido a utilização de uma cadeia de ligação com segurança ativada. Para aceder a esta base de dados, modifique as cadeias de ligação para conter 'segura' no servidor FQDN -.database.windows 'nome do servidor'.net deve ser modificado para .database 'nome do servidor'. `secure`. windows.net. |
| 40914 | 16 | Não é possível abrir o servidor '*[nome do servidor]*' pedido pelo início de sessão. Cliente não tem permissão para aceder ao servidor.<br /><br />Para corrigir, considere adicionar um [regra de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |O sistema do SQL Azure está sob carga e é colocando um limite superior no operações CRUD de base de dados em simultâneo para um único servidor (por exemplo, criar base de dados). O servidor especificado na mensagem de erro foi excedido o número máximo de ligações em simultâneo. Tente novamente mais tarde. |
| 45169 |16 |O sistema SQL do azure está em carga e é colocando um limite superior no número de operações CRUD de servidor em simultâneo para uma única subscrição (por exemplo, criar servidor). A subscrição especificada na mensagem de erro foi excedido o número máximo de ligações em simultâneo, e o pedido foi negado. Tente novamente mais tarde. |

## <a name="next-steps"></a>Passos seguintes
* Leia sobre [funcionalidades de base de dados SQL do Azure](sql-database-features.md).
* Leia sobre [escalões de serviço](sql-database-service-tiers.md).

