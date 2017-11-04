---
title: Os registos do servidor da base de dados do Azure para MySQL | Microsoft Docs
description: "Descreve os registos disponíveis na base de dados do Azure para o MySQL e os parâmetros disponíveis de ativação de níveis de registo diferentes."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 13b30df82c1a6c4c45a621a1f7a40148a55a7648
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Os registos do servidor na base de dados do Azure para MySQL
Na base de dados do Azure para MySQL, o registo de consultas lenta está disponível para os utilizadores. Acesso ao registo de transação não é suportado. O registo de consultas lenta pode ser utilizado para identificar estrangulamentos de desempenho para resolução de problemas. 

Para mais informações sobre o registo de consultas lenta de MySQL, consulte o manual de referência de MySQL [lenta da secção de registo de consulta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Aceder a registos do servidor
Pode listar e transferir a base de dados do Azure para os registos do servidor de MySQL, utilizando o portal do Azure e a CLI do Azure.

No portal do Azure, selecione a base de dados do Azure para o servidor de MySQL. Sob o **monitorização** cabeçalho, selecione o **registos do servidor** página.

Para obter mais informações sobre a CLI do Azure, consulte [acesso e configurar os registos do servidor com a CLI do Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Retenção do registo
Estão disponíveis registos de sete dias da respetiva criação. Se o tamanho total dos registos disponíveis exceder 7.5 GB, em seguida, os ficheiros mais antigos são eliminados até espaço esteja disponível. 

Os registos rodam cada 24 horas ou 7.5 GB, o que ocorrer primeiro.


## <a name="configure-logging"></a>Configurar o registo 
Por predefinição, o registo de consultas lenta está desativado. Para ativar, defina slow_query_log como ON.

Outros parâmetros que pode ajustar incluem:

- **long_query_time**: se uma consulta demora mais que long_query_time (em segundos) que consulta é registada. A predefinição é 10 segundos.
- **log_slow_admin_statements**: se ON inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções escritas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não utilizem índices com sessão iniciadas a slow_query_log
- **log_throttle_queries_not_using_indexes**: este parâmetro limita o número de consultas de índice não pode ser escrito para o registo de consultas lenta. Este parâmetro entra em vigor quando log_queries_not_using_indexes está definida como ON.

Consulte o MySQL [lenta documentação de registo de consulta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) para descrições completas dos parâmetros de registo de consulta lenta.

## <a name="next-steps"></a>Passos Seguintes
- [Como configurar e os registos do servidor de acesso a partir da CLI do Azure](howto-configure-server-logs-in-cli.md).
