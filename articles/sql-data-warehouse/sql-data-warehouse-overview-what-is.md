---
title: "O que é o Azure SQL Data Warehouse? | Microsoft Docs"
description: "Base de dados distribuída de nível empresarial capaz de processar volumes de petabytes de dados relacionais e não relacionais. É o primeiro armazém de dados em nuvem da indústria que pode aumentar, diminuir e ser colocado em pausa em segundos."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 575c49f83c8845edcea984459f3907490c62d269
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>O que é o Azure SQL Data Warehouse?
O Azure SQL Data Warehouse é uma base de dados para processamento paralelo em massa (MPP), baseada na cloud, de escalabilidade horizontal e relacional, capaz de processar grandes volumes de dados. 

SQL Data Warehouse:

* Combina a base de dados relacional do SQL Server com as capacidades de dimensionamento horizontal em nuvem do Azure. 
* Dissocia o armazenamento da computação.
* Ativa o aumento, diminuição, colocação em pausa e retoma da computação. 
* Integra-se na plataforma do Azure.
* Utiliza o Transact-SQL (T-SQL) e ferramentas do SQL Server.
* Está em conformidade com diversos requisitos de segurança legais e empresariais, como SOC e ISO.

Este artigo descreve as funcionalidades principais do SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>Arquitetura de processamento paralelo em massa
O SQL Data Warehouse é um sistema de base de dados distribuído de processamento paralelo em massa (MPP). Nos bastidores, o SQL Data Warehouse propaga os dados em muitas unidades de armazenamento e de processamento sem partilha. Os dados são armazenados numa camada de armazenamento Premium localmente redundante, sobre a qual os nós de Computação dinamicamente ligados executam consultas. O SQL Data Warehouse assume uma abordagem de “dividir e conquistar” para a execução de cargas e consultas complexas. Os pedidos são recebidos por um nó de Controlo, otimizados para distribuição e, em seguida, são transmitidos para os nós de Computação, que trabalham em paralelo.

Com armazenamento e computação desacoplados, o SQL Data Warehouse pode:

* Aumentar ou diminuir o tamanho do armazenamento, independentemente da computação.
* Aumentar ou diminuir o poder de computação sem mover dados.
* Colocar a capacidade de computação em pausa mantendo os dados intactos, pagando apenas pelo armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

O diagrama seguinte mostra a arquitetura de forma mais detalhada.

![Arquitetura do SQL Data Warehouse][1]

**Nó de controlo:** o nó de controlo gere e otimiza consultas. É o front-end que interage com todas as ligações e aplicações. No SQL Data Warehouse, o nó de controlo utiliza a tecnologia da Base de Dados SQL e a ligação ao mesmo é semelhante. Sob a superfície, o nó de Controlo coordena todos os movimentos dos dados e a computação necessária para executar consultas paralelas nos seus dados distribuídos. Quando submete uma consulta T-SQL ao SQL Data Warehouse, o nó de controlo transforma-a em consultas separadas que são executadas em paralelo em cada nó de computação.

**Nós de computação:** os nós de computação servem como o poder por trás do SQL Data Warehouse. São Bases de Dados SQL que armazenam os dados e processam a consulta. Quando adiciona dados, o SQL Data Warehouse distribui as filas entre os nós de computação. Os nós de computação são os trabalhadores que executam as consultas paralelas nos seus dados. Após o processamento, passam os resultados novamente para o nó de controlo. Para concluir a consulta, o nó de controlo agrega os resultados e devolve o resultado final.

**Armazenamento:** os dados são armazenados no Armazenamento de Blobs do Azure. Quando os nós de computação interagem com os seus dados, escrevem e leem diretamente de e para o armazenamento de blobs. Uma vez que o armazenamento do Azure se expande de forma transparente e vasta, o SQL Data Warehouse poderá fazer o mesmo. Como a computação e o armazenamento são independentes, o SQL Data Warehouse pode, de forma independente, dimensionar o armazenamento e a computação e vice-versa. O Armazenamento de Blobs do Azure é também totalmente tolerante a falhas e simplifica o processo de cópia de segurança e restauro.

**Serviço de Movimento de Dados:** o Serviço de Movimento de Dados (DMS) move dados entre os nós. O DMS dá aos nós de computação acesso aos dados de que necessitam para associações e agregações. O DMS não é um serviço do Azure. É um serviço do Windows que é executado em conjunto com a Base de Dados SQL em todos os nós. O DMS é um processo em segundo plano com o qual não é possível interagir diretamente. No entanto, pode observar os planos de consulta para ver quando ocorrem operações do DMS, uma vez que o movimento de dados é necessário para executar cada consulta em paralelo.

