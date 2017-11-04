---
title: "Ativar a encriptação transparente de dados para a Stretch Database - Azure | Microsoft Docs"
description: "Ativar a encriptação de dados transparente (TDE) para o SQL Server Stretch Database no Azure"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: barbkess
editor: 
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: ceb355d2ba872ed5d3886c6dc82ca75b1854db0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Ativar a encriptação de dados transparente (TDE) para a Stretch Database no Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Encriptação de dados transparente (TDE) ajuda a proteger contra a ameaça de atividade maliciosa através de encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e os ficheiros de registo de transações Inativos sem necessidade de alterações à aplicação.

TDE encripta o armazenamento de uma base de dados completa através de uma chave simétrica chamada a chave de encriptação da base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporada. O certificado de servidor incorporada é exclusivo para cada servidor do Azure. Microsoft roda automaticamente estes certificados, pelo menos, todos os 90 dias. Para obter uma descrição geral de TDE, consulte [encriptação de dados transparente (TDE)].

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar a TDE para um Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

1. Abrir a base de dados no [portal do Azure](https://portal.azure.com)
2. No painel da base de dados, clique o **definições** botão
3. Selecione o **encriptação transparente de dados** opção![][1]
4. Selecione o **no** definição e, em seguida, selecione **guardar**
   ![][2]

## <a name="disabling-encryption"></a>A desativação da encriptação
Para desativar o TDE para um Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

1. Abrir a base de dados no [portal do Azure](https://portal.azure.com)
2. No painel da base de dados, clique o **definições** botão
3. Selecione o **encriptação transparente de dados** opção
4. Selecione o **desativar** definição e, em seguida, selecione **guardar**

<!--Anchors-->
[encriptação de dados transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
