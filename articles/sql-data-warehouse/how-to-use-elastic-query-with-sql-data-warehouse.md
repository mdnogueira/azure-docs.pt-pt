---
title: "Conceitos de consulta elástico com o armazém de dados SQL do Azure | Microsoft Docs"
description: "Conceitos de consulta elásticos com o Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 295cc59fdb23105534b4e7431902eaa720643330
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>Como utilizar consulta elástico com o SQL Data Warehouse



Consulta elástica com o Azure SQL Data Warehouse permite-lhe escrever Transact-SQL na base de dados do SQL Server que é enviado remotamente para uma instância de armazém de dados SQL do Azure através da utilização de tabelas externas. Se utilizar esta funcionalidade fornece reduções de custos e mais performant arquiteturas, dependendo do cenário.

Esta funcionalidade permite que os dois cenários principais:

1. Isolamento de domínio
2. Execução da consulta remota

### <a name="domain-isolation"></a>Isolamento de domínio

Isolamento de domínio refere-se o cenário de mart dados clássico. Em certos cenários, um poderá pretender fornecer um domínio lógico de dados aos utilizadores a jusante que são isolados do resto do armazém de dados, por diversos motivos, incluindo, mas não se limitando a:

1. Isolamento de recursos - base de dados SQL está otimizado para servir de uma base de utilizadores em simultâneo que serve ligeiramente diferentes cargas de trabalho que as consultas analíticas grande que o armazém de dados está reservado para grande. Isolamento assegura que as cargas de trabalho à direita são servidas pelas ferramentas de direita.
2. Isolamento de segurança - para separar um subconjunto de dados autorizados seletivamente através de determinados esquemas.
3. Sandboxing - fornecem um conjunto de amostra de dados como um "playground" para explorar as consultas de produção etc.

Consulta elástica pode fornecer a capacidade de facilmente selecione subconjuntos de dados de armazém de dados do SQL Server e movê-la numa instância de base de dados do SQL Server. Além disso, este isolamento não preclude a capacidade de ativar também a execução de consulta remota permite cenários de "cache" mais interessantes.

### <a name="remote-query-execution"></a>Execução da consulta remota

Consulta elástica permite a execução de consulta remota numa instância de armazém de dados do SQL Server. Um pode utilizar o máximo proveito de base de dados SQL e SQL data warehouse, separando os dados de acesso frequente e amovíveis entre as duas bases de dados. Os utilizadores podem manter os dados mais recentes dentro de uma base de dados do SQL Server, que pode fornecer relatórios e grande número de utilizadores de empresas de média. No entanto, quando é necessário mais dados ou cálculo, um utilizador pode descarregar a parte da consulta para uma instância de armazém de dados do SQL Server onde podem ser processados em grande escala agregados muito mais rápido e mais eficiente.



## <a name="elastic-query-overview"></a>Descrição geral de consulta elástico

Uma consulta elástica pode ser utilizada para disponibilizar dados localizados dentro de um de dados SQL do armazém de instâncias de base de dados do SQL Server. Consulta elástica permite consultas de uma base de dados do SQL Server, consulte a tabelas numa instância de armazém de dados remota do SQL Server. 

O primeiro passo é criar uma definição de origem de dados externas refere-se para a instância de armazém de dados do SQL Server, que utiliza as credenciais de utilizador existente no SQL data warehouse. Sem alterações são necessárias na instância de armazém de dados do SQL Server remota. 

> [!IMPORTANT] 
> 
> Precisa de ter permissão ALTER qualquer origem de dados externa. Esta permissão está incluído com a permissão ALTER DATABASE. São necessárias permissões de ALTER qualquer origem de dados externa para fazer referência a origens de dados remota.

