---
title: "Gestão de simultaneidade e carga de trabalho no SQL Data Warehouse | Microsoft Docs"
description: "Compreenda a gestão de simultaneidade e carga de trabalho no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gestão de simultaneidade e carga de trabalho do armazém de dados do SQL Server
Para fornecer um desempenho previsível à escala, ajuda-o armazém de dados SQL do Microsoft Azure controla os níveis de simultaneidade e alocações de recursos, como a memória e a atribuição de prioridades da CPU. Este artigo apresenta os conceitos de gestão de simultaneidade e carga de trabalho, explicar foi implementadas como ambas as funcionalidades e como pode controlá-los no seu armazém de dados. Destina-se a gestão de carga de trabalho do armazém de dados do SQL Server para o ajudar a suportar ambientes de vários utilizadores. Não se destina de cargas de trabalho do multi-inquilinos.

## <a name="concurrency-limits"></a>Limites de concorrência
SQL Data Warehouse permite até 1.024 ligações em simultâneo. Todas as 1.024 ligações podem submeter consultas em simultâneo. No entanto, para otimizar o débito, o SQL Data Warehouse pode colocar em fila algumas consultas para garantir que cada consulta recebe uma concessão de memória mínima. Colocação em fila ocorre no momento de execução da consulta. Por consultas colocação quando os limites de concorrência são atingidos, do armazém de dados do SQL Server pode aumentar débito total, garantindo que as consultas de Active Directory obtém acesso aos recursos de memória extremamente necessária.  

Limites de concorrência são regidos pelas dois conceitos: *consultas em simultâneo* e *ranhuras de concorrência*. Para uma consulta executar, deve executar o limite de simultaneidade de consulta e a alocação da ranhura de concorrência.

