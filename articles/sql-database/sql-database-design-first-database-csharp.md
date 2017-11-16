---
title: Conceber a sua primeira SQL database do Azure - c# | Microsoft Docs
description: Saiba como conceber a sua primeira base de dados SQL do Azure e ligar ao mesmo com um programa c# ADO.NET a utilizar.
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 08/25/2017
ms.author: genemi
ms.openlocfilehash: 1e13a9a89873b189e1ea05c0208e7c8646881655
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Estruturar uma base de dados SQL do Azure e estabelecer ligação com C & #x; e ADO.NET

Base de dados SQL do Azure é um relacional da base de dados como um serviço (DBaaS) no Microsoft Cloud (Azure). Neste tutorial, irá aprender a utilizar o portal do Azure e ADO.NET com o Visual Studio para: 

> [!div class="checklist"]
> * Criar uma base de dados no portal do Azure
> * Configurar uma regra de firewall ao nível do servidor no portal do Azure
> * Ligar à base de dados com ADO.NET e o Visual Studio
> * Criar tabelas com ADO.NET
> * Inserir, atualizar e eliminar os dados com ADO.NET 
> * Consultar dados ADO.NET

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio Community 2017, Visual Studio Professional 2017 ou Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a tarefas de base de dados básicas, tais como criar uma base de dados e tabelas, carregar e consultar dados e restaurar a base de dados para um ponto anterior no tempo. Aprendeu a:
> [!div class="checklist"]
> * Criar uma base de dados
> * Configurar uma regra de firewall
> * Ligar à base de dados com [Visual Studio e c#](sql-database-connect-query-dotnet-visual-studio.md)
> * Criar tabelas
> * Inserir, atualizar e eliminar dados
> * Consultar dados

Avançar para o próximo tutorial para saber mais sobre como migrar os dados.

> [!div class="nextstepaction"]
>[Migrar a base de dados do SQL Server a SQL Database do Azure](sql-database-migrate-your-sql-server-database.md)

