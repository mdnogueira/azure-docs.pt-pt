---
title: "Restaurar uma base de dados SQL do Azure a partir de uma cópia de segurança | Microsoft Docs"
description: "Saiba mais sobre o restauro de ponto no tempo, o que permite-lhe reverter a uma base de dados do SQL do Azure para um ponto anterior no tempo (até 35 dias)."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 10/13/2017
ms.author: carlrab
ms.openlocfilehash: cb9b1296ced73c123faa0c682e9ef55d4b46ac11
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar uma base de dados SQL do Azure através de cópias de segurança da base de dados automatizada
Base de dados do SQL Server fornece estas opções para a base de dados de recuperação utilizando [cópias de segurança da base de dados automatizadas](sql-database-automated-backups.md) e [cópias de segurança no retenção de longo prazo](sql-database-long-term-retention.md). Pode restaurar a partir de uma cópia de segurança da base de dados para:

* Uma nova base de dados no mesmo servidor lógico recuperado para um ponto de especificado no tempo dentro do período de retenção. 
* Uma base de dados no mesmo servidor lógico recuperado para o tempo de eliminação de uma base de dados eliminada.
* Uma nova base de dados em qualquer servidor lógico em qualquer região recuperada para o ponto de cópias de segurança diárias mais recentes no armazenamento de BLOBs de georreplicação (RA-GRS).

> [!IMPORTANT]
> Não é possível substituir a base de dados existente durante o restauro.
>

Uma base de dados restaurada implica um custo de armazenamento adicional nas seguintes condições: 
- Restaure de P11 – P15 S4 S12 ou P1 – P6 se o tamanho máximo da base de dados é maior do que 500 GB.
- Restaure P1 – P6 ou PRS1 – PRS6 para S4 S12 se o tamanho máximo da base de dados for superior a 250 GB.

O extra custo é porque o tamanho máximo da base de dados restaurada é maior do que a quantidade de armazenamento incluído para o nível de desempenho e qualquer aprovisionado superior a quantidade incluída de armazenamento adicional é cobrado extra.  Para detalhes de armazenamento adicional de preços, consulte o [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/).  Se a quantidade real de espaço utilizado é inferior à quantidade de armazenamento incluído, em seguida, este custo extra pode ser evitada ao reduzir o tamanho máximo da base de dados para a quantidade incluída. Para obter mais informações sobre os tamanhos de armazenamento de base de dados e alterar o tamanho máximo da base de dados, consulte [única dos limites de recursos de base de dados](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels).  

> [!NOTE]
> [Cópias de segurança da base de dados automatizadas](sql-database-automated-backups.md) são utilizados quando cria um [cópia da base de dados](sql-database-copy.md). 
>


## <a name="recovery-time"></a>Tempo de recuperação
O tempo de recuperação para restaurar uma base de dados através de cópias de segurança da base de dados automatizada é afetado por vários fatores: 

* O tamanho da base de dados
* O nível de desempenho da base de dados
* O número de registos de transações envolvidos
* A quantidade de atividade que tem de ser reproduzido recuperar para o ponto de restauro
* A largura de banda de rede se o restauro estiver numa região diferente 
* O número de pedidos de restauro em simultâneo a ser processado na região de destino. 
  
  Para uma base de dados muito grande e/ou Active Directory, o restauro pode demorar várias horas. Se não houver indisponibilidade prolongada numa região, é possível que existem grande número de pedidos de georrestauro processados por noutras regiões. Quando existem muitos pedidos, pode aumentar o tempo de recuperação para bases de dados nessa região. Base de dados a maioria das restaura concluída no prazo de 12 horas.

Para uma única subscrição, existe a algumas limitações no número de pedidos de restauro em simultâneo (incluindo o ponto de restauro de tempo, restauro georreplicação e restauro a partir da cópia de segurança de retenção de longo prazo) que está a ser submetidas e proceeded:
|  | **N. º máximo de pedidos em simultâneo a ser processado** | **N. º máximo de pedidos simultâneos sejam submetidas** |
| :--- | --: | --: |
|Base de dados individual (por subscrição)|10|60|
|Agrupamento elástico (por conjunto)|4|200|
||||

