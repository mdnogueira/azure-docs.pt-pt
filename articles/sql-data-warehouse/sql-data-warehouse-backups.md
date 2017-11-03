---
title: "Azure SQL Data Warehouse cópias de segurança - instantâneos, georredundante | Microsoft Docs"
description: "Saiba mais sobre cópias de segurança do armazém de dados do SQL Server da base de dados incorporados que lhe permite restaurar um SQL Data Warehouse do Azure para um ponto de restauro ou uma região geográfica diferentes."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: e76349ef7a2afa02d4f9e5295f299bb8084d1e08
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Cópia de segurança e restauro no SQL Data Warehouse
Este artigo explica as especificações de cópias de segurança no SQL Data Warehouse. Utilize cópias de segurança de armazém de dados para restaurar uma base de dados de instantâneos para a região primária ou restaurar um georreplicação-cópia de segurança para a região georreplicação-emparelhado. 

## <a name="what-is-a-data-warehouse-backup"></a>O que é uma cópia de segurança do armazém de dados?
Uma cópia de segurança do armazém de dados é a cópia da base de dados que pode utilizar para restaurar um armazém de dados.  Uma vez que o SQL Data Warehouse é um sistema distribuído, uma cópia de segurança do armazém de dados consiste em muitos ficheiros que estão localizados no armazenamento do Azure. Uma cópia de segurança do armazém de dados inclui instantâneos da base de dados local e georreplicação-as cópias de segurança de todas as bases de dados e ficheiros que estão associados um armazém de dados. 

## <a name="local-snapshot-backups"></a>Cópias de segurança do instantâneo local
SQL Data Warehouse assume instantâneos do seu armazém de dados ao longo do dia. Os instantâneos são disponíveis durante sete dias. Armazém de dados do SQL Server suporta um objetivo de ponto de recuperação (RPO) oito horas. Pode restaurar o seu armazém de dados na região primária em qualquer um dos instantâneos efetuadas nos últimos sete dias.

Para ver quando o instantâneo último iniciada, execute esta consulta no online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>Cópias de segurança a Georreplicação
O SQL Data Warehouse efetua uma georreplicação-cópia de segurança uma vez por dia para um [Centro de dados emparelhado](../best-practices-availability-paired-regions.md). O RPO para um georrestauro é de 24 horas. Pode restaurar a georreplicação-cópia de segurança para o servidor na região georreplicação-emparelhado. cópia de segurança georreplicação garante que pode restaurar o armazém de dados no caso de não é possível aceder os instantâneos na sua região primária.

As cópias de segurança a Georreplicação estão por predefinição. Se o seu armazém de dados está otimizado para elasticidade, pode [ativamente](https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicyredirectedfrom=msdn) se desejar. Não é possível ativamente georreplicação-cópias de segurança com o otimizado para a camada de desempenho de computação.

## <a name="backup-costs"></a>Custos de cópia de segurança
Vai notar que a fatura do Azure tem um item de linha para o Premium Storage do Azure e um item de linha para o armazenamento georredundante. A taxa de Premium Storage é o custo total para armazenar os dados na região primária, que inclui os instantâneos.  A taxa georredundante abrange o custo de armazenar cópias de segurança georreplicação.  

O total de custos para o seu armazém de dados primária e sete dias de instantâneos do Blob do Azure é arredondado para o TB mais próximo. Por exemplo, se o armazém de dados é 1,5 TB e os instantâneos utilizam 100 GB, é-lhe faturado para 2 TB de dados às taxas de Premium Storage do Azure. 

> [!NOTE]
> Cada instantâneo está inicialmente vazio e crescimentos de como efetuar alterações no armazém de dados principal. Todos os instantâneos aumentam de tamanho como as alterações de armazém de dados. Por conseguinte, os custos de armazenamento de instantâneos de crescimento, de acordo com a taxa de alteração.
> 
> 

Se estiver a utilizar o armazenamento georredundante, receberá um custo de armazenamento separada. O armazenamento georredundante é faturado a taxa de acesso de leitura geograficamente armazenamento redundantes (RA-GRS) padrão.

Para obter mais informações sobre preços do SQL Data Warehouse, consulte [preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Retenção instantâneo quando um armazém de dados está em pausa
SQL Data Warehouse não cria instantâneos e não expira instantâneos enquanto um armazém de dados está em pausa. A antiguidade do instantâneo não altera enquanto o armazém de dados está em pausa. Retenção de instantâneo é baseada no número de dias que no armazém de dados está online, não os dias de calendário.

Por exemplo, se um instantâneo é iniciado 1 de Outubro a 4 pm e o armazém de dados está em pausa 3 de Outubro, 4 pm, os instantâneos são até dois dias de antiguidade. Quando o armazém de dados fica online novamente o instantâneo é dois dias de antiguidade. Se o armazém de dados ficar online 5 de Outubro, 4 pm, o instantâneo é dois dias de antiguidade e continua durante cinco dias mais.

Quando o armazém de dados volta a ficar online, o SQL Data Warehouse retoma dos novos instantâneos e expira instantâneos quando têm mais de sete dias de dados.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>Pode restaurar um armazém de dados ignorados?
Quando remover um armazém de dados, o SQL Data Warehouse cria um instantâneo final e guarda-o durante sete dias. Pode restaurar o armazém de dados para o ponto de restauro final criado em eliminação. 

> [!IMPORTANT]
> Se eliminar uma instância do SQL server lógica, todas as bases de dados que pertencem à instância também são eliminados e não podem ser recuperados. Não é possível restaurar um servidor eliminado.
> 

## <a name="next-steps"></a>Passos seguintes
Para restaurar um SQL data warehouse, consulte [restaurar um SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

Para obter uma descrição de continuidade do negócio, consulte [descrição geral da continuidade de negócio](../sql-database/sql-database-business-continuity.md)
