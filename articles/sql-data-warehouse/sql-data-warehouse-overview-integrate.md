---
title: "Criar soluções integradas com o SQL Data Warehouse | Microsoft Docs"
description: "Ferramentas e parceiros com soluções que se integram com o SQL Data Warehouse. "
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: d407c29f99fd7537590ec787febd84a9e3f4f353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="leverage-other-services-with-sql-data-warehouse"></a>Tirar partido de outros serviços com o SQL Data Warehouse
Para além do respetiva funcionalidades principais, o SQL Data Warehouse permite aos utilizadores tirar partido das muitas os outros serviços do Azure em conjunto com o mesmo.  Mais concretamente, podemos ter demorado atualmente passos para integrar profundamente com o seguinte:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Estamos a trabalhar para ligar com mais serviços através do ecossistema do Azure.

## <a name="power-bi"></a>Power BI
Integração do Power BI permite-lhe tirar partido da capacidade de computação do SQL Data Warehouse com o relatório dinâmico e visualização do Power BI. Integração do Power BI atualmente inclui:

* **Direcionar Connect**: um mais avançadas de ligação com a lógica pushdown no armazém de dados do SQL Server.  Isto fornece uma análise mais rápido numa escala maior.
* **Abrir no Power BI**: O botão 'Abrir no Power BI' passa a informação de instância para Power BI, permitindo uma ligação de mais integrada.

Consulte [integrar com o Power BI](sql-data-warehouse-integrate-power-bi.md) ou [documentação do Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) para obter mais informações.

## <a name="azure-data-factory"></a>Azure Data Factory
O Azure Data Factory fornece aos utilizadores uma plataforma gerida para criar pipelines de carga extrair complexas.  Integração do SQL Data Warehouse com o Azure Data Factory inclui o seguinte:

* **Procedimentos armazenados**: orquestrar a execução de procedimentos armazenados no armazém de dados do SQL Server.
* **Cópia**: Utilize ADF para mover dados para o SQL Data Warehouse.  Esta operação pode utilizar o mecanismo de movimento de dados padrão do ADF ou PolyBase nos bastidores. 

Consulte [integrar com o Azure Data Factory](sql-data-warehouse-integrate-azure-data-factory.md) ou [documentação do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) para obter mais informações.

## <a name="azure-machine-learning"></a>Azure Machine Learning
O Azure Machine Learning é um serviço de análise completamente gerido que permite aos utilizadores criar modelos intricate tirar partido de um grande conjunto de ferramentas preditivos.  Armazém de dados do SQL Server é suportado como uma origem e de destino para estes modelos com a seguinte funcionalidade:

* **Ler dados:** unidade modelos com dimensionamento, com o T-SQL no armazém de dados do SQL Server.
* **Escrita de dados:** consolidação é alterado de qualquer modelo de volta para o SQL Data Warehouse.

Consulte [integrar com o Azure Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md) ou [documentação do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) para obter mais informações.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
O Azure Stream Analytics é uma infraestrutura complexa, completamente gerida para processamento e consumir dados de eventos gerados a partir de Hub de eventos do Azure.  Integração com o SQL Data Warehouse permite a transmissão em fluxo de dados de forma eficaz processar e armazenar em conjunto com a dados relacionais, ativar a análise mais aprofundada, mais avançada.  

* **Resultado da tarefa:** enviar o resultado de tarefas do Stream Analytics diretamente ao SQL Data Warehouse.

Consulte [integrar com o Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md) ou [documentação do Azure Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) para obter mais informações.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