Em seguida criamos uma definição de tabela externa remoto na instância de base de dados SQL que aponta para uma tabela remota no armazém de dados do SQL Server. Quando utiliza uma consulta que utiliza uma tabela externa, a parte da consulta consultar a tabela externa é enviada para a instância de armazém de dados do SQL Server para ser processado. Depois de concluída a consulta, o conjunto de resultados é enviado para a instância de base de dados do SQL Server chamada. Para um tutorial breve de configuração de uma consulta elástico entre a base de dados SQL e do armazém de dados do SQL Server, consulte o [configurar consulta elástico com o SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Para obter mais informações sobre consulta elástico com base de dados do SQL Server, consulte o [descrição geral de consulta elástico de SQL Database do Azure][Azure SQL Database elastic query overview].



## <a name="best-practices"></a>Melhores práticas

### <a name="general"></a>Geral

- Quando utilizar a execução da consulta remota, certifique-se apenas estiver a seleção de colunas necessárias e aplicar os filtros à direita. Não só este aumento computação necessária, mas também aumenta o tamanho do conjunto de resultados e, por conseguinte, a quantidade de dados que têm de ser movidos entre as duas instâncias.
- Manter os dados para fins analíticos no armazém de dados do SQL Server e base de dados SQL no columnstore em cluster para um desempenho analytiIcal.
- Certifique-se de que as tabelas de origem são particionadas para movimento de dados e de consulta.
- Certifique-se de que as instâncias de base de dados do SQL Server utilizadas como uma cache são divididos em partições para ativar as atualizações mais granulares e uma gestão mais fácil. 
- Idealmente, utilize PremiumRS bases de dados porque fornecem as vantagens analíticas de columnstore em cluster indexação com um foco em cargas de trabalho de e/s intensivas com um desconto de bases de dados Premium.
- Após o carregamentos, utilize load ou colunas de identificação de data efetiva para upserts nas instâncias de base de dados do SQL Server para manter a integridade da origem de cache. 
- Crie um início de sessão separado e o utilizador na sua instância de armazém de dados SQL para as credenciais de início de sessão remoto do SQL Server da base de dados definido na origem de dados externas. 

### <a name="elastic-querying"></a>Consultar elástico

- A tabela externa e a tabela em cache interally existam como objetos diferentes, com a instância de base de dados do SQL Server. Considere criar uma vista ao longo da parte superior da parte da tabela e a tabela externa em cache de que as uniões as tabelas e aplica os filtros no ponto de limites de cada tabela.

  Imagine que gostaria de manter o ano mais recente dos dados numa instância de base de dados do SQL Server. Temos de duas tabelas **ext. As ordens**, que referencia o armazém de dados ordena tabelas, e **dbo. As ordens** que representa o mais recente visão de anos de dados a instância de base de dados do SQL Server. Em vez de pedir aos utilizadores para decidir se pretende consultar uma tabela ou outro, iremos criar uma vista ao longo da parte superior do ambas as tabelas no ponto de partição do ano mais recente.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Uma vista produzidos de forma vamos o compilador de consulta determinar se tem de utilizar a instância de armazém de dados para responder a consulta de utilizadores. 

  Não há custos gerais da submeter, a compilação, em execução e mover dados associados a cada consulta elástica contra a instância de armazém de dados. Ser cognizant que cada consulta elástica é contabilizada nas suas ranhuras de concorrência e utiliza os recursos.  


- Se um planos desagregar ainda mais para o conjunto de resultados da instância de armazém de dados, considere a materialização-la numa tabela temporária na base de dados do SQL Server para um desempenho e para impedir a utilização de recursos desnecessários.

### <a name="moving-data"></a>Mover dados 

- Se for possível, manter a gestão de dados mais fácil e com tabelas de origem acrescentar-só de forma a que as atualizações são facilmente sustentável entre as instâncias de base de dados e do armazém de dados.
- Mova dados ao nível da partição com semântica de remoção e preencher para minimizar o custo de consulta no nível do armazém de dados e a quantidade de dados movidas para manter a instância de base de dados atualizados. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Quando escolher o Analysis Services do Azure vs SQL Database

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- Tencione utilizar a sua cache com uma ferramenta de BI submete grande número de consultas pequenos
- Tem de subsecond latência de consulta
- Tem experiência na gestão/desenvolver modelos para o Analysis Services 

#### <a name="sql-database"></a>Base de Dados SQL

- Pretende consultar os dados da cache com o SQL Server
- Terá de execução remota para determinados consultas
- Tem maiores requisitos de cache



## <a name="faq"></a>FAQ

P: Posso utilizar bases de dados dentro de um conjunto de bases de dados elásticas com elástico consulta?

R: Sim. Bases de dados SQL num agrupamento elástico pode utilizar a consulta elástica. 

P: existe um limite para quantas bases de dados posso utilizar para a consulta elástica?

R: lógicos servidores têm limites DTU no local para impedir que os clientes overspending acidental. Se pretende ativar a várias bases de dados para a consulta elástica juntamente com uma instância de armazém de dados do SQL Server, pode atingir a cap inesperadamente. Se isto ocorrer, submeta um pedido para aumentar o limite DTU no seu servidor lógico. Pode aumentar a quota por [criar um pedido de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e selecionando *Quota* como o tipo de pedido

P: Posso utilizar dados linha nível segurança/dinâmico máscara com elástico consulta?

R: que os clientes que pretendem utilizar as funcionalidades mais avançadas de segurança com a base de dados SQL podem fazê-lo pelo primeiro mover e armazenar os dados na base de dados SQL. Atualmente não é possível aplicar a segurança ao nível da linha ou de ddm de autor nos dados consultados através de tabelas externas. 

P: posso efetuar do meu instância de base de dados do SQL Server para instância de armazém de dados?

R: atualmente esta funcionalidade não é suportada. Visite a nossa [página comentários] [ Feedback page] para criar/voto para esta funcionalidade se esta é uma funcionalidade que gostaria de ver no futuro. 

P: Posso utilizar tipos geográficos como geometria/geografia?

R: pode armazenar os tipos geográficos no armazém de dados do SQL Server como valores de varbinary (Max). Quando consultar estas colunas utilizando consulta elástica, pode convertê-los para os tipos de adequada no tempo de execução.

![tipos geográficos](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->