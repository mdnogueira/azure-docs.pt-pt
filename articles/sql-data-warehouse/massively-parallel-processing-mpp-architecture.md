---
title: Arquitetura MPP - Azure SQL Data Warehouse? | Microsoft Docs
description: "Saiba como o Azure SQL Data Warehouse combina processamento paralelo em grande escala (MPP) com o storage do Azure para alcançar elevado desempenho e escalabilidade."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: architecture
ms.date: 11/15/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 4c230eb0633b2917b90a5c1f9f4176882bfd0290
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>O Azure SQL Data Warehouse - paralelo em grande escala (MPP) arquitetura de processamento
Saiba como o Azure SQL Data Warehouse combina processamento paralelo em grande escala (MPP) com o storage do Azure para alcançar elevado desempenho e escalabilidade. 

## <a name="mpp-architecture-components"></a>Componentes de arquitetura MPP
SQL Data Warehouse tira partido de uma arquitetura para distribuir computacional processamento de dados em vários nós de ampliação. A unidade de escala é uma abstração da capacidade de computação que é conhecida como uma unidade do armazém de dados. O SQL Data Warehouse separa de computação de armazenamento que permitem que o utilizador, como o utilizador ao dimensionar a computação independentemente dos dados no seu sistema.

![Arquitetura do SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

O SQL Data Warehouse utiliza uma arquitetura de nó com base. Aplicações de ligarem e emitir comandos de T-SQL para um nó de controlo, o que é o único ponto de entrada para o armazém de dados. O nó de controlo é executado o motor MPP que otimiza consultas para processamento paralelo e, em seguida, passa operações em nós de computação para trabalhar em paralelo. Os nós de computação armazenarem todos os dados de utilizador no armazenamento do Azure e executam as consultas paralelas. O serviço de movimento de dados (DMS) é um serviço de interno de ao nível do sistema que move os dados em todos os nós conforme for necessário para executar consultas em paralelo e devolver resultados precisos. 

Com armazenamento e computação desacoplados, o SQL Data Warehouse pode:

* Independentemente tamanho poder de computação, independentemente das suas necessidades de armazenamento.
* Aumentar ou diminuir o poder de computação sem mover dados.
* Colocar em pausa a capacidade de computação, mantendo os dados intactos, pelo que apenas paga armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

### <a name="azure-storage"></a>Storage do Azure
O SQL Data Warehouse utiliza armazenamento do Azure para manter os seus dados de utilizador.  Uma vez que os dados são armazenados e geridos pelo armazenamento do Azure, o SQL Data Warehouse cobra separadamente para consumo de armazenamento. Dados propriamente ditos estão em partição horizontal em **distribuições** para otimizar o desempenho do sistema. Pode escolher qual padrão de fragmentação a utilizar para distribuir os dados ao definir a tabela. Armazém de dados SQL suporta estes padrões de fragmentação:

* Hash
* Round Robin
* Replicar

### <a name="control-node"></a>Nó de controlo

O nó de controlo é o "-brain" do armazém de dados. É o front-end que interage com todas as ligações e aplicações. O motor MPP é executado no nó de controlo para otimizar e coordenar consultas paralelas. Quando submete uma consulta T-SQL ao SQL Data Warehouse, o nó de controlo transforma-a em consultas que são executadas em relação a cada distribuição em paralelo.

### <a name="compute-nodes"></a>Nós de computação

Os nós de computação de fornecer a cálculo adequada. Distribuições mapeiam para nós de computação para processamento. Como paga mais recursos de computação, o SQL Data Warehouse mapeia novamente as distribuições em nós de computação disponíveis. O número de intervalos de nós de 1 a 60 de computação e é determinado pelo nível de serviço para o armazém de dados.

Cada nó de computação tem um ID de nó que está visível nas vistas de sistema. Pode ver o ID de nó de computação, procurando a coluna de node_id nas vistas de sistema cujos nomes começam por sys.pdw_nodes. Para obter uma lista destas vistas de sistema, consulte [vistas de sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Serviço de movimento de dados
Serviço de movimento de dados (DMS) é a tecnologia de transporte de dados que coordena movimento de dados entre os nós de computação. Algumas consultas necessitam de movimento de dados para garantir que as consultas paralelas devolvem resultados precisos. Quando for necessário o movimento de dados, o DMS garante que obtém os dados adequados para a localização correta. 

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento paralelo consultas que executar nos dados distribuídos. Quando o SQL Data Warehouse executa uma consulta, o trabalho está dividido em 60 consultas mais pequenas que são executadas em paralelo. Cada uma das 60 consultas mais pequenas é executada das distribuições de dados. Cada nó de computação gere um ou mais de 60 distribuições. Um armazém de dados com recursos de computação máximo tem uma distribuição por nó de computação. Um armazém de dados com recursos de computação mínima tem todas as distribuições no nó de computação de um.  

## <a name="hash-distributed-tables"></a>Tabelas hash distribuída
Uma tabela hash de distribuída pode fornecer o desempenho de consulta mais elevado para associações e agregações em tabelas grandes. 

Para dividir os dados para uma tabela hash distribuída, o SQL Data Warehouse utiliza uma função de hash deterministicamente atribuir cada linha a distribuição de um. Na definição de tabela, uma das colunas é designada como a coluna de distribuição. A função hash utiliza os valores na coluna de distribuição para atribuir a cada linha para distribuição.

O diagrama seguinte ilustra como um completo (tabela não distribuído) obtém armazenado como uma tabela hash distribuída. 

![Tabela de distribuídas](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "tabela distribuídas")  

* Cada linha pertence a uma distribuição.  
* Um algoritmo de hash determinista atribui cada linha a distribuição de um.  
* O número de linhas de tabela por distribuição varia conforme exibido os tamanhos de tabelas diferentes.

Existem considerações de desempenho para a seleção de uma coluna de distribuição, como distinctness, desfasamento de dados e os tipos de consultas que são executadas no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas round robin
Uma tabela de round robin é a tabela mais simples para criar e oferece um desempenho rápido quando utilizado como uma tabela de testes de cargas.

Uma tabela distribuída round robin distribui dados uniformemente entre a tabela, mas sem qualquer otimização adicional. Uma distribuição pela primeira vez é escolhida aleatoriamente e, em seguida, em que memórias intermédias de linhas são atribuídas sequencialmente para distribuições. É rápida carregar dados para uma tabela de round robin, mas o desempenho das consultas, muitas vezes, pode ser melhor com tabelas hash distribuído. Associações em tabelas de round robin requerem reshuffling dados e demora mais tempo.


## <a name="replicated-tables"></a>Tabelas replicadas
Uma tabela não replicada fornece o desempenho de consulta mais rápido para tabelas pequenos.

Uma tabela que é replicada coloca em cache uma cópia completa da tabela em cada nó de computação. Por conseguinte, uma tabela a ser replicada remove a necessidade para transferir dados entre os nós de computação antes de uma associação ou agregação. Tabelas replicadas são melhor utilizadas com tabelas pequenas. É necessário armazenamento adicional e existem sobrecargas adicionais que são cobradas ao escrever os dados que tornam grande tabelas impractical.  

O diagrama seguinte mostra uma tabela não replicada. Armazém de dados do SQL Server, a tabela não replicada é colocado em cache no primeiro de distribuição em cada nó de computação.  

![Tabela de replicados](media/sql-data-warehouse-distributed-data/replicated-table.png "replicados tabela") 

## <a name="next-steps"></a>Passos seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de exemplo][load sample data] rapidamente. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] quando se deparar com terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Criar pedido de suporte]
* [Fórum do MSDN]
* [Fórum do Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
