---
title: Os registos do servidor na base de dados do Azure para PostgreSQL | Microsoft Docs
description: Gera registos de consulta e erro na base de dados do Azure para PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 696af85cd5609171a719a7e77efbfcdeba0aaaaa
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Os registos do servidor na base de dados do Azure para PostgreSQL 
Base de dados do Azure para PostgreSQL gera o erro de consulta e registos. No entanto, o acesso aos registos de transações não é suportado. Os registos de consulta e o erro podem ser utilizados para identificar, resolver problemas e reparar erros de configuração e o desempenho inferior ao ideal. Para obter mais informações, consulte [relatório de erros e registo](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Aceder a registos do servidor
Pode listar e transferir os registos de erros do Azure PostgreSQL no portal do Azure, [CLI do Azure](howto-configure-server-logs-using-cli.md)e APIs REST do Azure.

## <a name="log-retention"></a>Retenção do registo
Pode definir o período de retenção para utilizar o sistema regista o **registo\_retenção\_período** parâmetro associado ao servidor. A unidade para este parâmetro é dias. O valor predefinido é 3 dias. O valor máximo é de 7 dias. O servidor tem de ter espaço suficiente atribuído armazenamento que contém os ficheiros de registo retidos.
Roda os ficheiros de registo para a cada uma hora ou 100 MB de tamanho, o que ocorrer primeiro.

## <a name="configure-logging-for-azure-postgresql-server"></a>Configurar o registo para o servidor de PostgreSQL do Azure
Pode ativar o registo de consultas e registo de erros para o servidor. Os registos de erros podem conter informações vacuum automática, a ligação e a pontos de verificação.

Pode ativar o registo de consulta para a instância de base de dados PostgreSQL definindo dois parâmetros de servidor: `log\_statement` e `log\_min\_duration\_statement`.

O **registo\_instrução** parâmetro controla quais instruções SQL estão registadas. Recomendamos que defina este parâmetro ***todos os*** para registar todas as instruções; o valor predefinido é none.

O **registo\_min\_duração\_instrução** parâmetro define o limite em milissegundos de uma instrução que será registado. Todas as instruções do SQL Server com mais do que a definição do parâmetro são registadas. Este parâmetro é desativado e definido para menos 1 (-1) por predefinição. Ativar este parâmetro pode ser útil no controlo de consultas não otimizadas nas suas aplicações.

O **registo\_min\_mensagens** permite-lhe controlar que níveis de mensagens é escritos no registo de servidor. A predefinição é de aviso. 

Para obter mais informações sobre estas definições, consulte [relatório de erros e registo](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) documentação. Para configurar particularmente base de dados do Azure para os parâmetros de servidor PostgreSQL, consulte [personalizar parâmetros de configuração de servidor utilizando a CLI do Azure](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Passos seguintes
- Para aceder a registos utilizando a interface de linha de comandos da CLI do Azure, consulte o artigo [acesso e configurar os registos do servidor com a CLI do Azure](howto-configure-server-logs-using-cli.md).
- Para obter mais informações sobre os parâmetros de servidor, consulte [personalizar parâmetros de configuração de servidor utilizando a CLI do Azure](howto-configure-server-parameters-using-cli.md).
