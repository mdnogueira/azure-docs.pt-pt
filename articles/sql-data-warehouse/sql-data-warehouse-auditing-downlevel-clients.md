---
title: "Suportem de clientes de nível inferior do armazém de dados do SQL Server para auditoria de dados | Microsoft Docs"
description: "Saiba mais sobre o suporte de clientes de nível inferior do armazém de dados do SQL Server para auditoria de dados"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: dfe29ff3-dfeb-4309-83c0-c1a300f4f44e
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: a7ea6141285a0098339f1e071af2592dd4535c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse----downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Suportem de clientes de nível inferior do armazém de dados do SQL Server - para máscara de dados de auditoria e dinâmicos
[Auditoria](sql-data-warehouse-auditing-overview.md) funciona com os clientes de SQL que suportam redirecionamento de TDS.

Qualquer cliente que implementa TDS 7.4 também deve suportar redirecionamento. Exceções a este incluem JDBC 4.0 em que a funcionalidade de redirecionamento não é totalmente suportada e Tedious para Node.JS no qual redirecionamento não foi implementada.

"Em clientes de", ou seja, que suporte TDS 7.3 e abaixo - o FQDN do servidor na ligação de cadeia de versão deve ser modificada:

Original FQDN do servidor na cadeia de ligação: <*nome do servidor*>. database.windows.net

Modificação FQDN do servidor na cadeia de ligação: <*nome do servidor*> .database. **segura**. windows.net

Uma lista parcial de "Clientes de nível inferior" inclui:

* O .NET 4.0 e abaixo
* ODBC 10.0 e abaixo.
* JDBC (enquanto JDBC suporta TDS 7.4, a funcionalidade de redirecionamento do TDS não é totalmente suportada)
* Tedious (para Node.JS)

**Remark:** servidor acima modificação FDQN poderá ser útil também aplicar uma política de auditoria de nível de servidor de SQL, sem necessidade de uma configuração passo em cada base de dados (mitigação temporária).     

