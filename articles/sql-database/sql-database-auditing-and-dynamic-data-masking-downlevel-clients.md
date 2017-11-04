---
title: Tabela de auditoria, redirecionamento de TDS e os pontos finais IP para a SQL Database do Azure | Microsoft Docs
description: "Saiba mais sobre a auditoria, redirecionamento de TDS IP e as alterações de ponto final quando implementar a tabela de auditoria na base de dados do Azure SQL."
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: 
ms.assetid: 4ef19ed1-e798-43a2-ad99-0e563f93ab53
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: giladm
ms.openlocfilehash: 42c89f09eee4394fec7d2f33f51ddc5875587530
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>Suportem de clientes de nível inferior de base de dados SQL - e ponto final de IP é alterado para auditoria de tabela

> [!IMPORTANT]
> Este documento só se aplica a auditoria de tabela, que é **agora preteridos**.<br>
> Utilize a nova [auditoria de Blob](sql-database-auditing.md) método, que **não** requerem modificações de cadeia de ligação de cliente de nível inferior. Podem encontrar informações adicionais sobre a auditoria de Blob [começar com a auditoria de base de dados do SQL Server](sql-database-auditing.md).

[Base de dados de auditoria](sql-database-auditing.md) funciona automaticamente com os clientes de SQL que suportam redirecionamento de TDS. Tenha em atenção que redirecionamento não se aplica ao utilizar o método de auditoria de Blob.

## <a id="subheading-1"></a>Suporte de clientes de nível inferior
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

## <a id="subheading-2"></a>Alterações de ponto final IP ao ativar a auditoria
Tenha em atenção que, quando ativar a auditoria de tabela, o ponto final de IP da base de dados será alterado. Se tiver definições de strict firewall, Atualize as definições de firewall em conformidade.

O novo ponto de final IP de base de dados irá depender da região de base de dados:

| Região de base de dados | Pontos finais IP possíveis |
| --- | --- |
| China Norte |139.217.29.176, 139.217.28.254 |
| Leste da China |42.159.245.65, 42.159.246.245 |
| Leste da Austrália |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Sudeste da Austrália |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Sul do Brasil |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| EUA Central |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| E.u. a central EUAP |52.180.178.16, 52.180.176.190 |
| Ásia Oriental |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| EUA Leste 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| EUA Leste |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| EUA Leste EUAP |52.225.190.86, 52.225.191.187 |
| Índia Central |104.211.98.219, 104.211.103.71 |
| Sul da Índia |104.211.227.102, 104.211.225.157 |
| Índia Ocidental |104.211.161.152, 104.211.162.21 |
| Leste do Japão |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Oeste do Japão |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| EUA Centro-Norte |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europa do Norte |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| EUA Centro-Sul |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sudeste Asiático |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa Ocidental |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| EUA Oeste |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| EUA Oeste 2 |13.66.224.156, 13.66.227.8 |
| EUA Centro-Oeste |52.161.29.186, 52.161.27.213 |
| Canadá Central |13.88.248.106, 13.88.248.110 |
| Leste do Canadá |40.86.227.82, 40.86.225.194 |
| Norte do Reino Unido |13.87.101.18, 13.87.100.232 |
| Sul do Reino Unido 2 |13.87.32.202, 13.87.32.226 |
