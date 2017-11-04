---
title: Utilizam o Azure Stream Analytics com o SQL Data Warehouse | Microsoft Docs
description: "Sugestões para utilizar o Azure Stream Analytics com o Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 14783f0464764a11d7f03a5db1c2d63728a4cb50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Utilizam o Azure Stream Analytics com o SQL Data Warehouse
O Azure Stream Analytics é um serviço completamente gerido que fornece processamento de eventos complexos baixa latência, elevada disponibilidade e dimensionável através de transmissão em fluxo de dados na nuvem. Pode aprender as noções básicas através da leitura [introdução ao Azure Stream Analytics][Introduction to Azure Stream Analytics]. Em seguida, pode saber como criar uma solução ponto-a-ponto Stream Analytics, seguindo o [começar a utilizar o Azure Stream Analytics] [ Get started using Azure Stream Analytics] tutorial.

Neste artigo, irá aprender a utilizar a base de dados do armazém de dados SQL do Azure como um sink de saída para as tarefas de análise Steam.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro, execute os seguintes passos no [começar a utilizar o Azure Stream Analytics] [ Get started using Azure Stream Analytics] tutorial.  

1. Criar um Hub de eventos de entrada
2. Configurar e iniciar a aplicação do gerador de eventos
3. Aprovisionar uma tarefa de Stream Analytics
4. Especifique a consulta e de entrada da tarefa

Em seguida, crie uma base de dados do Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especifique o resultado da tarefa: base de dados do armazém de dados SQL do Azure
### <a name="step-1"></a>Passo 1
Na sua tarefa do Stream Analytics clique **saída** da parte superior da página e, em seguida, clique em **adicionar saída**.

### <a name="step-2"></a>Passo 2
Selecione a base de dados SQL e clique em seguinte.

![][add-output]

### <a name="step-3"></a>Passo 3
Introduza os seguintes valores na página seguinte:

* *Alias de saída*: introduza um nome amigável para este resultado da tarefa.
* *Subscrição*:
  * Se a base de dados do armazém de dados do SQL Server na mesma subscrição que a tarefa de Stream Analytics, selecione utilizar base de dados do SQL da subscrição atual.
  * Se a base de dados está numa subscrição diferente, selecione base de dados de SQL de utilizar outra subscrição.
* *Base de dados*: Especifique o nome de uma base de dados de destino.
* *Nome do servidor*: Especifique o nome do servidor da base de dados que acabou de especificar. Pode utilizar o Portal clássico do Azure pode encontrá-lo.

![][server-name]

* *Nome de utilizador*: Especifique o nome de utilizador de uma conta que tenha permissões de escrita para a base de dados.
* *Palavra-passe*: forneça a palavra-passe da conta de utilizador especificado.
* *Tabela*: Especifique o nome da tabela de destino na base de dados.

![][add-database]

### <a name="step-4"></a>Passo 4
Clique no botão de verificação para adicionar este resultado da tarefa e certifique-se de que o Stream Analytics pode ligar com êxito na base de dados.

![][test-connection]

Quando a ligação à base de dados for bem sucedida, verá uma notificação na parte inferior do portal. Pode clicar em ligação de teste na parte inferior para testar a ligação à base de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter uma descrição geral de integração, consulte [descrição geral da integração do SQL Data Warehouse][SQL Data Warehouse integration overview].

Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
