---
title: Como restaurar um servidor na base de dados do Azure para MySQL | Microsoft Docs
description: Este artigo descreve como restaurar um servidor na base de dados do Azure para o MySQL no portal do Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6c1c0f8a0c0e59661b70b787b551b8cfdb024cda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como criar cópias de segurança e restaurar um servidor na base de dados do Azure para MySQL utilizando o portal do Azure

## <a name="backup-happens-automatically"></a>Cópia de segurança ocorre automaticamente
Quando utilizar a base de dados do Azure para MySQL, o serviço de base de dados efetua automaticamente uma cópia de segurança do serviço de cada cinco minutos. 

As cópias de segurança estão disponíveis para 7 dias, ao utilizar o escalão básico e 35 dias ao utilizar o escalão Standard. Para obter mais informações, consulte [base de dados do Azure para os escalões de serviço de MySQL](concepts-service-tiers.md)

Utilizar esta funcionalidade de cópia de segurança automática, pode restaurar o servidor e todas as suas bases de dados para um novo servidor para um ponto no tempo anterior.

## <a name="restore-in-the-azure-portal"></a>Restauro no portal do Azure
Base de dados do Azure para MySQL permite-lhe restaurar o servidor para um ponto específico no tempo e em para uma nova cópia do servidor. Pode utilizar este servidor novo para recuperar os dados. 

Por exemplo, se uma tabela acidentalmente foi removida hoje ao meio-dia, foi possível restaurar para uma hora imediatamente antes ao meio-dia e obter a tabela em falta e os dados da nova cópia do servidor.

Os seguintes passos restaurar o servidor de exemplo para um ponto específico no tempo:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/)

2. Localize a base de dados do Azure para o servidor de MySQL. No painel esquerdo, selecione **todos os recursos**e, em seguida, selecione o servidor da lista.

3.  No topo do painel de descrição geral do servidor, clique em **restaurar** na barra de ferramentas. É aberto o painel de restauro.
![Clique no botão de restauro](./media/howto-restore-server-portal/click-restore-button.png)

4. Preencha o formulário de restauro com as informações necessárias:

- **(UTC) do ponto de restauro**: utilizando o selecionador de data e o selecionador de tempo, selecione um ponto no tempo para restaurar. O período de tempo especificado está em formato UTC, por isso, provavelmente, terá de converter a hora local em UTC.
- **Restaurar para o novo servidor**: forneça um novo nome de servidor para restaurar o servidor existente para.
- **Localização**: A opção de região preenche automaticamente com a região de servidor de origem e não pode ser alterada.
- **Escalão de preço**: A opção de escalão de preço preenche automaticamente com o escalão de preço mesmo que o servidor de origem e não podem ser alterada aqui. 
![Restauro PITR](./media/howto-restore-server-portal/pitr-restore.png)

5. Clique em **OK** para restaurar o servidor para o ponto especificado no tempo. 

6. Após o restauro, localize o novo servidor que foi criado e, em seguida, certifique-se de que as bases de dados foram restaurados conforme esperado.

## <a name="next-steps"></a>Passos seguintes
- [Bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md).