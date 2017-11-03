---
title: "Portas para além de 1433 para a base de dados SQL | Microsoft Docs"
description: "Ligações de cliente do ADO.NET a SQL Database do Azure, podem ignorar o proxy e interaja diretamente com a base de dados utilizando as portas que não sejam 1433."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 3f17106a-92fd-4aa4-b6a9-1daa29421f64
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
ms.openlocfilehash: ea184cce4217e6c81c02740f0d6ccf79cc1c1c4a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portas para além de 1433 para ADO.NET 4.5
Este tópico descreve o comportamento de ligação de SQL Database do Azure para clientes que utilizam ADO.NET 4.5 ou uma versão posterior. 

> [!IMPORTANT]
> Para obter informações sobre a arquitetura de conectividade, consulte [arquitetura de conectividade da base de dados do Azure SQL](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Vs exteriores dentro
Para ligações ao SQL Database do Azure, podemos devem primeiro solicitar se o seu programa cliente é executada *fora* ou *dentro* o limite de nuvem do Azure. As subsecções discutem dois cenários comuns.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Exterior:* cliente é executado no seu computador de secretária
A porta 1433 é a porta única que tem de estar aberta no seu computador de secretária que aloja a aplicação de cliente da base de dados SQL.

#### <a name="inside-client-runs-on-azure"></a>*Interior:* cliente é executado no Azure
Quando o cliente é executado dentro do limite em nuvem do Azure, utiliza o que podemos chamar um *rota direta* para interagir com o servidor de base de dados SQL. Depois de uma ligação é estabelecida, mais as interações entre o cliente e a base de dados não envolvem não existe nenhum proxy middleware.

A sequência é o seguinte:

1. ADO.NET 4.5 (ou posterior) inicia uma breve interação com a nuvem do Azure e recebe um número de porta dinâmica identificados.
   
   * É o número da porta dinamicamente identificado no intervalo de 11000 11999 ou 14000 14999.
2. ADO.NET, em seguida, liga para o servidor de base de dados SQL diretamente, sem middleware sessão.
3. As consultas são enviadas diretamente à base de dados e os resultados são devolvidos diretamente para o cliente.

Certifique-se de que a porta de intervalos de 11000 11999 e 14000-14999 no seu computador cliente do Azure permanecem disponíveis para as interações de cliente do ADO.NET 4.5 com base de dados do SQL Server.

* Em particular, o intervalo de portas devem ter outros bloqueadores de saída.
* Na sua VM do Azure, o **Firewall do Windows com segurança avançada** controla as definições de porta.
  
  * Pode utilizar o [interface de utilizador do firewall](http://msdn.microsoft.com/library/cc646023.aspx) para adicionar uma regra que especificou o **TCP** como o protocolo, juntamente com um intervalo de portas com a sintaxe **11000 11999**.

## <a name="version-clarifications"></a>Clarificações de versão
Esta secção esclarece os monikers que se referem para versões de produto. Também apresenta uma lista alguns emparelhamentos de versões entre os produtos.

#### <a name="adonet"></a>ADO.NET
* O ADO.NET 4.0 suporta o protocolo TDS 7.3, mas não 7.4.
* ADO.NET 4.5 e posterior suporta o protocolo TDS 7.4.

## <a name="related-links"></a>Ligações relacionadas
* O ADO.NET 4.6 foi libertada 20 de Julho de 2015. Um anúncio de blogue da equipa do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* Foi lançada ADO.NET 4.5 15 de Agosto de 2012. Um anúncio de blogue da equipa do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * Uma mensagem de blogue sobre ADO.NET 4.5.1 está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [Lista de versão do protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
* [Firewall de base de dados SQL do Azure](sql-database-firewall-configure.md)
* [Como: configurar as definições da firewall na base de dados do SQL Server](sql-database-configure-firewall-settings.md)