Não é uma funcionalidade incorporada em massa de restauro. O [SQL Database do Azure: recuperação de servidor completa](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script é um exemplo de uma forma de realizar esta tarefa.

> [!IMPORTANT]
> Para recuperar cópias de segurança automatizadas a utilizar, tem de ser um membro da função de contribuinte do SQL Server na subscrição ou ser o proprietário da subscrição. Pode recuperar através do portal do Azure, do PowerShell ou da API REST. Não é possível utilizar Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Pode restaurar uma base de dados existente para um ponto anterior no tempo como uma nova base de dados no mesmo servidor lógico a utilizar o portal do Azure [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase), ou o [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Para um script do PowerShell de exemplo que mostra como efetuar um restauro de ponto no tempo de uma base de dados, consulte [restaurar uma base de dados do SQL Server com o PowerShell](scripts/sql-database-restore-database-powershell.md).
>

É possível restaurar a base de dados para qualquer nível de desempenho ou camada de serviço e como uma base de dados ou para um conjunto elástico. Certifique-se de que tem recursos suficientes no servidor lógico ou num agrupamento elástico ao qual está a restaurar a base de dados. Depois de concluído, a base de dados restaurada é uma base de dados normal, completamente acessível, online. A base de dados restaurada é debitado às taxas normais, com base no respetivo nível de desempenho e o escalão de serviço. Não pode implicar custos até que o restauro de base de dados esteja concluído.

Geralmente, restaurar uma base de dados para um ponto anterior para fins de recuperação. Ao fazê-lo, pode tratar a base de dados restaurada como uma substituição para a base de dados original ou utilizá-lo a obter dados de e, em seguida, atualizar a base de dados original. 

* ***Substituição da base de dados:*** se a base de dados restaurada destina-se como uma substituição para a base de dados original, deve verificar o nível de desempenho e/ou camada de serviço são adequada e dimensionar a base de dados, se necessário. Pode mudar o nome da base de dados original e, em seguida, atribua o nome de original utilizando o comando ALTER DATABASE em T-SQL de base de dados restaurada. 
* ***Recuperação de dados:*** se pretender obter os dados da base de dados restaurada para recuperar de um erro de utilizador ou aplicação, terá de escrever e executar os scripts de recuperação de dados necessários para extrair dados da base de dados restaurada na base de dados original. Apesar da operação de restauro pode demorar muito tempo a concluir, a base de dados de restauro é visível na lista da base de dados em todo o processo de restauro. Se eliminar a base de dados durante o restauro, a operação de restauro foi cancelada e não lhe serem cobrados para a base de dados que não foi possível concluir o restauro. 

### <a name="azure-portal"></a>Portal do Azure

Recuperar para um ponto no tempo no portal do Azure, abra a página da base de dados e clique em **restaurar** na barra de ferramentas.

![ponto no tempo de restauro](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Restauro da base de dados eliminada
Pode restaurar uma base de dados eliminada para o tempo de eliminação de uma base de dados eliminada no mesmo servidor lógico a utilizar o portal do Azure [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase), ou o [REST (createMode = restaurar)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Para um script do PowerShell de exemplo que mostra como restaurar uma base de dados eliminada, consulte [restaurar uma base de dados do SQL Server com o PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Se eliminar uma instância de servidor da SQL Database do Azure, todas as suas bases de dados também são eliminados e não podem ser recuperados. Não são atualmente suportadas para restaurar um servidor eliminado.
> 

### <a name="azure-portal"></a>Portal do Azure

Para recuperar uma base de dados eliminado durante o respetivo [período de retenção](sql-database-service-tiers.md) no portal do Azure, abra a página para o servidor e, na área de operações, clique em **eliminou bases de dados**.

![eliminado da base de dados-restauro-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![eliminado da base de dados-restauro-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Georrestauro
Pode restaurar uma base de dados do SQL Server em qualquer servidor em qualquer região do Azure a partir de mais recentes georreplicação completas e diferenciais cópias de segurança. Georrestauro utiliza uma cópia de segurança georredundante como origem e pode ser utilizado para recuperar uma base de dados, mesmo que esteja a base de dados ou o Centro de dados inacessível devido a uma falha. 

Georrestauro é a opção de recuperação predefinida quando a base de dados não está disponível devido a um incidente na região onde está alojada a base de dados. Se um incidente em grande escala nos resultados da região na indisponibilidade da sua aplicação de base de dados, pode restaurar uma base de dados de cópias de segurança georreplicação para um servidor em qualquer outra região. Há um atraso entre quando é efetuada uma cópia de segurança diferencial e quando é georreplicação para um Azure blob numa região diferente. Este atraso pode demorar até uma hora, por isso, se ocorrer um desastre, pode haver cópias de segurança a perda de dados de uma hora. A ilustração seguinte mostra o restauro da base de dados da cópia de segurança disponível último noutra região.

![georrestauro](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Para um script do PowerShell de exemplo que mostra como efetuar um restauro georreplicação, consulte [restaurar uma base de dados do SQL Server com o PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

Restauro de ponto no tempo numa secundária georreplicação não é atualmente suportado. Restauro de ponto no tempo pode ser feito apenas numa base de dados principal. Para obter informações detalhadas sobre como utilizar georrestauro para recuperar a partir de uma falha, consulte [recuperar a partir de uma falha](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Recuperação a partir de cópias de segurança é mais básica das soluções de recuperação após desastre disponíveis na base de dados do SQL Server com o objetivo de ponto de recuperação mais longo (RPO) e o tempo de recuperação de estimativa (ERT). Para soluções que utilizam as bases de dados básicas, georrestauro é frequentemente uma solução de DR razoável com um ERT de 12 horas. Para soluções que utilizam Standard ou Premium bases de dados maiores que necessitam de mais curtos tempos de recuperação, deve considerar a utilização [georreplicação ativa](sql-database-geo-replication-overview.md). Replicação geográfica activa oferece um RPO e ERT muito inferior porque apenas requer inicie uma ativação pós-falha para uma secundária continuamente replicada. Para obter mais informações sobre as opções de continuidade do negócio, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>Portal do Azure

Para georrestauro uma base de dados durante a respetiva [período de retenção](sql-database-service-tiers.md) no portal do Azure, abra a página de bases de dados SQL e, em seguida, clique em **adicionar**. No **selecionar origem** caixa de texto, selecione **cópia de segurança**. Especifique a cópia de segurança efetuar a recuperação na região e no servidor da sua preferência. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Execução através de programação de recuperação utilizando cópias de segurança automatizadas
Como anteriormente referido, além de portal do Azure, a recuperação de base de dados pode ser efetuada através de programação utilizando o Azure PowerShell ou a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell
| Cmdlet | Descrição |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Obtém um ou mais bases de dados. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Obtém uma base de dados eliminada pode restaurar. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante uma base de dados. |
| [Restauro-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Restaura uma base de dados do SQL Server. |
|  | |

### <a name="rest-api"></a>API REST
| API | Descrição |
| --- | --- |
| [REST (createMode = recuperação)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Restaura uma base de dados |
| [Obter criar ou atualizar o estado da base de dados](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Devolve o estado durante uma operação de restauro |
|  | |

## <a name="summary"></a>Resumo
Cópias de segurança automáticas proteger as bases de dados de utilizador e erros de aplicações, a eliminação acidental de base de dados e falhas prolongadas. Esta capacidade incorporada está disponível para todas as camadas de serviços e níveis de desempenho. 

## <a name="next-steps"></a>Passos seguintes
* Para cenários e uma descrição geral de continuidade de negócio, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).
* Para saber mais sobre o SQL do Azure, base de dados automática de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
* Para saber mais sobre a retenção de cópias de segurança de longa duração, consulte [retenção de cópias de segurança de longa duração](sql-database-long-term-retention.md).
* Para configurar, gerir e restaurar a partir de retenção de longo prazo de automática cópias de segurança num cofre dos serviços de recuperação do Azure no portal do Azure, consulte [configurar e a utilização de longa duração de cópia de segurança retenção](sql-database-long-term-backup-retention-configure.md). 
* Para saber mais sobre as opções de recuperação mais rápidas, consulte [ativação pós-falha de grupos e a georreplicação ativa](sql-database-geo-replication-overview.md).  
