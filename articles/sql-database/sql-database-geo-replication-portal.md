---
title: "Portal do Azure: georreplicação de base de dados SQL | Microsoft Docs"
description: "Configurar georreplicação para a SQL Database do Azure no portal do Azure e iniciação de ativação pós-falha"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 03/06/2016
ms.author: carlrab
ms.openlocfilehash: d880a2f1aef3fa1bfc824f14650088bac4cb8693
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configurar a georreplicação ativa para a SQL Database do Azure no portal do Azure e iniciação de ativação pós-falha

Este artigo mostra como configurar a georreplicação ativa para base de dados do SQL Server no [portal do Azure](http://portal.azure.com) e para iniciar a ativação pós-falha.

Para iniciar a ativação pós-falha com o portal do Azure, consulte [inicie uma ativação pós-falha planeada ou não planeada para a SQL Database do Azure com o portal do Azure](sql-database-geo-replication-portal.md).

Para configurar a replicação geográfica activa, utilizando o portal do Azure, é necessário o seguinte recurso:

* Uma base de dados SQL do Azure: A base de dados principal que pretende replicar para uma região geográfica diferentes.

> [!Note]
Replicação geográfica activa tem de ser entre bases de dados na mesma subscrição.

## <a name="add-a-secondary-database"></a>Adicionar uma base de dados secundária
Os seguintes passos criar uma nova base de dados secundária numa parceria de georreplicação.  

Para adicionar uma base de dados secundária, tem de ser o proprietário da subscrição ou coproprietário.

A base de dados secundária tem o mesmo nome que a base de dados primária e tem, por predefinição, o mesmo nível de serviço. A base de dados secundária pode ser uma base de dados ou uma base de dados num agrupamento elástico. Para obter mais informações, consulte [escalões de serviço](sql-database-service-tiers.md).
Depois do elemento secundário é criado e pré-propagadas, dados começa a replicar da base de dados primária para a nova base de dados secundária.

> [!NOTE]
> Se já existir na base de dados do parceiro (por exemplo, como resultado da terminação uma relação de replicação geográfica anterior) o comando falha.
> 

1. No [portal do Azure](http://portal.azure.com), navegue para a base de dados que pretende configurar a georreplicação.
2. Na página de base de dados do SQL Server, selecione **georreplicação**e, em seguida, selecione a região para criar a base de dados secundária. Pode selecionar qualquer região diferente da região que aloja a base de dados primária, mas recomendamos que o [região emparelhado](../best-practices-availability-paired-regions.md).
   
    ![Configurar georreplicação](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selecione ou configurar o servidor e o escalão de preço da base de dados secundária.
   
    ![Configurar secundário](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcionalmente, pode adicionar uma base de dados secundária para um conjunto elástico. Para criar a base de dados secundária num conjunto, clique em **conjunto elástico** e selecione um agrupamento no servidor de destino. Um agrupamento tem de existir no servidor de destino. Este fluxo de trabalho não crie um conjunto.
5. Clique em **criar** adicionar secundário.
6. A base de dados secundária é criado e iniciar o processo de propagação.
   
    ![Configurar secundário](./media/sql-database-geo-replication-portal/seeding0.png)
7. Quando o processo de propagação estiver concluído, a base de dados secundária apresenta o estado dele.
   
    ![O seeding concluída](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Inicie uma ativação pós-falha

A base de dados secundária pode ser mudado para se tornar o site primário.  

1. No [portal do Azure](http://portal.azure.com), navegue para a base de dados primária na parceria de georreplicação.
2. No painel da base de dados do SQL Server, selecione **todas as definições** > **georreplicação**.
3. No **secundárias** lista, selecione a base de dados que pretende tornar-se a nova principal e clique em **ativação pós-falha**.
   
    ![ativação pós-falha](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Clique em **Sim** para iniciar a ativação pós-falha.

O comando comutadores imediatamente a base de dados secundária para a função primária. 

Não há um curto período de tempo durante os quais não ambas as bases de dados estão disponíveis (na ordem dos 0 para 25 segundos) enquanto as funções são mudadas. Se a base de dados principal tem várias bases de dados secundárias, o comando reconfigura automaticamente as outras bases de dados secundárias para estabelecer ligação com nova principal. A operação toda deve demorar menos de um minuto em circunstâncias normais. 

> [!NOTE]
> Este comando foi concebido para recuperação rápida da base de dados em caso de indisponibilidade. Aciona a ativação pós-falha sem sincronização de dados (forçado a ativação pós-falha).  Se o principal está online e ao consolidar transações quando o comando é emitido alguma perda de dados poderão ocorrer. 
> 
> 

## <a name="remove-secondary-database"></a>Remover a base de dados secundária
Esta operação permanentemente termina a replicação para a base de dados secundário e a função de secundário é alterado para uma base de dados de leitura e escrita regular. Se a conectividade à base de dados secundária for interrompida, o comando for bem sucedida, mas os does secundários deixam de ser de leitura / escrita até após a conectividade ser restauradas.  

1. No [portal do Azure](http://portal.azure.com), navegue para a base de dados primária na parceria de georreplicação.
2. Na página de base de dados do SQL Server, selecione **georreplicação**.
3. No **secundárias** lista, selecione a base de dados que pretende remover a parceria de georreplicação.
4. Clique em **pare a replicação**.
   
    ![Remover secundário](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Será apresentada uma janela de confirmação. Clique em **Sim** para remover a base de dados de parceria de georreplicação. (Defina-o como uma base de dados de leitura e escrita não faz parte de qualquer replicação.)

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a replicação geográfica activa, consulte [georreplicação ativa](sql-database-geo-replication-overview.md).
* Para cenários e uma descrição geral de continuidade de negócio, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

