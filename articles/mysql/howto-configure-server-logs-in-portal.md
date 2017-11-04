---
title: Configurar e aceder aos registos do servidor da base de dados do Azure para o MySQL no Azure Portal | Microsoft Docs
description: Este artigo descreve como configurar e aceder os registos do servidor na base de dados do Azure para MySQL do Portal do Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 89674c133b458c16fbdacd771be24830624dde7c
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurar e iniciar sessão no servidor de acesso no portal do Azure

Pode configurar, lista e transferir o [base de dados do Azure para os registos do servidor MySQL](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
- [Base de dados do Azure para o servidor de MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configurar o registo
Configure o acesso para o registo de consultas lenta de MySQL. 

1. Inicie sessão no [Portal do Azure](http://portal.azure.com/).

2. Selecione a base de dados do Azure para o servidor de MySQL.

3. Sob o **monitorização** secção na barra lateral, selecione **registos do servidor**. 
   ![Selecionados os registos do servidor, clique aqui para configurar](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecione o título **clique aqui para ativar os registos e configurar parâmetros de registo** para ver os parâmetros de servidor.

5. Selecione o **mostrar mais** expander para ver uma lista dos parâmetros disponíveis expandida. 

   Para obter mais informações sobre as definições de parâmetros, consulte a documentação de MySQL no [registos](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

   ![Clique em Mostrar mais para a lista mais extensa](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Altere os parâmetros que terá de ajustar. Todas as alterações efetuadas nesta sessão são realçadas na roxa. 

   Assim que tiver alterado os parâmetros, pode clicar em **guardar**. Ou pode **rejeitar** as suas alterações.

   ![Clique em guardar ou eliminar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. Regressar à lista de registos, clicando a **botão Fechar** (X ícone) no **parâmetros do** página.

## <a name="view-list-and-download-logs"></a>Ver lista e transferir os registos
Depois de inicia o registo, pode ver uma lista de registos disponíveis e transferir ficheiros de registo individuais no painel de registos do servidor. 

1. Abra o portal do Azure.

2. Selecione a base de dados do Azure para o servidor de MySQL.

3. Sob o **monitorização** secção na barra lateral, selecione **registos do servidor**. A página mostra uma lista dos ficheiros de registo, conforme mostrado:

   ![Lista de registos](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A Convenção de nomenclatura do registo é **mysql - lenta - < o nome do servidor >-yyyymmddhh.log**. A data e hora utilizado no nome do ficheiro é a hora quando o registo foi emitido. Ficheiros de registo rodam cada 24 horas ou 7.5 GB, o que ocorrer primeiro.

4. Se necessário, utilize o **caixa pesquisa** para restringir rapidamente um registo específicos com base na data/hora. A pesquisa está no nome do registo.

5. Transferir ficheiros de registo individuais utilizando o **transferir** botão (inativo ícone de seta) junto a cada ficheiro de registo na linha da tabela, conforme mostrado:

   ![Clique em transferir ícone](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Passos seguintes
- Consulte [registos do servidor de acesso na CLI](howto-configure-server-logs-in-cli.md) para saber como transferir os registos através de programação.
- Saiba mais sobre [registos do servidor](concepts-server-logs.md) na base de dados do Azure para MySQL. 
- Para obter mais informações sobre as definições de parâmetros e o MySQL registo, consulte a documentação de MySQL no [registos](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

