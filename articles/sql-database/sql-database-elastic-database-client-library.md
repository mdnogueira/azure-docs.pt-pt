---
title: "Criação de bases de dados em nuvem dimensionáveis | Microsoft Docs"
description: "Criar aplicações de base de dados .NET dimensionáveis com a biblioteca de clientes de base de dados elástica"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 1f11c52d-13c1-4994-b9b1-5b1ae2f9255f
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: ddove
ms.openlocfilehash: 721b87c67aa5e8002f21faca5a10fe41b8958e1e
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="building-scalable-cloud-databases"></a>Criar bases de dados de nuvem escaláveis
Aumentar horizontalmente bases de dados pode ser facilmente conseguido utilizando ferramentas dimensionáveis e funcionalidades para a SQL Database do Azure. Em particular, pode utilizar o **biblioteca de clientes de base de dados elástica** para criar e gerir bases de dados de escalamento horizontal. Esta funcionalidade permite-lhe desenvolver facilmente aplicações em partição horizontal com centenas — ou mesmo milhares — das bases de dados SQL do Azure. [As tarefas elásticas](sql-database-elastic-jobs-powershell.md) , em seguida, podem ser utilizados para ajudar a facilitar a gestão destas bases de dados.

Para transferir:
* a versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).
* a versão de Java da biblioteca, consulte [repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).

## <a name="documentation"></a>Documentação
1. [Introdução às ferramentas de Base de Dados Elástica](sql-database-elastic-scale-get-started.md)
2. [Funcionalidades de base de dados elásticas](sql-database-elastic-scale-introduction.md)
3. [Gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
4. [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
6. [Consultas de várias partições horizontais](sql-database-elastic-scale-multishard-querying.md)
7. [Adicionar uma partição horizontal com as ferramentas de base de dados elástica](sql-database-elastic-scale-add-a-shard.md)
8. [Aplicações de multi-inquilinos com ferramentas de base de dados elástica e a segurança ao nível da linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Atualizar aplicações de biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Descrição geral de consultas elástico](sql-database-elastic-query-overview.md)
11. [Glossário das ferramentas das bases de dados elásticas](sql-database-elastic-scale-glossary.md)
12. [Biblioteca de clientes de base de dados elástica com o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de cliente de bases de dados elásticas com Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-client-library.md) 
16. [Perguntas mais frequentes sobre as ferramentas de base de dados elástica](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Capacidades de cliente
Aumentar horizontalmente aplicações utilizando *fragmentação* apresenta desafios para o programador, bem como o administrador. A biblioteca de clientes simplifica as tarefas de gestão, fornecendo as ferramentas que permitem a ambos os programadores e os administradores gerem as bases de dados de escalamento horizontal. Um exemplo típico, há muitas bases de dados, conhecidos como "shards", para gerir. Os clientes estão colocalizados na mesma base de dados e existir uma base de dados por cliente (um esquema único inquilino). A biblioteca de clientes inclui estas funcionalidades:

- **Gestão de mapa de partições horizontais**: uma base de dados especiais chamado o "Gestor de mapa de partições horizontais". Gestão de mapa de partições horizontais é a capacidade para uma aplicação gerir os metadados sobre os shards. Os programadores podem utilizar esta funcionalidade para registar as bases de dados como shards, descrevem os mapeamentos de chaves de fragmentação individuais ou intervalos de chaves para essas bases de dados e manter estes metadados como o número e medida que evolui da composição de bases de dados para refletir as alterações de capacidade. Sem a biblioteca de clientes de base de dados elástica, terá de passam muito tempo a escrever o código de gestão quando implementar fragmentação. Para obter mais informações, consulte [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md).

- **Dados dependentes encaminhamento**: Imagine um pedido entra da aplicação. Com base no valor da chave de fragmentação do pedido, a aplicação tem de determinar a base de dados correto com base no valor de chave. Em seguida, abre-se uma ligação à base de dados para processar o pedido. Dados dependentes encaminhamento fornece a capacidade para abrir as ligações com uma única chamada fácil para o mapa de partições horizontais da aplicação. Dados dependentes encaminhamento foi outra área do código de infraestrutura que agora é abrangido por funcionalidade na biblioteca de cliente da base de dados elásticas. Para obter mais informações, consulte [dados dependentes encaminhamento](sql-database-elastic-scale-data-dependent-routing.md).
- **Consultas de partições horizontais multi (MSQ)**: consultar várias partições horizontais funciona quando um pedido envolve shards várias (ou todos os). Uma consulta de partições horizontais multi executa o mesmo código de T-SQL em todas as partições horizontais ou um conjunto de partições horizontais. Os resultados dos participantes shards são intercalados num resultado geral definido utilizando UNION ALL semântica. A funcionalidade como expostos através da biblioteca de clientes processa muitas tarefas, incluindo: gestão de ligações, gestão de thread, processamento de falhas e resultados intermédios processamento. MSQ pode consultar até centenas de partições horizontais. Para obter mais informações, consulte [consultar várias partições horizontais](sql-database-elastic-scale-multishard-querying.md).

Em geral, os clientes utilizando ferramentas de base de dados elástica podem esperar obter a funcionalidade completa de T-SQL ao submeter operações de partições horizontais local, por oposição a operações de cross-partições horizontais que tem as seus próprios semântica.



## <a name="next-steps"></a>Passos seguintes

- Biblioteca de clientes de base de dados elásticas ([.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22)) - a **transferir** a biblioteca.

- [Começar a utilizar as ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md) - para experimentar o **aplicação de exemplo** que demonstra as funções do cliente.

- GitHub ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md)) – para tornar as suas contribuições para o código.
- [Descrição geral de consulta elástico de base de dados SQL do Azure](sql-database-elastic-query-overview.md) - para utilizar consultas elásticas.

- [Mover dados entre bases de dados de nuvem de escalamento horizontal](sql-database-elastic-scale-overview-split-and-merge.md) - para obter instruções sobre como utilizar o **ferramenta de intercalação de divisão**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

