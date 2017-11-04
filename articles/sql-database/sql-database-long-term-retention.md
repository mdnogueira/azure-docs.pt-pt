---
title: "Armazenar cópias de segurança de SQL Database do Azure para até 10 anos | Microsoft Docs"
description: "Saiba como o SQL Database do Azure suporta armazenar cópias de segurança de até 10 anos."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: e44c92c3f37b3f1e3397d1c8cdb8c8f6d0f9942e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Armazenar cópias de segurança de SQL Database do Azure para até 10 anos
Muitas aplicações terem regulamentação, conformidade ou de outras empresas fins que necessitam que sejam manter cópias de segurança da base de dados para além dos dias 7-35 fornecidas pelo SQL Database do Azure [cópias de segurança automáticas](sql-database-automated-backups.md). Ao utilizar a funcionalidade de cópia de segurança de retenção de longo prazo, pode armazenar as cópias de segurança de base de dados do SQL Server num cofre do Azure Recovery Services para até 10 anos. Pode armazenar até 1000 bases de dados por cofre. Em seguida, pode selecionar qualquer cópia de segurança no cofre para restaurá-lo como uma nova base de dados.

> [!IMPORTANT]
> Retenção de cópias de segurança de longa duração está atualmente em pré-visualização e estão disponíveis as seguintes regiões: Leste da Austrália, Sudeste da Austrália, sul do Brasil, EUA Central, Ásia Oriental, EUA leste, EUA Leste 2, Índia Central, Sul da Índia, leste do Japão, oeste do Japão, Norte Central dos EUA, Norte Europa, EUA Centro-Sul, Sudeste asiático, Europa Ocidental e EUA oeste.
>

> [!NOTE]
> Pode ativar até 200 bases de dados por cofre durante um período de 24 horas. Recomendamos que utilize um cofre separado para cada servidor para minimizar o impacto deste limite. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>Como funciona a retenção de cópias de segurança de longa duração de base de dados SQL

Com retenção de cópias de segurança de longo prazo, pode associar um servidor de base de dados SQL com um cofre dos serviços de recuperação do Azure. 

* Tem de criar o Cofre na mesma subscrição do Azure que criar o SQL server e na mesma região geográfica e grupo de recursos. 
* Em seguida, configure uma política de retenção para uma base de dados. A política faz com que as cópias de segurança da base de dados completa semanal ser copiado para o Cofre dos serviços de recuperação e mantidos durante o período de retenção especificado (até 10 anos). 
* Em seguida, pode restaurar a base de dados a partir de qualquer um destas cópias de segurança para uma nova base de dados em qualquer servidor na subscrição. Armazenamento do Azure cria uma cópia a partir de cópias de segurança existentes e a cópia não tem nenhum impacto no desempenho na base de dados existente.