## <a name="optimized-for-data-warehouse-workloads"></a>Otimizado para cargas de trabalho do armazém de dados
A abordagem MPP é auxiliada por várias otimizações de desempenho específicas dos armazéns de dados, incluindo:

* Um otimizador de consultas distribuídas e um conjunto de estatísticas complexas em todos os dados. Utilizando informações sobre o tamanho e a distribuição dos dados, o serviço consegue otimizar as consultas ao avaliar o custo de operações de consulta distribuída específicas.
* Técnicas e algoritmos avançados integrados no processo de movimento de dados para mover os dados de forma eficiente entre recursos de computação, conforme for necessário para executar a consulta. Estas operações de movimento de dados são incorporadas e todas as otimizações ao Serviço de Movimento de Dados ocorrem automaticamente.
* Índices **columnstore** em cluster por predefinição. Ao utilizar o armazenamento baseado em colunas, o SQL Data Warehouse obtém em média até 5x mais ganhos de compressão em relação ao armazenamento orientado por linhas tradicional e até 10x ou mais ganhos de desempenho de consulta. As consultas de análises que precisam de analisar um grande número de linhas funcionam melhor em índices columnstore.


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>Desempenho previsível e dimensionável com Unidades do Data Warehouse
O SQL Data Warehouse está incorporado com tecnologias semelhantes às da Base de Dados SQL, o que significa que os utilizadores podem contar com desempenho consistente e previsível em consultas de análise. Os utilizadores devem constatar que o desempenho dimensiona de forma linear, à medida que adicionam ou subtraem nós de Computação. A alocação de recursos para o SQL Data Warehouse é medida em Unidades do Data Warehouse (DWUs). As DWUs são uma medida dos recursos subjacentes como CPU, memória e IOPS, que estão alocados ao seu SQL Data Warehouse. O aumento do número de DWUs aumenta os recursos e o desempenho. Especificamente, as DWUs ajudam a garantir o seguinte:

* Consegue dimensionar o seu armazém de dados, sem se preocupar com o hardware ou software subjacente.
* Pode prever uma melhoria de desempenho para um nível de DWU antes de alterar a computação do seu armazém de dados.
* O hardware e o software subjacentes da sua instância podem ser alterados ou movidos sem afetar o desempenho da sua carga de trabalho.
* A Microsoft pode melhorar a arquitetura subjacente do serviço sem afetar o desempenho da sua carga de trabalho.
* A Microsoft pode melhorar rapidamente o desempenho no SQL Data Warehouse, de uma forma dimensionável e que afete o sistema uniformemente.

As Unidades do Data Warehouse proporcionam uma medida de três métricas, que estão altamente correlacionadas com o desempenho da carga de trabalho do armazém de dados. As métricas chave da carga de trabalho seguintes dimensionam de forma linear com as DWUs.

**Análise/Agregação:** uma consulta de armazém de dados padrão, que analisa um grande número de linhas e, em seguida, efetua uma agregação complexa. Esta operação é exigente em termos de E/S e CPU.

**Carga:** a capacidade de ingerir dados no serviço. As cargas são melhor efetuadas com o PolyBase a partir dos Blobs do Armazenamento do Azure ou do Azure Data Lake. Esta métrica foi concebida para realçar aspetos da rede e da CPU do serviço.

**Create Table As Select (CTAS):** o CTAS mede a capacidade de copiar uma tabela. Isto envolve a leitura de dados do armazenamento, a distribuição dos mesmos pelos nós da aplicação e a escrita novamente no armazenamento. É uma operação exigente em termos de CPU, de E/S e da rede.

## <a name="built-on-sql-server"></a>Incorporado no SQL Server
O SQL Data Warehouse baseia-se no motor de base de dados relacional do SQL Server e inclui muitas das funcionalidades que se espera de um armazém de dados empresarial. Se já conhece o T-SQL, é fácil transferir os seus conhecimentos para o SQL Data Warehouse. Quer seja um utilizador avançado ou esteja apenas a começar, os exemplos em toda a documentação irão ajudá-lo a começar. Em geral, pode pensar sobre a forma como construímos os elementos de linguagem do SQL Data Warehouse da seguinte forma:

