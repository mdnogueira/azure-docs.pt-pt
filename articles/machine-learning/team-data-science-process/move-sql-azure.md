---
title: Mover dados para uma base de dados do SQL do Azure para o Azure Machine Learning | Microsoft Docs
description: Criar tabela SQL e os dados de carga para a tabela de SQL
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 323861d078e9beeb197333dc7e2d0314014dfdb0
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para uma Base de Dados SQL do Azure para o Azure Machine Learning
Este tópico descreve as opções para mover dados de ficheiros simples (formatos de CSV ou TSV) ou a partir dos dados armazenados num local no SQL Server para uma base de dados SQL do Azure. Estas tarefas para mover dados para a nuvem fazem parte do processo de ciência de dados de equipa.

Para um tópico descreve as opções para mover dados para um servidor de SQL no local para o Machine Learning, consulte [mover dados para o SQL Server numa máquina virtual do Azure](move-sql-server-virtual-machine.md).

O seguinte **menu** ligações para tópicos que descrevem como a ingestão de dados em ambientes de destino onde os dados podem ser armazenados e processados durante o processo de ciência do equipa dados (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

A tabela seguinte resume as opções para mover dados para uma base de dados do SQL do Azure.

| <b>ORIGEM</b> | <b>DESTINO: Base de dados SQL do Azure</b> |
| --- | --- |
| <b>Ficheiro simples (TSV formatado ou CSV)</b> |<a href="#bulk-insert-sql-query">Consulta SQL de inserção em massa |
| <b>SQL Server no local</b> |1. <a href="#export-flat-file">Exportar para ficheiro simples<br> 2. <a href="#insert-tables-bcp">Assistente de migração de base de dados do SQL Server<br> 3. <a href="#db-migration">Base de dados back cópias de segurança e restauro<br> 4. <a href="#adf">Fábrica de dados do Azure |

## <a name="prereqs"></a>Pré-requisitos
Os procedimentos descritos aqui requerem que tenha:

* Um **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um **conta do storage do Azure**. Utilize uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [gerir as chaves de acesso de armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Acesso a um **base de dados SQL do Azure**. Se tem de configurar uma base de dados de SQL do Azure, [introdução à base de dados do Microsoft Azure SQL](../../sql-database/sql-database-get-started.md) fornece informações sobre como aprovisionar uma nova instância de uma base de dados do SQL do Azure.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

**Dados**: os processos de migração são demonstrados utilizando o [NYC Taxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados NYC Taxi contém informações sobre dados de viagem e fairs e está disponível no armazenamento de Blobs do Azure: [NYC Taxi dados](http://www.andresmh.com/nyctaxitrips/). Um exemplo e uma descrição destes ficheiros são fornecidos na [NYC Taxi viagens Dataset Descrição](sql-walkthrough.md#dataset).

Pode adaptar os procedimentos aqui descritos para um conjunto de dados da sua própria ou siga os passos, conforme descrito utilizando o conjunto de dados NYC Taxi. Para carregar o conjunto de dados NYC Taxi para a base de dados do SQL Server no local, siga o procedimento descrito no [em massa importar dados na base de dados do SQL Server](sql-walkthrough.md#dbload). Estas instruções se destinam a um SQL Server uma Máquina Virtual no Azure, mas o procedimento para carregar para o SQL Server no local é o mesmo.

## <a name="file-to-azure-sql-database"></a>Mover dados de uma origem de ficheiro simples para uma base de dados SQL do Azure
Dados em ficheiros simples (formato CSV ou TSV) podem ser movidos para uma base de dados SQL do Azure utilizando uma consulta de SQL de inserção em massa.

### <a name="bulk-insert-sql-query"></a>Consulta SQL de inserção em massa
Os passos para o procedimento utilizando a consulta de SQL de inserção em massa são semelhantes às abordadas nas secções para mover dados de uma origem de ficheiro simples para o SQL Server numa VM do Azure. Para obter mais informações, consulte [consulta de SQL de inserção em massa](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Mover dados do SQL Server no local para uma base de dados SQL do Azure
Se a origem de dados é armazenada num SQL Server no local, existem vários possibilidades para mover os dados para uma base de dados SQL do Azure:

1. [Exportar para ficheiro simples](#export-flat-file)
2. [Assistente de migração de base de dados do SQL Server](#insert-tables-bcp)
3. [Base de dados back cópias de segurança e restauro](#db-migration)
4. [Azure Data Factory](#adf)

Os passos para a primeira três são muito semelhantes dessas secções [mover dados para o SQL Server numa máquina virtual do Azure](move-sql-server-virtual-machine.md) que abrangem estes procedimentos mesmos. São fornecidas hiperligações para as secções apropriadas nesse tópico nas instruções seguintes.

### <a name="export-flat-file"></a>Exportar para ficheiro simples
Os passos para esta exportação para um ficheiro simples são semelhantes às abrangidas [exportar para ficheiro simples](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Assistente de migração de base de dados do SQL Server
Os passos para utilizar o Assistente de migração de base de dados do SQL são semelhantes às abrangidas [Assistente de migração de base de dados do SQL Server](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Base de dados back cópias de segurança e restauro
Os passos para utilizar a base de dados de cópia de segurança e restauro são semelhantes às abrangidas [da base de dados back cópias de segurança e restaurar](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Fábrica de dados do Azure
O procedimento para mover dados para uma base de dados SQL do Azure com o Azure Data Factory (ADF) é fornecido no tópico [mover dados de um servidor SQL no local para o SQL Azure com o Azure Data Factory](move-sql-azure-adf.md). Este tópico mostra como mover dados de uma base de dados do SQL Server no local para uma base de dados SQL do Azure através do Blob Storage do Azure com ADF.

Considere utilizar ADF dados tem de ser migrados continuamente num cenário híbrido que acede no local e aos recursos na nuvem e os dados são transacionados ou tem de ser modificados ou ter lógica de negócio adicionada ao mesmo quando a ser migrado. ADF permite o agendamento e a monitorização de tarefas utilizando scripts simples do JSON que gerem o movimento de dados numa base periódica. ADF também tem outras funcionalidades, tais como o suporte para operações complexas.