> [!TIP]
> Para obter um guia de procedimentos, consulte [configurar e restauro a partir da retenção de cópias de segurança de longa duração de base de dados do Azure SQL](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Ativar a retenção de cópias de segurança de longa duração

Para configurar a retenção de cópias de segurança de longa duração, para uma base de dados:

1. Crie um cofre dos serviços de recuperação do Azure na mesma região, a subscrição e o grupo de recursos como o servidor de base de dados do SQL Server. 
2. Registe o servidor no cofre.
3. Crie uma política de proteção do Azure Recovery Services.
4. Aplica a política de proteção para as bases de dados que necessitam de retenção de cópias de segurança de longa duração.

Para configurar, gerir e restaurar uma base de dados de retenção de cópias de segurança de longa duração das cópias de segurança automatizadas num cofre dos serviços de recuperação do Azure, efetue o seguinte:

* No portal do Azure: clique em **retenção de cópias de segurança de longa duração**, selecione uma base de dados e, em seguida, clique em **configurar**. 

   ![Selecione uma base de dados para a retenção de cópias de segurança de longa duração](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* Através do PowerShell: Aceda a [configurar e restauro a partir da retenção de cópias de segurança de longa duração de base de dados do Azure SQL](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>Restaurar uma base de dados que é armazenado com a funcionalidade de cópia de segurança de retenção de longo prazo

Para recuperar a partir de uma cópia de segurança de retenção de cópias de segurança longo prazo:

1. Liste o Cofre de onde está armazenada a cópia de segurança.
2. Liste o contentor que está mapeado para o servidor lógico.
3. Liste a origem de dados dentro do cofre que está mapeado para a base de dados.
4. Liste os pontos de recuperação que estão disponíveis para restaurar.
5. Restaure a base de dados do ponto de recuperação para o servidor de destino na sua subscrição.

Para configurar, gerir e restaurar uma base de dados de retenção de cópias de segurança de longa duração das cópias de segurança automatizadas num cofre dos serviços de recuperação do Azure, efetue o seguinte:

* No portal do Azure: aceda a [gerir retenção de cópias de segurança de longa duração no portal do Azure](sql-database-long-term-backup-retention-configure.md). 

* Através do PowerShell: Aceda a [gerir retenção de cópias de segurança de longo prazo com o PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>Obter o preço da retenção de cópias de segurança de longa duração

Retenção de cópias de segurança de longa duração de uma base de dados do SQL Server é cobrada de acordo com o [serviços de cópia de segurança do Azure, taxas de preços](https://azure.microsoft.com/pricing/details/backup/).

Depois do servidor de base de dados do SQL Server se encontra registado junto do cofre, são-lhe cobrados para o armazenamento total que é utilizado pelo cópias de segurança semanais armazenadas no cofre.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Vista disponíveis cópias de segurança que são armazenadas na retenção de cópias de segurança de longa duração

Para configurar, gerir e restaurar uma base de dados de retenção de cópias de segurança de longa duração das cópias de segurança automatizadas num cofre do Azure Recovery Services utilizando o portal do Azure, efetue o seguinte:

* No portal do Azure: aceda a [gerir retenção de cópias de segurança de longa duração no portal do Azure](sql-database-long-term-backup-retention-configure.md). 

* Através do PowerShell: Aceda a [gerir retenção de cópias de segurança de longo prazo com o PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Desativar a retenção de longo prazo

O serviço de recuperação processa automaticamente a limpeza de cópias de segurança baseada na política de retenção fornecido. 

Para parar de enviar as cópias de segurança para uma base de dados específico para o cofre, remova a política de retenção para a base de dados.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> As cópias de segurança que já estão no cofre são afetadas. Estes são eliminados automaticamente pelo serviço de recuperação quando o respetivo período de retenção expira.

## <a name="long-term-backup-retention-faq"></a>Retenção de cópias de segurança de longa duração FAQ

**Posso manualmente a eliminar as cópias de segurança específicas no Cofre?**

Atualmente não. O Cofre limpa automaticamente cópias de segurança quando o período de retenção tiver expirado.

**Pode registar meu servidor para armazenar a mais do que um cofre de cópias de segurança?**

Não, atualmente pode armazenar a apenas um cofre de cópias de segurança de cada vez.

**Posso ter um cofre e o servidor em diferentes subscrições?**

Não, atualmente o Cofre e o servidor tem de ser na mesma subscrição e grupo de recursos.

**Pode utilizar um cofre que criar numa região diferente da região do meu servidor?**

Não, o Cofre e o servidor tem de ser na mesma região para minimizar o tempo de cópia e evitar custos de tráfego.

**Como muitas bases de dados pode armazenar um cofre?**

Atualmente, fornecemos suporte até 1000 bases de dados por cofre. 

**Quantos cofres pode criar por subscrição**

Pode criar até 25 cofres por subscrição.

**Como muitas bases de dados pode configurar por dia por Cofre?**

Pode configurar 200 bases de dados por dia por cofre.

**Retenção de cópias de segurança de longa duração funciona com conjuntos elásticos?**

Sim. Qualquer base de dados no conjunto pode ser configurado com a política de retenção.

**Pode escolher a hora em que é criada a cópia de segurança?**

Não, a base de dados SQL controla a agenda de cópia de segurança para minimizar o impacto do desempenho nas suas bases de dados.

**Tenho a encriptação transparente de dados ativada para a minha base de dados. Posso utilizá-lo com o Cofre?** 

Sim, a encriptação transparente de dados é suportada. Pode restaurar a base de dados do cofre, mesmo que já não existe na base de dados original.

**O que acontece com as cópias de segurança no cofre minha subscrição está suspensa?** 

Se a sua subscrição está suspensa, vamos manter as bases de dados existentes e as cópias de segurança. Novas cópias de segurança não são copiadas para o cofre. Após reativar a subscrição, o serviço de retoma de copiar as cópias de segurança para o cofre. O Cofre torna-se acessível para as operações de restauro utilizando as cópias de segurança que foram copiadas existe antes da subscrição foi suspenso. 

**Posso obter acesso aos ficheiros de cópia de segurança da base de dados SQL para que posso pode transferir ou restaure-os para o SQL server?**

Não, atualmente não.

**É possível ter várias agendas (diariamente, anual mensal, semanais,) dentro de uma política de retenção do SQL Server.**

Não, vários agendamentos atualmente só estão disponíveis para cópias de segurança da máquina virtual.

**E se configuramos retenção de cópias de segurança de longa duração numa base de dados que é uma base de dados secundária georreplicação ativa?**

Uma vez que não fazer cópias de segurança em réplicas, atualmente não é efectuada nenhuma opção para a retenção de cópias de segurança de longa duração em bases de dados secundárias. No entanto, é importante para os utilizadores configurar a retenção de cópias de segurança de longa duração numa base de dados secundária georreplicação ativa para estes motivos:
* Quando ocorre uma ativação pós-falha e a base de dados torna-se uma base de dados primária, iremos efetuar um completo cópia de segurança, que é carregado para o cofre.
* Existe um custo extra para o cliente para configurar a retenção de cópias de segurança de longa duração numa base de dados secundária.

## <a name="next-steps"></a>Passos seguintes
Porque as cópias de segurança da base de dados proteger os dados de danos acidentais ou eliminação, se estiver a uma parte essencial dos quaisquer continuidade do negócio e a estratégia de recuperação após desastre. Para saber mais sobre as outras soluções de continuidade do negócio da base de dados do SQL Server, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).
