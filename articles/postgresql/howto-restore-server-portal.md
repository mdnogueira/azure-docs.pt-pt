---
title: Como restaurar um servidor na base de dados do Azure para PostgreSQL | Microsoft Docs
description: Este artigo descreve como restaurar um servidor na base de dados do Azure para PostgreSQL no portal do Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 903fd2ff446e1963ab5cfcec745766188b74efcf
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Como criar cópias de segurança e restaurar um servidor na base de dados do Azure para PostgreSQL no portal do Azure

## <a name="backup-happens-automatically"></a>Cópia de segurança ocorre automaticamente
Quando utilizar a base de dados do Azure para PostgreSQL, o serviço de base de dados efetua automaticamente uma cópia de segurança do servidor a cada 5 minutos. 

As cópias de segurança estão disponíveis para 7 dias, ao utilizar o escalão básico e 35 dias ao utilizar o escalão Standard. Para obter mais informações, consulte [base de dados do Azure para PostgreSQL escalões de serviço](concepts-service-tiers.md)

Utilizar esta funcionalidade de cópia de segurança automática, pode restaurar o servidor e todas as suas bases de dados para um novo servidor para um ponto no tempo anterior.

## <a name="restore-in-the-azure-portal"></a>Restauro no portal do Azure
Base de dados do Azure para PostgreSQL permite-lhe restaurar o servidor novamente para um ponto no tempo e em para uma nova cópia do servidor. Pode utilizar este servidor novo para recuperar os dados. 

Por exemplo, se uma tabela estava acidentalmente removido hoje em dia, ao meio-dia foi possível restaurar para a hora imediatamente antes ao meio-dia e obter a tabela em falta e os dados a partir dessa nova cópia do servidor.

Os seguintes passos restaurar o servidor de exemplo para um ponto no tempo:
1. Inicie sessão no [portal do Azure](https://portal.azure.com/)
2. Localize a base de dados do Azure para o servidor de PostgreSQL. No portal do Azure, clique em **todos os recursos** do menu da esquerda e escreva o nome do servidor, tais como **mypgserver 20170401**, para procurar o servidor existente. Clique no nome do servidor listado no resultado da pesquisa. É apresentada a página **Descrição Geral** do servidor, que fornece opções para configuração adicional.

   ![Portal do Azure - pesquisa para localizar o seu servidor](media/postgresql-howto-restore-server-portal/1-locate.png)

3. Na barra de ferramentas da página de descrição geral do servidor, clique em **restaurar**. Abre a página de restauro.

   ![Base de dados do Azure para restauro PostgreSQL - descrição geral - botão](./media/postgresql-howto-restore-server-portal/2_server.png)

4. Preencha o formulário de restauro com as informações necessárias:

   ![Base de dados do Azure para PostgreSQL - informações de restauro ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - **Ponto de restauro**: selecione um ponto no tempo que ocorre antes do servidor foi alterado.
  - **Servidor de destino**: forneça um novo nome de servidor que pretende restaurar.
  - **Localização**: não é possível selecionar a região. Por predefinição é igual ao servidor de origem.
  - **Escalão de preço**: não é possível alterar este valor ao restaurar um servidor. É igual ao servidor de origem. 

5. Clique em **OK** para restaurar o servidor ao restaurar para um ponto no tempo. 

6. Depois do restauro concluir, localize o novo servidor que é criado para verificar que os dados foram restaurados conforme esperado.

## <a name="next-steps"></a>Passos seguintes
- [Bibliotecas de ligação para base de dados do Azure para PostgreSQL](concepts-connection-libraries.md)
