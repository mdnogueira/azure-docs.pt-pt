---
title: "Simulações de recuperação de desastres do SQL Server da base de dados | Microsoft Docs"
description: "Saiba orientações e melhores práticas para utilizar a SQL Database do Azure para efetue simulações de recuperação após desastre."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 07/31/2016
ms.author: sashan
ms.openlocfilehash: 8e395153fc9907107156c3412e5e0de554c83750
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="performing-disaster-recovery-drill"></a>Efetuar exercício de recuperação de desastre
Recomenda-se que a validação da preparação da aplicação para o fluxo de trabalho de recuperação é efetuada periodicamente. Verificar o comportamento da aplicação e as implicações de perda de dados e/ou a interrupção envolve a que a ativação pós-falha é uma boa prática de engenharia. Também é um requisito pela maioria das normas da indústria como parte da certificação de continuidade do negócio.

Efetuar um exercício de recuperação de desastre é composta por:

* Falha de camada de dados simulando
* Recuperar
* Validar a recuperação de post de integridade de aplicações

Dependendo de como pode [concebido a aplicação para a continuidade do negócio](sql-database-business-continuity.md), o fluxo de trabalho a executar o exercício pode variar. Abaixo, iremos descrevem as melhores práticas de realizar um exercício de recuperação após desastre no contexto da SQL Database do Azure.

## <a name="geo-restore"></a>Georrestauro
Para evitar a potencial perda de dados quando realizar um exercício de recuperação após desastre, recomendamos que efetuar a desagregação utilizando um ambiente de teste ao criar uma cópia de ambiente de produção e utilizá-la para verificar o fluxo de trabalho de ativação pós-falha da aplicação.

#### <a name="outage-simulation"></a>Simulação de falha
Para simular a interrupção, pode eliminar ou mudar o nome da base de dados de origem. Isto faz com que as falhas de conectividade de aplicação.

#### <a name="recovery"></a>Recuperação
* Efetuar o restauro georreplicação da base de dados para um servidor diferente, tal como descrito [aqui](sql-database-disaster-recovery.md).
* Alterar a configuração da aplicação para ligar à base de dados recuperada e siga o [configurar uma base de dados após a recuperação](sql-database-disaster-recovery.md) guia para concluir a recuperação.

#### <a name="validation"></a>Validação
* Conclua a desagregação ao verificar a recuperação de post de integridade de aplicação (incluindo cadeias de ligação, inícios de sessão, testar a funcionalidade básica ou outro parte validações dos procedimentos de signoffs padrão de aplicação).

## <a name="geo-replication"></a>Georreplicação
Para uma base de dados que está protegida com georreplicação o exercício desagregação envolve a ativação pós-falha planeada para a base de dados secundária. A ativação pós-falha planeada assegura que o primário e as bases de dados secundárias permanecerem sincronizadas quando as funções são mudadas. Ao contrário a ativação pós-falha não planeada, esta operação resulta numa perda de dados, pelo que a desagregação pode ser executada no ambiente de produção.

#### <a name="outage-simulation"></a>Simulação de falha
Para simular a interrupção, pode desativar a aplicação web ou a máquina virtual ligada à base de dados. Isto resulta em falhas de conectividade para os clientes web.

#### <a name="recovery"></a>Recuperação
* Certifique-se a configuração da aplicação na região DR aponta para o anterior secundária torna-se a nova principal totalmente acessível.
* Efetuar [a ativação pós-falha planeada](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) para tornar a base de dados secundária um novo site primário
* Siga o [configurar uma base de dados após a recuperação](sql-database-disaster-recovery.md) guia para concluir a recuperação.

#### <a name="validation"></a>Validação
* Conclua a desagregação ao verificar a recuperação de post de integridade de aplicação (incluindo cadeias de ligação, inícios de sessão, testar a funcionalidade básica ou outro parte validações dos procedimentos de signoffs padrão de aplicação).

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre cenários de continuidade do negócio, consulte [cenários de continuidade](sql-database-business-continuity.md)
* Para saber mais sobre o SQL do Azure, base de dados automática de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, consulte [restaurar uma base de dados de cópias de segurança iniciou o serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre as opções de recuperação mais rápidas, consulte [georreplicação ativa](sql-database-geo-replication-overview.md)  
