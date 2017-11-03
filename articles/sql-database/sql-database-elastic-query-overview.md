---
title: "Descrição geral de consulta elástico de base de dados SQL do Azure | Microsoft Docs"
description: "Consulta elástica permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados."
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: a8bf0e2c-bc74-44d0-9b1e-bcc9a6aa2e33
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: mlandzic
ms.openlocfilehash: 6389702b1be5e52c7191e6e57d17b48289e800b2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Descrição geral de consulta elástico do Azure SQL Database (pré-visualização)
A funcionalidade de consulta elástico (em pré-visualização) permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados na base de dados do Azure SQL. Permite-lhe executar consultas de base de dados em vários locais para aceder a tabelas remotas e para estabelecer a ligação de ferramentas da Microsoft e de terceiros (Excel, PowerBI, Tableau, etc.) para consultar em camadas de dados com várias bases de dados. Utilizar esta funcionalidade, pode aumentar horizontalmente consultas para as camadas de dados de grandes dimensões na base de dados do SQL Server e visualizar os resultados em relatórios do business intelligence (BI).


## <a name="why-use-elastic-queries"></a>Porquê utilizar consultas elásticas?

**Base de Dados SQL do Azure**

Consultar em bases de dados SQL do Azure completamente em T-SQL. Isto permite a consultar só de leitura de bases de dados remotas. Isto oferece uma opção para clientes de SQL Server no local atuais migrar as aplicações utilizando nomes de três e four part ou o servidor ligado à base de dados do SQL Server.

**Disponível no escalão standard**

Consulta elástica é suportada na camada padrão de desempenho para além do escalão de desempenho Premium. Consulte a secção sobre limitações de pré-visualização abaixo no limitações de desempenho para os escalões de desempenho inferiores.

**Push às bases de dados remotas**

Consultas elásticas agora podem emitir parâmetros do SQL Server para as bases de dados remotas para execução.

**Execução do procedimento armazenado**

Executar chamadas de procedimento armazenado remoto ou funções remotas com [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714).

**Flexibilidade**

As tabelas externas com consulta elástica agora podem fazer referência a tabelas remotas com um esquema diferente ou o nome da tabela.

## <a name="elastic-query-scenarios"></a>Cenários de consulta elástico

O objetivo é facilitar a consultas cenários em que várias bases de dados contribuem linhas para um único resultado geral. A consulta ou pode ser composta por utilizador ou aplicação diretamente ou indiretamente através de ferramentas que estão ligadas à base de dados. Isto é especialmente útil durante a criação de relatórios, utilizando ferramentas de integração de BI ou dados comerciais — ou qualquer aplicação que não pode ser alterada. Com uma consulta elástica, pode consultar em várias bases de dados utilizando a experiência de conectividade familiar do SQL Server em ferramentas, como o Excel, PowerBI, Tableau ou Cognos.
Uma consulta elástica permite que o facilitar o acesso a um conjunto completo de bases de dados através de consultas emitidas pelo SQL Server Management Studio ou Visual Studio e facilita a consultar entre bases de dados do Entity Framework ou outros ambientes ORM. Figura 1 mostra um cenário onde existente na nuvem aplicações (que utiliza o [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md)) baseiam-se num dados expandidos camada e uma consulta elástica é utilizada para entre bases de dados de relatórios.

**Figura 1** elástica consulta utilizada na camada de dados de escalamento horizontal

![Consulta elástica utilizada na camada de dados de escalamento horizontal][1]

Cenários de cliente para a consulta elástico são caracterizados pelas seguintes topologias:

* **Criação de partições vertical - consultas de base de dados entre** (topologia 1): os dados estão particionados verticalmente entre um número de bases de dados numa camada de dados. Normalmente, os conjuntos diferentes de tabelas residirem em bases de dados diferentes. Isto significa que o esquema é diferente em bases de dados diferentes. Por exemplo, todas as tabelas de inventário são numa base de dados enquanto todas as tabelas relacionadas com a gestão de contas numa segunda base de dados. Casos de utilização comuns com esta topologia pedir uma para consultar em ou para compilar relatórios em tabelas em várias bases de dados.
* **Divisão em partições horizontal - fragmentação** (topologia 2): dados estão particionados horizontalmente distribuam linhas por um dados expandido terminar camada. Com esta abordagem, o esquema é idêntico em todos os participantes bases de dados. Esta abordagem também é denominada "fragmentação". Pode ser efetuada a fragmentação e gerido a utilizar (1) a base de dados elástica das ferramentas de bibliotecas ou (2) o self-fragmentação. Uma consulta elástica é utilizada para consultar ou compilar relatórios em várias partições horizontais.