* O SQL Data Warehouse utiliza sintaxe de T-SQL para muitas operações. Suporta também um conjunto amplo de construções tradicionais de SQL, tais como procedimentos armazenados, funções definidas pelo utilizador, criação de partições de tabela, índices e agrupamentos.
* O SQL Data Warehouse contém também várias funcionalidades mais recentes do SQL Server, incluindo índices **columnstore** em cluster, integração do PolyBase e auditoria de dados (completa com avaliação de ameaças).
* Determinados elementos da linguagem T-SQL que são menos comuns para cargas de trabalho de armazém de dados, ou que são mais recentes para o SQL Server, poderão não estar disponíveis atualmente. Para obter mais informações, veja a [Migration documentation (Documentação de migração)][Migration documentation].

Com o Transact-SQL e a convergência de funcionalidades entre o SQL Server, o SQL Data Warehouse, a Base de Dados SQL e o Analytics Platform System, pode desenvolver uma solução que se adeque às suas necessidades de dados. Pode decidir onde pretende manter os seus dados, com base em requisitos de desempenho, segurança e escala, e, em seguida, transferir os dados conforme necessário entre sistemas diferentes.

## <a name="data-protection"></a>Proteção de dados
O SQL Data Warehouse armazena todos os dados no armazenamento localmente redundante Premium do Azure. Várias cópias síncronas dos dados são mantidas no centro de dados local para garantir a proteção transparente de dados contra falhas localizadas. Além disso, o SQL Data Warehouse efetua automaticamente cópias de segurança das bases de dados ativas (que não estão em pausa) a intervalos regulares através de Instantâneos de Armazenamento do Azure. Para saber mais sobre como funciona a cópia de segurança e o restauro, veja [Backup and restore overview (Descrição geral da cópia de segurança e do restauro)][Backup and restore overview].

## <a name="integrated-with-microsoft-tools"></a>Integrado com ferramentas da Microsoft
O SQL Data Warehouse também integra muitas das ferramentas com que os utilizadores do SQL Server podem estar familiarizados. Estas ferramentas incluem:

**Ferramentas tradicionais do SQL Server:** o SQL Data Warehouse, está totalmente integrado com o SQL Server Analysis Services, o Integration Services e o Reporting Services.

**Ferramentas baseadas na cloud:** o SQL Data Warehouse pode ser integrado em vários serviços do Azure, incluindo o Data Factory, o Stream Analytics, o Machine Learning e o Power BI. Para obter uma lista mais completa, veja [Integrated tools overview (Descrição geral de ferramentas integradas)][Integrated tools overview].

**Ferramentas de terceiros:** muitos fornecedores terceiros de ferramentas certificaram a integração das respetivas ferramentas com o SQL Data Warehouse. Para obter uma lista completa, veja [SQL Data Warehouse solution partners (Parceiros de soluções do SQL Data Warehouse)][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Cenários híbridos de origens de dados
O Polybase permite tirar partido dos seus dados de diferentes origens através da utilização de comandos familiares do T-SQL. O Polybase permite fazer consultas em dados não relacionais mantidos no Armazenamento de Blobs do Azure como se se tratasse de uma tabela normal. Utilize o Polybase para consultar dados não relacionais ou para importar dados não relacionais para o SQL Data Warehouse.

* O PolyBase utiliza tabelas externas para aceder a dados não relacionais. As definições de tabela são armazenadas no SQL Data Warehouse e pode aceder-lhes com o SQL e ferramentas, tal como acederia a dados relacionais normais.
* O Polybase é agnóstico em termos de integração. Expõe as mesmas funções e funcionalidades a todas as origens que suporta. Os dados lidos pelo Polybase podem ter vários formatos, incluindo ficheiros delimitados ou ficheiros ORC.
* Pode utilizar o PolyBase para aceder ao armazenamento de blobs que também está a ser utilizado como armazenamento para um cluster do HDInsight. Isto dá-lhe acesso aos mesmos dados com ferramentas relacionais e não relacionais.

## <a name="sla"></a>SLA
O SQL Data Warehouse oferece um contrato de nível de serviço (SLA) de nível de produto como parte do SLA do Microsoft Online Services. Para obter mais informações, visite [SLA para o SQL Data Warehouse][SLA for SQL Data Warehouse]. Para obter mais informações de SLA sobre todos os outros produtos, pode visitar a página [Contratos de Nível de Serviço] do Azure ou transferi-los na página [Volume Licensing (Licenciamento em Volume)][Volume Licensing]. 

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
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contratos de Nível de Serviço]: https://azure.microsoft.com/en-us/support/legal/sla/
