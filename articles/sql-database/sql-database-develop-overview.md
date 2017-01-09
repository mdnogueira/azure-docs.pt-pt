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
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 18dc3cce7451d90b6b65b990b80c05e7f6decb56


---
# <a name="sql-database-application-development-overview"></a>Descrição Geral da Programação de Aplicações de Base de Dados SQL
Este artigo explica as considerações básicas que um programador deve conhecer ao escrever códigos para ligar à base de dados SQL do Azure.

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar uma firewall baseada no servidor, ver propriedades do servidor, ligar com o SQL Server Management Studio, consultar a base de dados mestra, criar uma base de dados de exemplo e uma base de dados em branco, consultar as propriedades da base de dados, ligar com o SQL Server Management Studio e consultar a base de dados de exemplo, veja [Tutorial de Introdução](sql-database-get-started.md).
>

## <a name="language-and-platform"></a>Linguagem e plataforma
Estão disponíveis exemplos de código para várias linguagens de programação e plataformas. Pode encontrar ligações para exemplos de código em: 

* Mais informações: [Bibliotecas de ligação para Base de Dados SQL e SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limitações de recursos
A Base de Dados SQL do Azure gere os recursos disponíveis para uma base de dados através de dois mecanismos diferentes: Governação de Recursos e Imposição de Limites.

* Mais Informações: [Limites de recursos de Base de Dados SQL do Azure](sql-database-resource-limits.md)

## <a name="security"></a>Segurança
A Base de Dados SQL do Azure fornece recursos para limitar o acesso, proteger os dados e monitorizar as atividades numa Base de Dados SQL.

* Mais Informações: [Proteger a Base de Dados SQL](sql-database-security-overview.md)

## <a name="authentication"></a>Autenticação
* A Base de Dados SQL do Azure suporta utilizadores e inícios de sessão de autenticação do SQL Server, bem como utilizadores e inícios de sessão de [autenticação do Azure Active Directory](sql-database-aad-authentication.md).
* Tem de especificar uma base de dados específica, em vez de utilizar a predefinição para a base de dados *mestra*.
* Não pode utilizar o Transact-SQL **UTILIZAR myDatabaseName;** instrução na base de dados SQL para mudar para outra base de dados.
* Mais informações: [segurança da Base de Dados SQL: gerir a segurança de início de sessão e acesso da base de dados](sql-database-manage-logins.md)

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório ao ligar à Base de Dados SQL, o seu código deverá repetir a chamada.  Recomendamos que tente novamente a lógica de repetição utilize a lógica de backoff, para que não sobrecarregue a Base de Dados SQL com vários clientes em simultâneo a tentar novamente.

* Exemplos de código: para obter exemplos de código que ilustram a lógica de repetição, consulte exemplos para a linguagem da sua preferência em: [bibliotecas de ligação para Base de Dados SQL e SQL Server](sql-database-libraries.md)
* Mais informações: [mensagens de erro para os programas do cliente de Base de Dados SQL](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Gerir Ligações
* Na sua lógica de ligação de cliente, substitua o tempo limite predefinido para 30 segundos.  A predefinição de 15 segundos é demasiado curta para ligações que dependem da Internet.
* Se estiver a utilizar um [conjunto de ligações](http://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de que fecha a ligação assim que o seu programa não estiver a utilizá-la ativamente e não estiver a preparar-se para reutilizá-la.

## <a name="network-considerations"></a>Considerações de Rede
* No computador que aloja o seu programa cliente, certifique-se de que a firewall permite a comunicação TCP de saída na porta 1433.  Mais informações: [Configurar uma firewall uma Base de Dados SQL do Azure](sql-database-configure-firewall-settings.md)
* Se o seu programa cliente se liga à Base de Dados SQL V12 enquanto o cliente executa numa máquina virtual do Azure (VM), tem de abrir determinados intervalos de portas na VM. Mais informações: [portas para além do 1433 para ADO.NET 4.5 e Base de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)
* As ligações de cliente para Base de Dados SQL V12 do Azure, por vezes, ignoram o proxy e interagem diretamente com a base de dados. As portas que não sejam 1433 tornam-se importantes. Mais informações: [portas para além do 1433 para ADO.NET 4.5 e Base de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Fragmentação de Dados com Escala Elástica
A Escala Elástica simplifica o processo de aumentar horizontalmente (e reduzir). 

* [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
* [Introdução à Pré-visualização da Escala Elástica da Base de Dados SQL do Azure](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Passos seguintes
Explore todas as [capacidades da Base de Dados SQL](https://azure.microsoft.com/services/sql-database/).




<!--HONumber=Dec16_HO4-->