* Consultas em simultâneo são as consultas em execução ao mesmo tempo. Armazém de dados SQL suporta até 32 consultas em simultâneo em tamanhos de DWU maiores.
* Ranhuras de concorrência são alocadas com base no DWU. Cada 100 DWU fornece 4 ranhuras de concorrência. Por exemplo, um DW100 atribui 4 ranhuras de simultaneidade e DW1000 aloca 40. Cada consulta consome um ou mais simultaneidade ranhuras, dependentes do [classe de recursos](#resource-classes) da consulta. As consultas em execução na classe de recurso smallrc consumam um bloco de concorrência. As consultas em execução de uma classe de recurso superior consumam mais ranhuras de concorrência.

A tabela seguinte descreve os limites para consultas em simultâneo e de ranhuras de concorrência em vários tamanhos DWU.

### <a name="concurrency-limits"></a>Limites de concorrência
| DWU | Consultas em simultâneo máx. | Ranhuras de concorrência atribuídas |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

Quando for cumprida uma destes limiares, consultas novas são colocados em fila e executadas numa base first in, First Out.  Como um consultas concluída e o número de consultas e de ranhuras fica abaixo os limites, são lançadas consultas em fila. 

> [!NOTE]  
> *Selecione* consultas em execução no exclusivamente vistas de gestão dinâmica (DMVs) ou vistas de catálogo não são regidas por qualquer uma dos limites de concorrência. Pode monitorizar o sistema independentemente do número de consultas em execução no mesmo.
> 
> 

## <a name="resource-classes"></a>Classes de Recursos
As classes de recursos ajudam-no a controlar a alocação de memória e os ciclos de CPU dados a uma consulta. Pode atribuir dois tipos de classes de recurso a um utilizador no formato de funções de base de dados. Os dois tipos de classes de recursos são os seguintes:
1. Classes de recursos dinâmicos (**smallrc, mediumrc, largerc, xlargerc**) atribua uma variável quantidade de memória, dependendo de DWU atual. Isto significa que quando aumentar verticalmente para uma maior DWU, as suas consultas obtém automaticamente mais memória. 
2. Classes de recurso estático (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**) atribuir a mesma quantidade de memória, independentemente de DWU atual (desde que o DWU próprio tem suficiente memória). Isto significa que em DWUs maior, pode executar consultas mais cada classe de recursos em simultâneo.

Os utilizadores **smallrc** e **staticrc10** recebem uma quantidade menor de memória e pode tirar partido de concorrência superior. Em contrapartida, os utilizadores atribuídos a **xlargerc** ou **staticrc80** recebem grandes quantidades de memória, e, por conseguinte, menos as consultas podem ser executados em simultâneo.

Por predefinição, cada utilizador é membro da classe de recurso pequeno, **smallrc**. O procedimento `sp_addrolemember` é utilizado para aumentar a classe de recursos e `sp_droprolemember` é utilizado para diminuir a classe de recursos. Por exemplo, este comando deverá aumentar a classe de recurso dos loaduser para **largerc**:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Consultas que não honrar classes de recursos

Existem alguns tipos de consultas não beneficiam de uma alocação de memória superior. O sistema ignora a respetiva alocação de classe de recursos e são sempre executadas em vez disso, estas consultas na classe de recurso pequeno. Se estas consultas são sempre executados na classe de recursos pequeno, podem executar quando existem ranhuras de concorrência sob pressão e estes não irão consumir mais ranhuras que os necessários. Consulte [exceções de classe de recurso](#query-exceptions-to-concurrency-limits) para obter mais informações.

## <a name="details-on-resource-class-assignment"></a>Detalhes sobre a atribuição de classe de recursos


Alguns obter mais detalhes sobre a classe de recursos:

* *Alterar função* é necessária permissão para alterar a classe de recursos de um utilizador.
* Apesar de poder adicionar um utilizador para uma ou mais das classes de recurso superiores, classes de recursos dinâmicos têm precedência sobre as classes de recurso estático. Ou seja, se um utilizador for atribuído a ambos **mediumrc**(dinâmico) e **staticrc80**(estáticas), **mediumrc** é a classe de recursos que está a ser cumprida.
 * Quando um utilizador está atribuído a mais do que uma classe de recursos de um tipo de classe de recurso específico (mais do que uma classe de recursos dinâmicos ou mais do que uma classe de recursos estático), é honrada a classe de recursos mais elevada. Ou seja, se um utilizador é atribuído a mediumrc e largerc, é honrada a classe de recursos superior (largerc). E se um utilizador for atribuído a ambos **staticrc20** e **statirc80**, **staticrc80** é honrado.
* Não é possível alterar a classe de recursos do utilizador administrativo do sistema.

Para obter um exemplo detalhado, consulte [exemplo de classe de recursos de utilizador de alteração](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Atribuição de memória
Existem os profissionais de TI e contras para aumentar a classe de recursos de um utilizador. Aumentar a uma classe de recursos para um utilizador, fornece o acesso de consultas para obter mais memória, o que pode significar que consultas ser executados mais rapidamente.  No entanto, superiores classes de recursos também reduzem o número de consultas em simultâneo que podem ser executados. Este é o compromisso entre alocar grandes quantidades de memória a uma única consulta ou permitir que outras consultas, que também terá das alocações de memória, para executar em simultâneo. Se um utilizador é determinado elevadas alocações de memória para uma consulta, outros utilizadores não terão acesso a essa mesma memória para executar uma consulta.

A seguinte tabela mapeia a memória atribuída a cada distribuição por classe de recursos e de DWU.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Alocações de memória por distribuição para classes de recursos dinâmicos (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

A seguinte tabela mapeia a memória atribuída a cada distribuição DWU e classe de recursos estático. Tenha em atenção que as classes de recurso superiores têm as respetivas memória reduzida para honrar os limites DWU global.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Alocações de memória por distribuição para classes de recurso estático (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

A tabela anterior, pode ver que uma consulta em execução num DW2000 no **xlargerc** classe de recursos deverá ter acesso a 6,400 MB de memória em cada uma das bases de dados distribuídas 60.  No SQL Data Warehouse, existem 60 distribuições. Por conseguinte, para calcular a alocação de memória total para uma consulta de uma classe de recursos específico, os valores acima deverão ser multiplicados pelo 60.

### <a name="memory-allocations-system-wide-gb"></a>Memória alocações globais do sistema (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Esta tabela de alocações de memória de todo sistema, pode ver que uma consulta em execução num DW2000 na classe de recurso xlargerc é atribuída um total de 375 GB de memória (6,400 MB * 60 distribuições / 1.024 converter em GB) através da totalidade do seu SQL Data Warehouse.

O cálculo mesmo se aplica a classes de recurso estático.
 
## <a name="concurrency-slot-consumption"></a>Consumo de ranhura de concorrência  
O SQL Data Warehouse atribui mais memória para as consultas em execução em classes de recurso superiores. A memória é um recurso fixo.  Por conseguinte, a mais memória alocada por consulta, podem executar as consultas em simultâneo menos. A tabela seguinte reiterates todas os conceitos anteriores numa única vista que mostra o número de ranhuras de concorrência disponíveis por DWU e ranhuras consumidas por cada classe de recursos.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Alocação e o consumo de ranhuras de concorrência para classes de recursos dinâmicos  
| DWU | Consultas em simultâneo máximas | Ranhuras de concorrência atribuídas | Ranhuras utilizadas pelo smallrc | Ranhuras utilizadas pelo mediumrc | Ranhuras utilizadas pelo largerc | Ranhuras utilizadas pelo xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Alocação e o consumo de ranhuras de concorrência para classes de recurso estático  
| DWU | Consultas em simultâneo máximas | Ranhuras de concorrência atribuídas |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

Destas tabelas, pode ver essa execução do SQL Data Warehouse como DW1000 atribui um máximo de 32 consultas em simultâneo e um total de 40 ranhuras de concorrência. Se estiver a executar no smallrc todos os utilizadores, 32 consultas em simultâneo seriam permitidas porque cada consulta consuma 1 ranhura de concorrência. Se todos os utilizadores um DW1000 estivesse em execução num mediumrc, cada consulta deverá ser alocada 800 MB por distribuição de uma atribuição de memória total de 47 GB por consulta e simultaneidade seria limitada a 5 utilizadores (40 ranhuras de concorrência 8 ranhuras por utilizador mediumrc /).

## <a name="selecting-proper-resource-class"></a>Selecionar a classe de recursos correto  
É uma boa prática permanentemente atribuir utilizadores a uma classe de recursos em vez de alterar as respetivas classes de recursos. Por exemplo, carrega a tabelas columnstore em cluster criar índices de qualidade superior quando atribuído mais memória. Para se certificar de que carrega têm acesso à memória superior, criar um utilizador especificamente para carregar os dados e atribua permanentemente este utilizador para uma classe de recurso superior.
Existem alguns procedimentos recomendados para aqui. Tal como mencionado acima, o armazém de dados do SQL Server suporta dois tipos de tipos de classe de recursos: classes de recursos dinâmicos e de classes de recurso estático.
### <a name="loading-best-practices"></a>Melhores práticas de carregamento
1.  Se as expectativas são cargas com regular quantidade de dados, uma classe de recurso estático é uma boa opção. Mais tarde, quando como aumentar verticalmente para obter mais de cálculo adequada, o armazém de dados será capaz de executar mais simultâneo consultas out-of-a-box, como o utilizador de carga não consumir mais memória.
2.  Se as expectativas são maiores cargas em ocasiões, uma classe de recursos dinâmicos é uma boa opção. Mais tarde, quando como aumentar verticalmente para obter mais de cálculo adequada, o carga utilizador irá receber mais memória out-of-a-box, por conseguinte, permitindo que a carga efetuar mais rapidamente.

A memória necessária para processar cargas de forma eficiente depende a natureza a tabela carregada e a quantidade de processamento de dados. Por exemplo, carregar dados para tabelas CCI requer alguns memória para permitir que rowgroups CCI alcançar optimality. Para obter mais detalhes, consulte os índices Columnstore - orientações do carregamento de dados.

Como melhor prática, aconselhamos que utilize, pelo menos, 200MB de memória para cargas.

### <a name="querying-best-practices"></a>Consultar as melhores práticas
As consultas têm requisitos diferentes, consoante os respetivos complexidade. Aumente a memória por consulta ou aumentar a simultaneidade é ambas as direções válidas para aumentar o débito global consoante as necessidades de consulta.
1.  Se as expectativas são de consultas complexas, regulares (por exemplo, para gerar relatórios diários e semanais) e não é necessário tirar partido de simultaneidade, uma classe de recursos dinâmicos é uma boa opção. Se o sistema tem mais dados para processar, como aumentar verticalmente o armazém de dados, por conseguinte, automaticamente fornecerá mais memória para o utilizador a executar a consulta.
2.  Se as expectativas são de padrões de simultaneidade de variável ou diurnal (para a instância se a base de dados é consultado através de uma IU da web do amplamente acessível), uma classe de recurso estático é uma boa opção. Mais tarde, quando como aumentar verticalmente do armazém de dados, o utilizador associado a classe de recursos estático automaticamente será capaz de executar consultas mais em simultâneo.

Selecionar concessão de memória adequada consoante a necessidade da sua consulta é não trivial, porque depende de vários fatores, tais como a quantidade de dados a ser consultados, a natureza os esquemas de tabela e vários associação, seleção e predicados de grupo. De um ponto de vista geral, alocar mais memória irá permitir que as consultas agilizar a conclusão, mas seria reduza a simultaneidade geral. Se a simultaneidade não é um problema, não danificar alocação excessiva da memória. Para otimizar o débito, a tentativa de vários tipos de classes de recursos poderá ser necessário.

Pode utilizar o seguinte procedimento armazenado para descobrir a concessão de memória e de concorrência por classe de recursos num determinado SLO e classe de recurso melhor mais próximo para operações CCI que consomem muita memória na tabela CCI não particionadas uma classe de recursos específico:

#### <a name="description"></a>Descrição:  
Eis o objetivo deste procedimento armazenado:  
1. Para ajudar o utilizador descobrir simultaneidade e memória conceder por classe de recursos num SLO indicado. Utilizador tem de fornecer um valor nulo para o esquema e tablename para este conforme mostrado no exemplo abaixo.  
2. Para ajudar o utilizador descobrir a classe de recursos melhor mais próxima para a memória intensed operações CCI (carga, a tabela de cópia, reconstrua o índice, etc.) na tabela CCI não particionada uma classe de recursos específico. O procedimento armazenado utiliza o esquema de tabela para obter a concessão de memória necessária para este.

#### <a name="dependencies--restrictions"></a>As dependências e restrições:
- Este procedimento armazenado não foi concebido para calcular o requisito de memória para a tabela de cci particionada.    
- Este procedimento armazenado não tem o requisito de memória em consideração para a parte SELECIONE CTAS/INSERT-SELECIONAR e parte do princípio de que seja um SELECIONE simple.
- Este procedimento armazenado utiliza uma tabela temporária, pelo que pode ser utilizado na sessão onde este procedimento armazenado foi criado.    
- Este procedimento armazenado depende as ofertas atuais (por exemplo, a configuração de hardware, a configuração DMS) e se qualquer um dos que for alterada, em seguida, este procedimento armazenado não funciona corretamente.  
- Este procedimento armazenado depende de limite de concorrência oferecido existentes e se de que as alterações, em seguida, este procedimento armazenado não funciona corretamente.  
- Este procedimento armazenado depende de ofertas de classe de recursos existente se que alterações e, em seguida, isto armazenados proc wuold não funcionam corretamente.  

>  [!NOTE]  
>  Se não estiver a obter o resultado depois de executar o procedimento armazenado com parâmetros fornecidos, em seguida, pode haver dois casos. <br />1. O parâmetro de armazém de dados contém o valor SLO inválido <br />2. OU não existe nenhuma classe de recurso correspondente para a operação de CCI se foi fornecido o nome da tabela. <br />Por exemplo, no DW100, mais elevado concessão de memória disponível é 400MB e se o esquema da tabela é suficientemente grande para cruzada o requisito de 400MB.
      
#### <a name="usage-example"></a>Exemplo de utilização:
Sintaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU:Ou forneça um parâmetro nulo para extrair o DWU atual da base de dados do armazém de dados ou forneça quaisquer DWU suportado no formato 'DW100'
2. @SCHEMA_NAME:Forneça um nome de esquema da tabela
3. @TABLE_NAME:Forneça um nome de tabela de interesse

Exemplos de executar este procedimento armazenado:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Table1 utilizados nos exemplos acima pode ser criado como abaixo:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Segue-se a definição de procedimento armazenado:
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Importância da consulta
O SQL Data Warehouse implementa classes de recursos através da utilização de grupos de carga de trabalho. Existem um total de oito grupos de carga de trabalho que controlam o comportamento das classes de recursos entre os vários tamanhos DWU. Para qualquer DWU, o SQL Data Warehouse utiliza apenas quatro grupos de carga de oito trabalho. Isto faz sentido porque cada grupo de carga de trabalho é atribuído a uma das quatro classes de recursos: smallrc mediumrc, largerc, ou xlargerc. A importância de compreender os grupos de carga de trabalho é que alguns destes grupos de carga de trabalho estiverem definido como superiores a *importância*. Importância é utilizada para a CPU agendamento. Consultas executadas com importância elevada irão receber três vezes mais ciclos da CPU que as com importância Média. Por conseguinte, mapeamentos de ranhura de concorrência também determinam a prioridade da CPU. Quando uma consulta consome ranhuras 16 ou mais, que seja executada como importância elevada.

A tabela seguinte mostra os mapeamentos de importância para cada grupo de carga de trabalho.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapeamentos de grupo de carga de trabalho para as ranhuras de simultaneidade e importância
| Grupos de carga de trabalho | Mapeamento de ranhura de concorrência | MB / distribuição | Mapeamento de importância |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Médio |
| SloDWGroupC01 |2 |200 |Médio |
| SloDWGroupC02 |4 |400 |Médio |
| SloDWGroupC03 |8 |800 |Médio |
| SloDWGroupC04 |16 |1,600 |Elevado |
| SloDWGroupC05 |32 |3,200 |Elevado |
| SloDWGroupC06 |64 |6,400 |Elevado |
| SloDWGroupC07 |128 |12,800 |Elevado |

Do **alocação e o consumo de ranhuras de concorrência** gráfico, pode ver que um DW500 utiliza 1, 4, 8 ou ranhuras de concorrência 16 para smallrc, mediumrc, largerc e xlargerc, respetivamente. Pode procurar esses valores no gráfico anterior para determinar a importância para cada classe de recursos.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapeamento de DW500 de classes de recursos para importância
| Classe de recursos | Grupo de carga de trabalho | Ranhuras de concorrência utilizadas | MB / distribuição | Importância |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Médio |
| mediumrc |SloDWGroupC02 |4 |400 |Médio |
| largerc |SloDWGroupC03 |8 |800 |Médio |
| xlargerc |SloDWGroupC04 |16 |1,600 |Elevado |
| staticrc10 |SloDWGroupC00 |1 |100 |Médio |
| staticrc20 |SloDWGroupC01 |2 |200 |Médio |
| staticrc30 |SloDWGroupC02 |4 |400 |Médio |
| staticrc40 |SloDWGroupC03 |8 |800 |Médio |
| staticrc50 |SloDWGroupC03 |16 |1,600 |Elevado |
| staticrc60 |SloDWGroupC03 |16 |1,600 |Elevado |
| staticrc70 |SloDWGroupC03 |16 |1,600 |Elevado |
| staticrc80 |SloDWGroupC03 |16 |1,600 |Elevado |

Pode utilizar a seguinte consulta DMV para examinar as diferenças na alocação de recursos de memória em detalhe da perspectiva do Governador de recursos ou para analisar a utilização do Active Directory e histórico dos grupos de carga de trabalho quando a resolução de problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Consultas que respeite os limites de concorrência
A maioria das consultas são regidas pelas classes de recursos. Estas consultas devem caber ambos os simultâneos consulta e simultaneidade ranhura limiares. Um utilizador não é possível escolher excluir uma consulta do modelo de ranhura de concorrência.

Para reiterate, as seguintes declarações honrar classes de recursos:

* INSERT SELECT
* ATUALIZAÇÃO
* ELIMINAR
* SELECIONE (quando consultar tabelas de utilizador)
* ALTER A RECONSTRUÇÃO DO ÍNDICE
* ALTERAR ÍNDICE REORGANIZE
* ALTER TABLE REBUILD
* CRIAR O ÍNDICE
* CRIAR O ÍNDICE COLUMNSTORE EM CLUSTER
* CRIAR TABLE AS SELECT (CTAS)
* Carregamento de dados
* Operações de movimento de dados efetuadas pelo serviço de movimento de dados (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Exceções de consulta para os limites de concorrência
Algumas consultas não honrar a classe de recursos para o qual o utilizador está atribuído. Estas exceções os limites de concorrência que são efetuadas quando os recursos de memória necessários para um determinado comando são baixo, muitas vezes, porque o comando é uma operação de metadados. O objetivo destas exceções está evitar a maior memória alocações para consultas que nunca vai precisar deles. Nestes casos, a classe de recurso pequeno predefinido (smallrc) é sempre utilizada, independentemente da classe de recursos real atribuída ao utilizador. Por exemplo, `CREATE LOGIN` será sempre executado no smallrc. Os recursos necessários para realizar esta operação são muito baixos, pelo que não faz sentido para incluir a consulta no modelo de ranhura de concorrência.  Estas consultas são também não se limitando por limite de concorrência 32 utilizador, um número ilimitado destas consultas pode executar cópias de segurança para o limite da sessão de 1.024 sessões.

As seguintes declarações não respeite as classes de recursos:

* Criar ou remover a tabela
* FALHA DE ALTER TABLE... PARTIÇÃO de intercalação, divisão ou comutador
* ALTERAR A DESATIVAÇÃO DO ÍNDICE
* REMOVER O ÍNDICE
* CRIAR, ATUALIZAR ou DROP STATISTICS
* TRUNCAR A TABELA
* ALTER AUTORIZAÇÃO
* CRIAR O INÍCIO DE SESSÃO
* CRIAR, alterar ou remover utilizador
* CRIAR, alterar ou remover o procedimento
* Criar ou remover vista
* INSERIR VALORES
* SELECIONAR vistas de sistema e DMVs
* EXPLICAR
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a>Alterar um exemplo de classe de recursos de utilizador
1. **Criar o início de sessão:** abrir uma ligação à sua **mestre** da base de dados no SQL server que aloja a base de dados do armazém de dados do SQL Server e execute os seguintes comandos.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > É uma boa ideia criar um utilizador na base de dados mestra para utilizadores do Azure SQL Data Warehouse. Criar um utilizador no mestre permite ao utilizador iniciar sessão utilizando ferramentas como SSMS sem especificar um nome de base de dados.  Também permite-lhe utilizar o Explorador de objeto para ver todas as bases de dados num SQL server.  Para obter mais detalhes sobre como criar e gerir utilizadores, consulte [proteger uma base de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Criar SQL Data Warehouse utilizador:** abrir uma ligação para o **SQL Data Warehouse** da base de dados e execute o seguinte comando.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Conceder permissões:** concede o exemplo seguinte `CONTROL` no **SQL Data Warehouse** base de dados. `CONTROL`na base de dados do nível é o equivalente do db_owner no SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Aumentar a classe de recursos:** utilize a seguinte consulta para adicionar um utilizador a uma função de gestão de carga de trabalho superior.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Diminuir a classe de recursos:** utilize a seguinte consulta para remover um utilizador a partir de uma função de gestão de carga de trabalho.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Não é possível remover um utilizador smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Deteção de consulta em fila e outros DMVs
Pode utilizar o `sys.dm_pdw_exec_requests` DMV para identificar as consultas que estão a aguardar na fila de concorrência. Consulta a aguardar uma ranhura de concorrência terá um Estado de **suspenso**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Funções de gestão de carga de trabalho podem ser visualizadas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

A consulta seguinte mostra a qual cada utilizador tem atribuída para a função.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

O SQL Data Warehouse tem os seguintes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: consultas manter-se fora do framework de ranhura de concorrência. Consultas DMV e sistema funciona como `SELECT @@VERSION` são exemplos de consultas locais.
* **UserConcurrencyResourceType**: consultas manter-se no interior do framework de ranhura de concorrência. Consultas de tabelas do utilizador final representam exemplos que pretende utilizar este tipo de recurso.
* **DmsConcurrencyResourceType**: aguarda resultantes de operações de movimento de dados.
* **BackupConcurrencyResourceType**: este espera indica que uma base de dados está a ser efetuada. O valor máximo para este tipo de recurso é 1. Se tiverem sido solicitadas várias cópias de segurança ao mesmo tempo, os outros ficarão em fila.

O `sys.dm_pdw_waits` DMV pode ser utilizado para ver quais os recursos que está a aguardar um pedido.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

O `sys.dm_pdw_resource_waits` DMV mostra apenas os poucos recursos consumidos por uma consulta fornecida. Tempo de espera de recursos apenas mede o tempo à espera de recursos para ser fornecido, por oposição a tempo de espera de sinal, que é o tempo que demora para os SQL servers subjacentes agendar a consulta para a CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

O `sys.dm_pdw_wait_stats` DMV pode ser utilizado para análise de tendências históricos de poucos.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a gestão de utilizadores de base de dados e segurança, consulte [proteger uma base de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como maiores classes de recurso podem melhorar a qualidade de índice columnstore em cluster, consulte [reconstruir índices para melhorar a qualidade de segmento].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[reconstruir índices para melhorar a qualidade de segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
