---
title: "Descrição Geral da Programação de Aplicações de Bases de Dados SQL | Microsoft Docs"
description: "Aprenda sobre as bibliotecas de conetividade disponíveis e as melhores práticas para as aplicações ligarem à Base de Dados SQL."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: genemi
ms.assetid: 67c02204-d1bd-4622-acce-92115a7cde03
ms.service: sql-database
ms.custom: develop apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
ms.openlocfilehash: 5948db9a52dc24d75f3fecc4ed166dd327061b37
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="sql-database-application-development-overview"></a>Descrição geral de desenvolvimento de aplicações de base de dados SQL
Este artigo explica as considerações básicas que um programador deve conhecer ao escrever códigos para ligar à base de dados SQL do Azure.

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar uma firewall baseada no servidor, ver propriedades do servidor, ligar com o SQL Server Management Studio, consultar a base de dados mestra, criar uma base de dados de exemplo e uma base de dados em branco, consultar as propriedades da base de dados, ligar com o SQL Server Management Studio e consultar a base de dados de exemplo, veja [Tutorial de Introdução](sql-database-get-started-portal.md).
>

## <a name="language-and-platform"></a>Linguagem e plataforma
Estão disponíveis exemplos de código para várias linguagens de programação e plataformas. Pode encontrar ligações para exemplos de código em: 

* Obter mais informações: [bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Ferramentas 
Pode tirar partido de ferramentas de código aberto, como [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli) ou [VS Code](https://code.visualstudio.com/). Além disso, a Base de Dados SQL do Azure funciona com ferramentas da Microsoft como o [Visual Studio](https://www.visualstudio.com/downloads/) e o [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  Também pode utilizar o Portal de Gestão do Azure, o PowerShell e as API REST que o ajudam a obter produtividade adicional.

## <a name="resource-limitations"></a>Limitações de recursos
A Base de Dados SQL do Azure gere os recursos disponíveis para uma base de dados através de dois mecanismos diferentes: Governação de Recursos e Imposição de Limites.

* Obter mais informações: [dos limites de recursos de base de dados do Azure SQL](sql-database-service-tiers.md).

## <a name="security"></a>Segurança
A Base de Dados SQL do Azure fornece recursos para limitar o acesso, proteger os dados e monitorizar as atividades numa Base de Dados SQL.

* Obter mais informações: [proteger a sua base de dados do SQL Server](sql-database-security-overview.md).

## <a name="authentication"></a>Autenticação
* A Base de Dados SQL do Azure suporta utilizadores e inícios de sessão de autenticação do SQL Server, bem como utilizadores e inícios de sessão de [autenticação do Azure Active Directory](sql-database-aad-authentication.md).
* Tem de especificar uma base de dados específica, em vez de utilizar a predefinição para a base de dados *mestra*.
* Não pode utilizar o Transact-SQL **UTILIZAR myDatabaseName;** instrução na base de dados SQL para mudar para outra base de dados.
* Obter mais informações: [segurança da base de dados SQL: Gerir a segurança da base de dados de início de sessão e acesso](sql-database-manage-logins.md).

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório ao ligar à Base de Dados SQL, o seu código deverá repetir a chamada.  Recomendamos que tente novamente a lógica de repetição utilize a lógica de backoff, para que não sobrecarregue a Base de Dados SQL com vários clientes em simultâneo a tentar novamente.

* Exemplos de código: para obter exemplos de código que ilustram repetir lógica, consulte amostras para o idioma da sua preferência em: [bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md).
* Obter mais informações: [mensagens de erro para programas de cliente da base de dados SQL](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Gerir ligações
* Na sua lógica de ligação de cliente, substitua o tempo limite predefinido para 30 segundos.  A predefinição de 15 segundos é demasiado curta para ligações que dependem da Internet.
* Se estiver a utilizar um [conjunto de ligações](http://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de que fecha a ligação assim que o seu programa não estiver a utilizá-la ativamente e não estiver a preparar-se para reutilizá-la.

## <a name="network-considerations"></a>Considerações de rede
* No computador que aloja o seu programa cliente, certifique-se de que a firewall permite a comunicação TCP de saída na porta 1433.  Obter mais informações: [configurar uma firewall da SQL Database do Azure](sql-database-configure-firewall-settings.md).
* Se o seu programa cliente se liga à base de dados do SQL Server enquanto o cliente é executada numa máquina virtual do Azure (VM), tem de abrir determinados intervalos de portas na VM. Obter mais informações: [portas para além de 1433 para ADO.NET 4.5 e a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* Ligações de cliente para a SQL Database do Azure, por vezes, ignorar o proxy e interajam diretamente com a base de dados. As portas que não sejam 1433 tornam-se importantes. Para obter mais informações, [arquitetura de conectividade da base de dados do Azure SQL](sql-database-connectivity-architecture.md) e [portas para além de 1433 para ADO.NET 4.5 e a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Fragmentação de dados com uma escala elástica
Dimensionamento elástico simplifica o processo de saída (e na). 

* [Padrões de conceção para aplicações SaaS multi-inquilino com a base de dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* [Dados dependentes encaminhamento](sql-database-elastic-scale-data-dependent-routing.md).
* [Introdução à pré-visualização do dimensionamento elástico de base de dados do SQL do Azure](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>Passos seguintes
Explore todas as [capacidades da Base de Dados SQL](sql-database-technical-overview.md).
