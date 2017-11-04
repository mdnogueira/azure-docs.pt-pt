---
title: "Restaurar um armazém de dados do Azure - local e georredundante | Microsoft Docs"
description: "Descrição geral das opções de restauro de base de dados para recuperar uma base de dados no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: ea42b7135d0695b66d569095e70bb3d9f8b9594b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-restore"></a>Restauro do SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL Data Warehouse oferece restauros geográficas tanto locais como parte do respetivo após desastre do armazém de dados capacidades de recuperação. Utilize cópias de segurança de armazém de dados para restaurar o seu armazém de dados para um ponto de restauro na região primária, ou as cópias de segurança georredundante restaurar para uma região geográfica diferentes. Este artigo explica as especificações de restaurar um armazém de dados.

## <a name="what-is-a-data-warehouse-restore"></a>O que é um restauro do armazém de dados?
Um restauro do armazém de dados é um novo armazém de dados que é criado a partir de uma cópia de segurança existente ou do armazém de dados eliminada. O armazém de dados restaurada recria o armazém de dados de cópia de segurança num momento específico. Uma vez que o SQL Data Warehouse é um sistema distribuído, um restauro do armazém de dados é criado a partir de muitos ficheiros de cópia de segurança que são armazenados em blobs do Azure. 

Restauro da base de dados é uma parte essencial de qualquer estratégia de recuperação de continuidade e desastre negócio porque cria novamente os dados depois de danos acidentais ou eliminação.

Para obter mais informações, consulte:

* [Cópias de segurança do SQL Data Warehouse](sql-data-warehouse-backups.md)
* [Descrição geral da continuidade de negócio](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Pontos de restauro do armazém de dados
Como uma vantagem de utilizar o Premium Storage do Azure, o SQL Data Warehouse utiliza os instantâneos do Blob de armazenamento do Azure para o armazém de dados primária de cópia de segurança. Cada instantâneo tem um ponto de restauro que representa a hora de início do instantâneo. Para restaurar um armazém de dados, escolha um ponto de restauro e emitir um comando de restauro.  

O SQL Data Warehouse restaura sempre a cópia de segurança para um novo armazém de dados. Pode manter o armazém de dados restaurada e aquele atual ou elimine um deles. Se pretende substituir o armazém de dados atual com o armazém de dados restaurada, pode alterá-lo.

Se tiver de restaurar um armazém de dados eliminada ou em pausa, pode [criar um pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Georredundante restauro
Pode restaurar o seu armazém de dados para qualquer região que suportam o Azure SQL Data Warehouse ao seu nível de desempenho que escolheu. Tenha em atenção que DWU 9000 e 18000 não são suportados em todas as regiões durante a pré-visualização.

> [!NOTE]
> Para efetuar um restauro com redundância geográfica tem não optou por fora esta funcionalidade.
> 
> 

## <a name="restore-timeline"></a>Restaurar a linha cronológica
Pode restaurar uma base de dados para qualquer ponto de restauro disponíveis nos últimos sete dias. Instantâneos de iniciar a cada quatro para oito horas e estão disponíveis para sete dias. Quando um instantâneo com mais de sete dias, expirar e o respetivo ponto de restauro já não está disponível.

## <a name="restore-costs"></a>Restaurar os custos
Os encargos de armazenamento para o armazém de dados restaurada é faturado a taxa de Premium Storage do Azure. 

Se colocar em pausa de um armazém de dados restaurada, são-lhe cobrados para armazenamento a taxa de Premium Storage do Azure. A vantagem de colocar em pausa não é que lhe serem cobrados os recursos de computação de DWU.

Para obter mais informações sobre preços do SQL Data Warehouse, consulte [preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Utilizações de restauro
A utilização principal para o restauro de armazém de dados é recuperar dados após a perda acidental de dados ou danos.

Também pode utilizar o restauro de armazém de dados para manter uma cópia de segurança mais de sete dias. Depois da cópia de segurança é restaurada, que tem o armazém de dados online e pode colocar em pausa indefinidamente para reduzir os custos de computação. A base de dados em pausa implica os encargos de armazenamento, a taxa de Premium Storage do Azure. 

## <a name="related-topics"></a>Tópicos relacionados
### <a name="scenarios"></a>Cenários
* Para obter uma descrição de continuidade do negócio, consulte [descrição geral da continuidade de negócio](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Para efetuar um restauro do armazém de dados, restaure a utilizar:

* Consulte portal, do Azure [restaurar um armazém de dados no portal do Azure](sql-data-warehouse-restore-database-portal.md)
* Cmdlets do PowerShell, consulte [restaurar um armazém de dados utilizando cmdlets do PowerShell](sql-data-warehouse-restore-database-powershell.md)
* APIs de REST, consulte [restaurar um armazém de dados com as APIs REST](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
