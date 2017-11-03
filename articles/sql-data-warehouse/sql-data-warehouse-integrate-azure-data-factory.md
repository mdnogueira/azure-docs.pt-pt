---
title: Utilizar o Azure Data Factory com o SQL Data Warehouse | Microsoft Docs
description: "Sugestões de utilização do Azure Data Factory (ADF) com o Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Utilize o Azure Data Factory com o SQL Data Warehouse
O Azure Data Factory fornece um método completamente gerido para da orquestração a transferência de dados e a execução de procedimentos armazenados no armazém de dados do SQL Server.  Isto permitirá mais facilmente configuração e agenda complexa extrair transformação e carregamento (ETL) os procedimentos no armazém de dados do SQL Server. Para obter uma descrição mais completa do Azure Data Factory, consulte o [documentação do Azure Data Factory][Azure Data Factory documentation].

## <a name="data-movement"></a>Movimento de Dados
O Azure Data Factory permite que o movimento de dados entre origens no local e serviços do Azure diferentes.  Geral, atual integração com o Azure Data Factory suporta o movimento de dados de e para as seguintes localizações:

* Armazenamento de Blobs do Azure
* Base de Dados SQL do Azure
* SQL Server no local
* SQL Server em IaaS

Para obter informações sobre como configurar dados de atividade de cópia consulte [copiar dados com o Azure Data Factory][Copy data with Azure Data Factory]

## <a name="stored-procedures"></a>Procedimentos Armazenados
 Da mesma forma, pode ser utilizado para agendar a transferência de dados do Azure Data Factory também podem ser utilizado para orquestrar a execução de procedimentos armazenados.  Isto permite pipelines mais complexas para ser criado e expande a capacidade de Azure Data Factory para tirar partido computacional do SQL Data Warehouse.

## <a name="next-steps"></a>Passos seguintes
Para obter uma descrição geral de integração, consulte [descrição geral da integração do SQL Data Warehouse][SQL Data Warehouse integration overview].
Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