> [!NOTE]
> Consulta elástica funciona melhor para cenários de relatórios ocasionais onde a maior parte do processamento pode ser efetuadas na camada de dados. Para cargas de trabalho pesadas relatórios ou cenários com consultas mais complexas do armazém de dados, considere também a utilizar [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Criação de partições vertical - entre bases de dados consultas

Para iniciar a codificação, consulte [introdução à consulta de base de dados em vários locais (criação de partições vertical)](sql-database-elastic-query-getting-started-vertical.md).

Uma consulta elástica pode ser utilizada para tornar os dados localizados numa SQL database disponível para outras bases de dados do SQL Server. Isto permite que as consultas de uma base de dados para fazer referência a tabelas no quaisquer outro remoto base de dados SQL. O primeiro passo consiste em definir uma origem de dados externo para cada base de dados remota. A origem de dados externa está definida na base de dados local a partir do qual pretende obter acesso a tabelas localizados na base de dados remota. Sem alterações são necessárias na base de dados remota. Para verticais partições cenários típicos onde diferentes bases de dados têm esquemas diferentes, consultas elásticas podem ser utilizadas para implementar os casos de utilização comuns, como o acesso aos dados de referência e entre bases de dados a consultar.

> [!IMPORTANT]
> Precisa de ter permissão ALTER qualquer origem de dados externa. Esta permissão está incluído com a permissão ALTER DATABASE. São necessárias permissões de ALTER qualquer origem de dados externa para fazer referência à origem de dados subjacente.
>

**Referência a dados**: A topologia é utilizada para gestão de dados de referência. A figura abaixo, duas tabelas (T1 e T2) com dados de referência são mantidas na base de dados dedicado. Utilizando uma consulta elástica, pode agora aceder a tabelas T1 e T2 remotamente a partir de outras bases de dados, conforme mostrado na figura. Utilizar topologia 1 se as tabelas de referência são consultas pequenas ou remotas, na tabela de referência tem predicados seletivos.

**Figura 2** criação de partições Vertical - o utilizando dados de referência de consulta para elástico

![Criação de partições vertical - o utilizando dados de referência de consulta para elástico][3]

**Consulta de base de dados entre**: elástico consulta casos de utilização de ativar que necessitam de consulta em várias bases de dados do SQL Server. A figura 3 mostra quatro bases de dados diferentes: CRM, inventário, HR e produtos. Consultas de efetuar uma das bases de dados também necessitam de aceder a uma ou todas as outras bases de dados. Utilizando uma consulta elástica, pode configurar a base de dados para este cenário através da execução de algumas instruções DDL simples em cada um dos quatro bases de dados. Após esta configuração única, o acesso a uma tabela remota é tão simple como referência a uma tabela local das consultas T-SQL ou a partir de ferramentas de BI. Esta abordagem é recomendada se as consultas remotas devolver resultados grande.

**Figura 3** criação de partições Vertical - o utilizando elástico para consulta através de várias bases de dados

![Criação de partições vertical - o utilizando elástico para consulta através de várias bases de dados][4]

Consultas de base de dados elástica para cenários de criação de partições verticais que necessitam de acesso a uma tabela localizado nas bases de dados de SQL Server remotos com o mesmo esquema de configurar os seguintes passos:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [ORIGEM de dados externa CREATE/DROP](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource do tipo **RDBMS**
* [TABELA externa CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Depois de executar as instruções DDL, pode aceder à tabela remota "mytable" como se fosse uma tabela local. Base de dados SQL do Azure automaticamente abre uma ligação à base de dados remota, processa o pedido na base de dados remota e devolve os resultados.

## <a name="horizontal-partitioning---sharding"></a>Criação de partições horizontais - fragmentação
Necessita de camada de dados elástica consulta a utilizar para efetuar tarefas de criação de relatórios através de um em partição horizontal, ou seja, horizontalmente particionadas, um [mapa de partições horizontais de base de dados elástica](sql-database-elastic-scale-shard-map-management.md) para representar as bases de dados de camada de dados. Normalmente, apenas um mapa de partições horizontais único é utilizado neste cenário e serve de base de dados dedicado com capacidades de consulta elástico (nó principal) como o ponto de entrada para consultas de relatórios. Apenas esta base de dados dedicado precisa de acesso para o mapa de partições horizontais. A figura 4 ilustra esta topologia e a respetiva configuração com o mapa de base de dados e o ID de partição horizontal elástico de consulta. As bases de dados na camada de dados podem ser qualquer versão de SQL Database do Azure ou a edição. Para obter mais informações sobre a biblioteca de clientes de base de dados elástica e a criação de partições horizontais maps, consulte [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** criação de partições horizontais - o utilizando consulta elástica para relatórios através de camadas de dados fragmentados

![Criação de partições horizontais - o utilizando consulta elástica para relatórios através de camadas de dados fragmentados][5]

> [!NOTE]
> Base de dados elástica para a consulta (nó principal) pode ser a base de dados separada, ou pode ser a mesma base de dados que aloja o mapa de partições horizontais. Qualquer configuração que escolher, certifique-se de que da camada de serviço e o desempenho dessa base de dados for suficientemente elevada para lidar com a quantidade de pedidos de início de sessão/consulta esperada.

Os seguintes passos configurar consultas de base de dados elástica para horizontais partições cenários que necessitam de acesso a um conjunto de tabela que se encontram em (normalmente) várias remotos bases de dados SQL:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* Criar um [mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md) que representa a camada de dados utilizando a biblioteca de clientes de base de dados elásticas.   
* [ORIGEM de dados externa CREATE/DROP](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource do tipo **SHARD_MAP_MANAGER**
* [TABELA externa CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Depois de ter de efetuar estes passos, pode aceder à tabela particionada horizontalmente "mytable" como se fosse uma tabela local. Base de dados SQL do Azure automaticamente abre-se várias ligações paralelas às bases de dados remotas, onde as tabelas fisicamente são armazenadas, processa os pedidos nas bases de dados remotas e devolve os resultados.
Obter mais informações sobre os passos necessários para o cenário de criação de partições horizontal pode ser encontrado na [elástica consulta para criação de partições horizontais](sql-database-elastic-query-horizontal-partitioning.md).

Para iniciar a codificação, consulte [como começar a consultar elástico (fragmentação) de partições horizontais](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Consulta de T-SQL
Uma vez que definiu as origens de dados externos e as tabelas externas, pode utilizar regulares cadeias de ligação do SQL Server para ligar às bases de dados em que definiu as tabelas externas. Em seguida, pode executar instruções T-SQL em seu tabelas externas nessa ligação com as limitações descritas abaixo. Pode encontrar mais informações e exemplos de consultas de T-SQL nos tópicos documentação para [criação de partições horizontais](sql-database-elastic-query-horizontal-partitioning.md) e [criação de partições vertical](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas
Pode utilizar regulares cadeias de ligação do SQL Server para ligar as suas aplicações e ferramentas de integração de BI ou dados às bases de dados que tenham as tabelas externas. Certifique-se de que o SQL Server é suportada como uma origem de dados para a ferramenta. Assim que estiver ligado, consulte a base de dados elásticas consulta e as tabelas externas essa base de dados tal como seria feito com quaisquer outro do SQL Server da base de dados que se ligam a com a ferramenta.

> [!IMPORTANT]
> A autenticação utilizando o Azure Active Directory com consultas elásticas não é atualmente suportada.
> 
> 

## <a name="cost"></a>Custo
Consulta elástica está incluída no custo de bases de dados do SQL Database do Azure. Tenha em atenção que são suportadas topologias de onde as bases de dados remotos estão num centro de dados diferente que o ponto final de consulta elástico, mas a saída de dados de bases de dados remotas são-lhe cobrados regular [taxas Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitações de pré-visualização
* Executar a sua primeira consulta elástica pode demorar alguns minutos na camada padrão de desempenho. Desta vez, é necessária para carregar a funcionalidade de consulta elástico; carregar o desempenho melhora com camadas de desempenho superiores.
* Ainda, o script de origens de dados externas ou tabelas externas do SSMS ou o SSDT não é suportado.
* Importar/exportar para o SQL DB ainda não suporte origens de dados externas e tabelas externas. Se precisar de utilizar a importação/exportação, remova estes objetos antes de exportar e, em seguida, recrie-las após a importação.
* Elástico query atualmente só suporta o acesso só de leitura para as tabelas externas. No entanto, pode utilizar a funcionalidade completa de T-SQL na base de dados em que a tabela externa está definida. Isto pode ser útil para, por exemplo, manter resultados temporários a utilizar, por exemplo, SELECIONE < column_list > em < local_table >, ou para definir os procedimentos armazenados na base de dados elásticas consulta que se referem a tabelas externas.
* Exceto nvarchar (Max), os tipos LOB não são suportados em definições de tabela externa. Como solução, pode criar uma vista na base de dados remota que casts o tipo LOB na nvarchar (Max), definir a sua tabela externa através da vista em vez da tabela base e, em seguida, convertê-lo novamente para o tipo LOB original nas suas consultas.
* Estatísticas de coluna em tabelas externas não são atualmente suportadas. Estatísticas de tabelas são suportadas, mas tem de ser criadas manualmente.

## <a name="feedback"></a>Comentários
. Partilhe comentários sobre a sua experiência com consultas elásticas connosco no Livefyre abaixo, os fóruns da MSDN, ou Stackoverflow. Estamos interessados em todos os tipos de comentários sobre o serviço (defeitos, contornos aproximados, intervalos de funcionalidade).

## <a name="next-steps"></a>Passos seguintes

* Para um tutorial de criação de partições vertical, consulte [introdução à consulta de base de dados em vários locais (criação de partições vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e exemplos de dados verticalmente particionadas, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para um tutorial horizontal criação de partições (fragmentação), consulte [como começar a consultar elástico (fragmentação) de partições horizontais](sql-database-elastic-query-getting-started.md).
* Para consultas de sintaxe e exemplos de dados particionadas horizontalmente, consulte [consultar horizontalmente particionada dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que servem como shards num esquema de partições horizontal.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
