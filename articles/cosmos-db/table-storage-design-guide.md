---
title: Guia de estrutura de tabela de armazenamento do Azure | Microsoft Docs
description: "Design dimensionável e tabelas de Performant no Table Storage do Azure"
services: cosmos-db
documentationcenter: na
author: mimig1
manager: tadb
editor: tysonn
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: eaa9d2208406afece5c77859546e888c1e49e902
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Guia de Design da tabela de armazenamento do Azure: Estruturar dimensionável e tabelas Performant
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

A estrutura dimensionável e tabelas performant tem de considerar um número de fatores, tais como o desempenho, escalabilidade e o custo. Se anteriormente tiver concebido esquemas para bases de dados relacionais, estas considerações será parecer-lhe, mas enquanto existirem alguns semelhanças entre o modelo de armazenamento do serviço de Azure Table e modelos relacionais, também existem várias diferenças importantes. Estas diferenças normalmente levar a muito diferentes estruturas que poderá ser counter-intuitive ou errado para alguém familiarizado com bases de dados relacionais, mas o que sentido boa se está a conceber um arquivo de chave/valor NoSQL, tais como o serviço de Azure Table. Muitos dos seus diferenças de estrutura irão refletir o facto de que o serviço tabela foi concebido para suportar aplicações de escala da nuvem que podem conter billions das entidades (linhas na terminologia de base de dados relacional) de dados ou para conjuntos de dados tem de suportar volumes muito elevado de transação: por conseguinte, tem de pensar em forma diferente como armazena os dados e compreender como funciona o serviço de tabela. Um arquivo de dados NoSQL bem estruturado pode ativar a sua solução de dimensionar muito mais (e a um custo mais baixo) que uma solução que utiliza uma base de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço de tabelas do Azure
Esta secção realça algumas das principais funcionalidades do serviço tabela que são especialmente relevantes para estruturar para desempenho e escalabilidade. Se estiver familiarizado com o Storage do Azure e o serviço de tabela, leia primeiro [introdução ao Storage do Microsoft Azure](../storage/common/storage-introduction.md) e [introdução ao armazenamento de tabelas do Azure através do .NET](table-storage-how-to-use-dotnet.md) antes de ler o resto deste artigo. Embora o foco deste guia é o serviço tabela, incluirá algumas debate dos serviços do Azure fila e Blob, e como pode utilizá-los, juntamente com o serviço de tabela numa solução.  

O que é o serviço tabela? Como seria de esperar do nome, o serviço tabela utiliza um formato de tabela para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades dessa entidade. Cada entidade tem um par de chaves para identificar exclusivamente e uma coluna de carimbo que o serviço tabela utiliza para controlar quando a entidade foi efetuada a última atualizado (isto ocorre automaticamente e manualmente não é possível substituir o timestamp com um valor arbitrário). O serviço de tabela utiliza este timestamp last-modified (LMT) para gerir a simultaneidade otimista.  

> [!NOTE]
> As operações de REST API do serviço tabela também devolvem uma **ETag** valor que deriva do timestamp last-modified (LMT). Este documento utilizaremos os termos de licenciamento ETag e LMT-no alternadamente porque consultarem os mesmos dados subjacentes.  
> 
> 

O exemplo seguinte mostra um design de tabela simples para armazenar as entidades dos empregados e departamento. Muitos dos exemplos mostrados posteriormente neste guia são baseados nesta estrutura simple.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Jun</td>
<td>CaO</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Departamento</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Vendas</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Manuel</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Até ao momento, isto é muito semelhante a uma tabela da base de dados relacional com as principais diferenças que está a ser colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo utilizador, tais como **FirstName** ou **idade** tem um tipo de dados, tais como número inteiro ou uma cadeia, apenas como uma coluna na base de dados relacional. Embora ao contrário de uma base de dados relacional, a natureza sem esquema do serviço tabela significa que uma propriedade não precisa de ter os mesmo tipo em cada entidade de dados. Para armazenar os tipos de dados complexas numa única propriedade, tem de utilizar um formato serializado como JSON ou XML. Para obter mais informações sobre os tipos de dados de serviço suportado, tais como tabela, intervalos de data suportados, regras de nomenclatura e restrições de tamanho, consulte [compreender o modelo de dados do serviço tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Como verá, à sua escolha de **PartitionKey** e **RowKey** são fundamentais para estruturar tabela bom. Cada entidade armazenada numa tabela tem de ter uma combinação única de **PartitionKey** e **RowKey**. Tal como acontece com as chaves de uma tabela de base de dados relacional, o **PartitionKey** e **RowKey** valores são indexados para criar um índice em cluster que permite a rápida look-ups; no entanto, o serviço de tabela não criar índices secundários para que estas são as propriedades indexadas apenas dois (alguns dos padrões descritos mais à frente mostram como pode contornar esta limitação aparente).  

Uma tabela é constituída por uma ou mais partições e como verá, muitas das decisões de conceção que efetuar serão à volta de escolher um adequado **PartitionKey** e **RowKey** para otimizar a sua solução. Uma solução pode consistir apenas uma única tabela que contém todas as entidades organizadas em partições, mas normalmente uma solução irá ter várias tabelas. As tabelas ajudá-lo a logicamente organizar as entidades, ajudar a gerir o acesso aos dados por listas de controlo de acesso e pode colocar uma tabela inteira utilizando uma operação de armazenamento única.  

### <a name="table-partitions"></a>Partições da tabela
O nome de conta, o nome da tabela e **PartitionKey** em conjunto identificar a partição no âmbito do serviço de armazenamento onde o serviço tabela armazena a entidade. Bem como a ser parte do esquema de endereçamento para entidades, partições definem um âmbito para transações (consulte [entidade grupo transações](#entity-group-transactions) abaixo), e a base da forma como dimensiona serviço tabela. Para obter mais informações sobre as partições Consulte [metas de desempenho e escalabilidade do Storage do Azure](../storage/common/storage-scalability-targets.md).  

No serviço tabela, um nó individual dos serviços de um ou mais conclua partições e as escalas de serviço, dinamicamente o balanceamento de carga partições entre nós. Se um nó estiver sob carga, o serviço de tabela pode *dividir* o intervalo de partições servidos esse nó em nós diferentes; quando subsides de tráfego, o serviço pode *intercalação* fazer uma cópia de intervalos de partição de nós silenciosos para um único nó.  

Para obter mais informações sobre os detalhes internos do serviço tabela e, em especial, como o serviço gere partições, consulte o documento [armazenamento do Microsoft Azure: A nuvem armazenamento serviço de elevada disponibilidade com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transações de grupo de entidade
No serviço tabela, transações de grupo de entidade (EGTs) são o mecanismo apenas incorporado para executar atualizações atómicas entre várias entidades. EGTs são também denominados *lote de transações* em alguma documentação. EGTs só pode operar em entidades armazenadas na mesma partição (partilhar a mesma chave de partição numa tabela fornecida), pelo que, sempre que necessitar de comportamento transacional atómico entre várias entidades, que certifique-se de que as entidades estão a ser a mesma partição. Isto é frequentemente um motivo para manter os vários tipos de entidade na mesma tabela (e partição) e não a utilizar várias tabelas para tipos de entidade diferente. Um único EGT pode operar no máximo de 100 entidades.  Se submeter vários EGTs simultâneas para o processamento é importante certificar-se de que esses EGTs não operam entidades que são comuns entre EGTs como processar caso contrário, pode sofrer um atraso.

EGTs também introduzir um compromisso potencial para avaliar na sua estrutura: utilizar mais partições irá aumentar a escalabilidade da sua aplicação porque o Azure tem mais de oportunidades de balanceamento de carga pedidos entre nós, mas isto pode limitar a capacidade da sua aplicação para efetuar transações atómicas e manter a consistência forte para os seus dados. Além disso, existem metas de escalabilidade específico no nível de uma partição que pode limitar o débito de transações que pode esperar para um único nó: Para mais informações sobre os destinos de escalabilidade para contas do storage do Azure e o serviço tabela, consulte [metas de desempenho e escalabilidade do Storage do Azure](../storage/common/storage-scalability-targets.md). As secções deste guia abordam vários design estratégias que o ajudam a gerir compromissos, tal como esta e discutem melhor escolher a sua chave de partição com base nos requisitos específicos da sua aplicação de cliente.  

### <a name="capacity-considerations"></a>Considerações sobre a capacidade
A tabela seguinte inclui alguns dos valores da chave a ter em consideração quando está a conceber uma solução de serviço tabela:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas de uma conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições numa tabela |Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades numa partição |Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho de uma entidade individuais |Com um máximo de 255 propriedades até 1 MB (incluindo o **PartitionKey**, **RowKey**, e **Timestamp**) |
| Tamanho do **PartitionKey** |A cadeia até 1 KB de tamanho |
| Tamanho do **RowKey** |A cadeia até 1 KB de tamanho |
| Tamanho de uma transação de grupo de entidade |Uma transação pode incluir um máximo de 100 entidades e o payload tem de ser inferior a 4 MB de tamanho. Uma vez, uma EGT só pode atualizar uma entidade. |

Para obter mais informações, consulte [compreender o modelo de dados do serviço tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerações sobre o custo
O Table storage é relativamente barato, mas deve incluir estimativas de custo de utilização da capacidade e a quantidade de transações como parte da sua avaliação de qualquer solução que utiliza o serviço de tabela. No entanto, em vários cenários para armazenar dados denormalized ou duplicados para melhorar o desempenho ou escalabilidade da sua solução é uma abordagem seja válida. Para obter mais informações sobre preços, consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Diretrizes para estruturar tabela
Estas listas resumem alguns das diretrizes de chaves que deve ter em consideração quando conceber a sua tabelas e este guia irá resolvê-los todos em mais detalhe posteriormente. Estas diretrizes são muito diferentes das diretrizes que normalmente seriam siga para o design da base de dados relacional.  

Conceber a sua solução de serviço tabela para ser *ler* eficiente:

* ***Estrutura de consulta em aplicações pesado de leitura.*** Ao conceber a sua tabelas, tenha em consideração as consultas (especialmente a latência aqueles confidenciais) que será executado antes de pensar sobre como atualizar as entidades. Normalmente, isto resulta numa solução eficiente e performant.  
* ***Especifique PartitionKey e RowKey nas suas consultas.*** *Ponto de consultas* como estas são as consultas de serviço tabela mais eficientes.  
* ***Considere armazenar cópias duplicadas de entidades.*** O Table storage é cheap, por isso, considere armazenar a mesma entidade várias vezes (com chaves diferentes) para ativar as consultas mais eficientes.  
* ***Considere denormalizing os seus dados.*** O Table storage é cheap por isso considere denormalizing os seus dados. Por exemplo, armazene entidades de resumidas para que as consultas para agregadas dados só precisam de aceder a uma única entidade.  
* ***Utilize os valores da chave compostos.*** As chaves só tem são **PartitionKey** e **RowKey**. Por exemplo, utilize os valores da chave compostos Ativar caminhos de acesso codificada alternativo para entidades.  
* ***Utilize projeção da consulta.*** Pode reduzir a quantidade de dados que são transferidos através da rede através de consultas que selecionar apenas os campos que precisa.  

Conceber a sua solução de serviço tabela para ser *escrever* eficiente:  

* ***Não crie partições frequente.*** Escolha as chaves que lhe permite distribuídos os pedidos por várias partições em qualquer ponto de tempo.  
* ***Evite picos de tráfego.*** Smooth o tráfego durante um período de tempo razoável e evitar picos de tráfego.
* ***Necessariamente não crie uma tabela separada para cada tipo de entidade.*** Quando necessitar de transações atómicas em tipos de entidade, pode armazenar estes vários tipos de entidade na mesma partição na mesma tabela.
* ***Considere o débito máximo que deve realizar.*** Tem de ter em consideração os destinos de escalabilidade para o serviço de tabela e certifique-se de que a estrutura não fará com que excedesse-los.  

Como ler este guia, irá ver exemplos que colocar todos estes princípios em prática.  

## <a name="design-for-querying"></a>Conceção para consultas
Soluções de serviço tabela poderão ser leitura intensiva, intensiva de escrita ou uma combinação dos dois. Esta secção está centrado nas questões a tenha em consideração quando está a conceber o seu serviço de tabela para suportar operações de leitura de forma eficiente. Normalmente, também é eficiente para operações de escrita de uma estrutura que suporta operações de leitura de forma eficiente. No entanto, existem considerações adicionais para tenha em consideração quando conceber para suportar operações de escrita, abordadas na secção seguinte, [Design de modificação de dados](#design-for-data-modification).

É um ponto de partida para conceber a sua solução de serviço tabela para que possa ler os dados de forma eficiente e peça "consultas de que a minha aplicação terá de executar para obter os dados que necessita do serviço tabela?"  

> [!NOTE]
> Com o serviço tabela, é importante obter o design correto adiantado porque é difícil e dispendiosas alterá-la mais tarde. Por exemplo, numa base de dados relacional é frequentemente possível para resolver problemas de desempenho simplesmente através da adição de índices para uma base de dados: não é uma opção com o serviço de tabela.  
> 
> 

Esta secção centra-se nos assuntos chaves que devem ser abordadas ao conceber as tabelas para consultas. Tópicos abrangidos nesta secção incluem:

* [Como à sua escolha de PartitionKey e RowKey tem impacto no desempenho das consultas](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Escolher um PartitionKey adequado](#choosing-an-appropriate-partitionkey)
* [Otimizar as consultas para o serviço tabela](#optimizing-queries-for-the-table-service)
* [Ordenação de dados no serviço tabela](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como à sua escolha de PartitionKey e RowKey tem impacto no desempenho das consultas
Os exemplos seguintes partem do princípio de serviço tabela está a armazenar as entidades de empregado com a seguinte estrutura (omita a maioria dos exemplos de **Timestamp** propriedade para efeitos de clareza):  

| *Nome da coluna* | *Tipo de dados* |
| --- | --- |
| **PartitionKey** (nome do departamento de) |Cadeia |
| **RowKey** (Id de empregado) |Cadeia |
| **Nome próprio** |Cadeia |
| **Apelido** |Cadeia |
| **Idade** |Número inteiro |
| **Endereço de correio eletrónico** |Cadeia |

A secção anterior [descrição geral do serviço de Azure Table](#overview) descreve algumas das principais funcionalidades do serviço tabela do Azure que tenham uma influência direta na conceção para a consulta. Estes fazer com as seguintes diretrizes gerais para conceber consultas do serviço de tabela. Tenha em atenção que a sintaxe de filtro utilizada nos exemplos abaixo é a partir da API de REST do serviço tabela para obter mais informações, consulte [entidades de consulta](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

* A ***ponto consulta*** é a pesquisa mais eficiente para utilizar e é recomendada a utilizar para pesquisas de elevado volume ou pesquisas que necessitam de latência mais baixa. Este tipo uma consulta pode utilizar os índices para localizar uma entidade individual de forma muito eficiente, especificando ambos os **PartitionKey** e **RowKey** valores. Por exemplo: $filter = (PartitionKey eq 'Vendas') e (RowKey eq '2')  
* Segundo melhor é um ***intervalo de consulta*** que utiliza o **PartitionKey** e os filtros num intervalo de **RowKey** valores para devolver mais de uma entidade. O **PartitionKey** valor identifica uma partição específica e o **RowKey** valores identificam um subconjunto de entidades dessa partição. Por exemplo: $filter = PartitionKey eq 'Dos vendas e do RowKey ge' e RowKey lt possível '  
* É melhor terceiro um ***partição analisar*** que utiliza o **PartitionKey** e filtros e que a outra propriedade de chave não podem devolver mais de uma entidade. O **PartitionKey** valor identifica uma partição específica, e a propriedade de valores select para um subconjunto de entidades dessa partição. Por exemplo: $filter = PartitionKey eq 'Vendas' e LastName eq 'Santos'  
* A ***tabela analisar*** não inclui o **PartitionKey** e é muito ineficaz porque este procura todas as partições que compõem a sua tabela por sua vez para qualquer entidades correspondentes. A operação executará uma análise da tabela, independentemente se pretende ou não utiliza o filtro de **RowKey**. Por exemplo: $filter = LastName eq 'Jones'  
* As consultas que devolvem várias entidades devolvem-las ordenados **PartitionKey** e **RowKey** ordem. Para evitar resorting as entidades no cliente, escolha um **RowKey** que define a sequência de ordenação mais comuns.  

Tenha em atenção que utilizar um "**ou**" para especificar um filtro com base no **RowKey** valores resultados de uma análise de partição e não é tratado como uma consulta de intervalo. Por conseguinte, deve evitar consultas que utilizam os filtros, tais como: $filter = PartitionKey eq 'Vendas' e (RowKey eq '121' ou RowKey eq '322')  

Para obter exemplos de código do lado do cliente que utilizam a biblioteca de clientes de armazenamento para executar consultas eficiente, consulte:  

* [Executar uma consulta de ponto de utilizar a biblioteca de clientes de armazenamento](#executing-a-point-query-using-the-storage-client-library)
* [A obter várias entidades com LINQ](#retrieving-multiple-entities-using-linq)
* [Projecção do lado do servidor](#server-side-projection)  

Para obter exemplos de código do lado do cliente que pode processar vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidade heterogénea](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Escolher um PartitionKey adequado
À sua escolha de **PartitionKey** deve balancear a necessidade de permite a utilização de EGTs (para garantir consistência) com o requisito de distribuir as entidades por várias partições (para garantir que uma solução dimensionável).  

Em uma Alpine, pode armazenar todas as entidades numa partição única, mas isto pode limitar a escalabilidade da sua solução e impediriam que o serviço de tabela a ser capaz de pedidos de balanceamento de carga. Em outra Alpine, pode armazenar uma entidade por partição, que seria altamente dimensionável e que permite que o serviço tabela pedidos de balanceamento de carga, mas que iria impedi-lo da utilização de transações do grupo de entidade.  

Um ideal **PartitionKey** um que permite-lhe utilizar consultas eficiente e que tem partições suficientes para garantir a sua solução escalável. Normalmente, irá encontrar que as entidades terão uma propriedade adequada que distribui as entidades partições suficientes.

> [!NOTE]
> Por exemplo, um sistema que armazena informações sobre os utilizadores ou os funcionários, UserID pode ser uma boa PartitionKey. Pode ter várias entidades que utilizam um ID de utilizador especificado como a chave de partição. Cada entidade que armazena dados sobre um utilizador se encontra agrupada para uma única partição e, por isso, estas entidades estão acessíveis através de transações do grupo de entidade, enquanto altamente dimensionável.
> 
> 

Existem considerações adicionais na sua escolha de **PartitionKey** relacionadas com a forma como irá inserir, atualizar e eliminar entidades: consulte a secção [Design de modificação de dados](#design-for-data-modification) abaixo.  

### <a name="optimizing-queries-for-the-table-service"></a>Otimizar as consultas para o serviço tabela
O serviço tabela indexa automaticamente os seus entidades utilizando o **PartitionKey** e **RowKey** valores existentes num índice em cluster único, por conseguinte, o motivo que apontam consultas são mais eficiente para utilizar. No entanto, não existem nenhum índices diferente que no índice em cluster no **PartitionKey** e **RowKey**.

Muitas estruturas têm de cumprir os requisitos para ativar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de empregado com base no correio eletrónico, id de empregado ou apelido. Os seguintes padrões na secção [padrões de conceção de tabela](#table-design-patterns) endereço estes tipos de requisito e descrevem formas de resolver o facto de que o serviço tabela fornecem índices secundários:  

* [Padrão de índice secundário intra partição](#intra-partition-secondary-index-pattern) -armazenar várias cópias de cada entidade utilizando diferentes **RowKey** valores (na mesma partição) para ativar rápido e eficiente pesquisas e as ordens de ordenação alternada utilizando diferentes **RowKey** valores.  
* [Padrão de índice secundário partição entre](#inter-partition-secondary-index-pattern) - armazenar várias cópias de cada entidade a utilizar valores diferentes de RowKey em partições separadas ou em tabelas separadas para ativar rápido e eficiente pesquisas e ordenação alternada ordena utilizando diferentes **RowKey** valores.  
* [Padrão de entidades do índice](#index-entities-pattern) -manter entidades de índice para ativar pesquisas eficiente que devolvem apresenta uma lista de entidades.  

### <a name="sorting-data-in-the-table-service"></a>Ordenação de dados no serviço tabela
O serviço tabela devolve entidades ordenadas por ordem, com base no ascendente **PartitionKey** e, em seguida, **RowKey**. Estas chaves são valores de cadeia e para garantir que os valores numéricos ordenar corretamente, deverá convertê-los para um comprimento fixo e preenche-as com zeros. Por exemplo, se o valor de id de empregado utilizar como o **RowKey** é um valor de número inteiro, deve converter id de empregado **123** para **00000123**.  

Muitas aplicações têm requisitos para utilizar dados ordenados as ordens de diferentes: por exemplo, ordenação dos empregados pelo nome ou ao associar data. Os seguintes padrões na secção [padrões de conceção de tabela](#table-design-patterns) como alternativa as ordens de ordenação para as entidades de endereços:  

* [Padrão de índice secundário intra partição](#intra-partition-secondary-index-pattern) - armazenar várias cópias de cada entidade utilizar valores diferentes de RowKey (na mesma partição) para ativar rápido e eficiente pesquisas e ordenação alternada ordena utilizando valores RowKey diferentes.  
* [Padrão de índice secundário partição entre](#inter-partition-secondary-index-pattern) - armazenar várias cópias de cada entidade utilizar valores diferentes de RowKey em separado partições em tabelas separadas para ativar rápido e eficiente pesquisas e ordenação alternada ordena utilizando valores RowKey diferentes.
* [Padrão de seguimento de registo](#log-tail-pattern) -obter o  *n*  entidades recentemente adicionadas a uma partição utilizando um **RowKey** valor ordena na data inversa e ordem de tempo.  

## <a name="design-for-data-modification"></a>Estrutura de modificação de dados
Esta secção centra-se em considerações de design para otimizar as introduções, atualizações e elimina. Em alguns casos, terá de avaliar o compromisso entre as estruturas que otimizar para consulta contra estruturas otimizar a modificação de dados tal como o que fazer em estruturas de bases de dados relacionais (embora as técnicas para gerir os compromissos de design diferentes numa base de dados relacional). A secção [padrões de conceção de tabela](#table-design-patterns) descreve alguns padrões de conceção de detalhado para o serviço de tabela e realça algumas destas compromissos. Na prática, irá encontrar que muitas estruturas otimizadas para consultar entidades também funcionam bem para modificar as entidades.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho de inserir, atualizar e eliminar operações
Para atualizar ou eliminar uma entidade, deve conseguir identificá-lo utilizando o **PartitionKey** e **RowKey** valores. No que esta respeita, à sua escolha de **PartitionKey** e **RowKey** para modificar as entidades deve seguir critérios semelhantes à sua escolha para suportar o ponto de consultas porque pretende identificar entidades mais eficientemente possível. Não pretende utilizar uma análise ineficaz de partição ou tabela para localizar uma entidade para detetar o **PartitionKey** e **RowKey** valores tem de atualizar ou eliminá-lo.  

Os seguintes padrões na secção [padrões de conceção de tabela](#table-design-patterns) otimizar o desempenho ou a inserção, atualização de endereço e operações de eliminação:  

* [Um volume elevado eliminar padrão](#high-volume-delete-pattern) -ativar a eliminação de um grande volume de entidades armazenando todas as entidades para eliminação em simultâneo na sua própria tabela separada; eliminar as entidades por eliminar a tabela.  
* [Padrão de séries de dados](#data-series-pattern) -série de dados completa de loja uma entidade única para minimizar o número de pedidos que efetuar.  
* [Padrão de entidades Wide](#wide-entities-pattern) -utilizar várias entidades físicas para armazenar as entidades lógicas com mais do que 252 propriedades.  
* [Padrão de entidades grandes](#large-entities-pattern) -armazenamento de BLOBs de utilização para armazenar os valores de propriedade grandes.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Garantir a consistência no seu entidades armazenadas
O fator chave que influencia à sua escolha de chaves para otimizar as modificações de dados é como garantir consistência utilizando transações atomic. Só pode utilizar um EGT a funcionar entidades armazenadas na mesma partição.  

Os seguintes padrões na secção [padrões de conceção de tabela](#table-design-patterns) endereço gerir consistência:  

* [Padrão de índice secundário intra partição](#intra-partition-secondary-index-pattern) -armazenar várias cópias de cada entidade utilizando diferentes **RowKey** valores (na mesma partição) para ativar rápido e eficiente pesquisas e as ordens de ordenação alternada utilizando diferentes **RowKey** valores.  
* [Padrão de índice secundário partição entre](#inter-partition-secondary-index-pattern) - armazenar várias cópias de cada entidade a utilizar valores diferentes de RowKey em partições separadas ou em tabelas separadas para ativar rápido e eficiente pesquisas e ordenação alternada ordena utilizando diferentes **RowKey** valores.  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) -ativar o comportamento eventualmente consistente em limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.
* [Padrão de entidades do índice](#index-entities-pattern) -manter entidades de índice para ativar pesquisas eficiente que devolvem apresenta uma lista de entidades.  
* [Padrão de denormalization](#denormalization-pattern) -combinar dados relacionados com o em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  
* [Padrão de séries de dados](#data-series-pattern) -série de dados completa de loja uma entidade única para minimizar o número de pedidos que efetuar.  

Para obter informações sobre transações do grupo de entidade, consulte a secção [entidade grupo transações](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir a estrutura para que as modificações eficiente facilita eficiente consultas
Em muitos casos, uma estrutura eficaz resulta consultas em modificações eficiente, mas deve sempre avaliar se for este o caso para o seu cenário específico. Alguns dos padrões na secção [padrões de conceção de tabela](#table-design-patterns) explicitamente avaliar compromissos entre consultas e modificar as entidades e deve sempre ter em consideração o número de cada tipo de operação.  

Os seguintes padrões na secção [padrões de conceção de tabela](#table-design-patterns) compromissos entre conceber consultas eficiente e estruturar para modificação de dados eficiente de endereços:  

* [Padrão de chave composta](#compound-key-pattern) -utilização composta **RowKey** valores para permitir que um cliente para procurar dados relacionados com um ponto único de consulta.  
* [Padrão de seguimento de registo](#log-tail-pattern) -obter o  *n*  entidades recentemente adicionadas a uma partição utilizando um **RowKey** valor ordena na data inversa e ordem de tempo.  

## <a name="encrypting-table-data"></a>Encriptar dados da tabela
A biblioteca de clientes do Storage de Azure .NET suporta a encriptação das propriedades de entidade de cadeia para inserção e substitua operações. As cadeias de encriptados são armazenadas no serviço como propriedades binárias e estes são convertidos para cadeias depois de desencriptação.    

Para as tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Pode fazê-lo especificando a um atributo [EncryptProperty] (para entidades POCO que derivem de TableEntity) ou um resolvedor de encriptação nas opções de pedido. Um resolvedor de encriptação é um delegado que utiliza uma chave de partição, chave de linha e nome de propriedade e devolve um valor boleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada durante a escrita para a transmissão. Também fornece o delegado para a possibilidade de lógica em torno da forma como as propriedades são encriptadas. (Por exemplo, se X, então encriptar propriedade um; caso contrário, encriptar propriedades A e B.) Tenha em atenção que não seja necessário fornecer estas informações ao ler ou consultar entidades.

Tenha em atenção que merge não é atualmente suportado. Uma vez que um subconjunto de propriedades poderá ter sido encriptado anteriormente com uma chave diferente, basta intercalar as propriedades de novo e atualizar os metadados resultará na perda de dados. Intercalar o requer efetuar chamadas de serviço adicional para a entidade pré-existente de leitura do serviço ou utilizar uma nova chave por propriedade, que não são adequadas por motivos de desempenho.     

Para obter informações sobre a encriptação de dados da tabela, consulte [encriptação do lado do cliente e o Cofre de chaves do Azure para armazenamento do Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="modelling-relationships"></a>Modelling relações
Criação de modelos de domínio é um passo chave na estrutura dos sistemas complexos. Normalmente, o processo modelling é utilizado para identificar as entidades e as relações entre os mesmos como uma forma de compreender o domínio de empresa e informar sobre a estrutura do seu sistema. Esta secção centra-se na forma como pode traduzir alguns dos tipos de relação comuns encontrados nos modelos de domínio para as estruturas do serviço tabela. O processo de mapeamento de um modelo de dados lógico ao físico NoSQL baseada em modelo de dados-é muito diferente da utilizada ao conceber a base de dados relacional. Estrutura de bases de dados relacionais, normalmente, assume um processo de normalização de dados otimizado para minimizando a redundância – e uma capacidade de consulta declarativa que deduz como a implementação de como funciona a base de dados.  

### <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio do negócio ocorrerem com muita frequência: por exemplo, um departamento tem muitos colaboradores. Existem várias formas de implementar relações um-para-muitos no serviço tabela cada com os profissionais de TI e contras que podem ser relevantes para o cenário em particular.  

Considere o exemplo de uma grande empresa múltiplos national com dezenas de milhares de departamentos e entidades de empregado onde cada departamento tem muitos colaboradores e cada empregado como associados com um departamento específico. É uma abordagem armazenar departamento separado e as entidades de empregado como estas:  

![][1]

Este exemplo mostra uma relação um-para-muitos implícita entre tipos com base no **PartitionKey** valor. Cada departamento pode ter muitos colaboradores.  

Este exemplo mostra também uma entidade de departamento e respetivos entidades de empregado relacionadas com a mesma partição. Pode optar por utilizar partições diferentes, tabelas ou contas de armazenamento, mesmo que para os tipos de entidade diferentes.  

Uma abordagem alternativa é denormalize os seus dados e armazenar as entidades de empregado apenas com os dados de departamento denormalized conforme mostrado no exemplo seguinte. Neste cenário específico, esta abordagem denormalized não pode ser a melhor se tiver um requisito para poder alterar os detalhes de um Gestor de departamento porque para efetuar este procedimento, tem de atualizar todos os funcionários do departamento.  

![][2]

Para obter mais informações, consulte o [padrão Denormalization](#denormalization-pattern) mais à frente neste guia.  

A tabela seguinte resume as profissionais de TI e contras de cada uma das abordagens descritas acima para armazenar o empregado e entidades de departamento que tenham uma relação um-para-muitos. Deve também considerar a frequência esperada desempenhar várias operações: pode ser aceitável para ter uma estrutura que inclui uma operação dispendiosa, se essa operação só acontece com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Profissionais de TI</th>
<th>Contras</th>
</tr>
<tr>
<td>Separe os tipos de entidade, a mesma partição, a mesma tabela</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento com uma única operação.</li>
<li>Pode utilizar um EGT para manter a consistência, se tiver um requisito para modificar uma entidade de departamento sempre que atualizar/insert/eliminar uma entidade do empregado. Por exemplo, se mantém uma contagem de empregado departamentais para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Terá de obter um empregado e uma entidade de departamento para algumas atividades de cliente.</li>
<li>Operações de armazenamento acontecer na mesma partição. Em volumes de transação elevada, isto pode resultar num hotspot.</li>
<li>Não é possível mover um empregado para departamento novo utilizando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separado, partições diferentes ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade do departamento ou uma entidade de empregado com uma única operação.</li>
<li>Em volumes de transação elevada, isto pode ajudar a distribuídos a carga por mais partições.</li>
</ul>
</td>
<td>
<ul>
<li>Terá de obter um empregado e uma entidade de departamento para algumas atividades de cliente.</li>
<li>Não é possível utilizar EGTs para manter a consistência quando a atualização/insert/eliminar um empregado e a atualização de um departamento. Por exemplo, ao atualizar uma contagem do empregado na entidade de um departamento.</li>
<li>Não é possível mover um empregado para departamento novo utilizando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize para o tipo de entidade única</td>
<td>
<ul>
<li>Pode obter todas as informações que necessárias com um único pedido.</li>
</ul>
</td>
<td>
<ul>
<li>Poderá ser Caro manter a consistência se precisar de atualizar as informações de departamento (Isto iria requerem que Atualize todos os funcionários do departamento de um).</li>
</ul>
</td>
</tr>
</table>

Como escolher entre estas opções e de que os profissionais de TI e contras são mais significativas, depende os cenários de aplicação específica. Por exemplo, com que frequência modificar entidades departamento; todas as consultas de empregado precisa as informações adicionais departamentais; como fechar está os limites de escalabilidade no seu partições ou a sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um para um
Modelos de domínio podem incluir um para um relações entre entidades. Se precisar de implementar uma relação unívoca no serviço tabela, também tem de escolher como ligar as duas entidades relacionadas quando precisar de recuperar os dois. Esta ligação pode ser implícita, com base numa convenção de valores da chave ou explícito ao armazenar uma ligação sob a forma de **PartitionKey** e **RowKey** valores em cada entidade para a entidade relacionada. Para ver um debate do se deve armazenar as entidades relacionadas na mesma partição, consulte a secção [um-para-muitos relações](#one-to-many-relationships).  

Tenha em atenção que também existem considerações de implementação que podem originar a implementação relações um para um no serviço tabela:  

* Processamento de entidades grandes (para obter mais informações, consulte [padrão de entidades grandes](#large-entities-pattern)).  
* Implementar controlos de acesso (para obter mais informações, consulte [controlar o acesso com assinaturas de acesso partilhado](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Associação no cliente
Embora existam formas para modelar relações no serviço tabela, deve não se esqueça que as duas razões prime para utilizar o serviço de tabela são escalabilidade e desempenho. Se encontrar que são modelling muitas relações comprometer o desempenho e escalabilidade da sua solução, deverá solicitar a si se é necessário compilar todas as relações de dados para a estrutura da tabela. Poderá simplificar a estrutura e melhorar a escalabilidade e o desempenho da sua solução se permitir que a aplicação de cliente, efetuar quaisquer associações necessárias.  

Por exemplo, se tiver pequenas tabelas que contêm dados que não se altera com muita frequência, em seguida, pode obter estes dados uma vez e cache-la no cliente. Isto pode evitar e voltas repetidas para obter os mesmos dados. Nos exemplos que consultou neste guia, o conjunto de departamentos numa organização pequeno é provável que sejam pequenas e alterar raramente tornando um bom candidato para dados que a aplicação cliente pode transferir uma vez e cache como aspeto dos dados.  

### <a name="inheritance-relationships"></a>Relações de herança
Se a aplicação de cliente utiliza um conjunto de classes que fazem parte de uma relação de herança para representar as entidades de negócio, facilmente, pode manter as entidades no serviço tabela. Por exemplo, pode ter o seguinte conjunto de classes definidas na sua aplicação de cliente onde **pessoa** é uma classe abstracta.

![][3]

Pode manter instâncias das classes concretas duas no serviço tabela utilizando uma única tabela de pessoa utilizar entidades em que aspeto este:  

![][4]

Para obter mais informações sobre como trabalhar com diversos tipos de entidade na mesma tabela no código de cliente, consulte a secção [trabalhar com tipos de entidade heterogéneos](#working-with-heterogeneous-entity-types) mais à frente neste guia. Isto fornece exemplos de como reconhecer o tipo de entidade no código de cliente.  

## <a name="table-design-patterns"></a>Padrões de conceção da tabela
Nas secções anteriores, constatou que algumas detalhadas em debates sobre como otimizar a estrutura da tabela para ambos os dados de entidade obter utilizando consultas e para inserir, atualizar e eliminar dados de entidade. Esta secção descreve alguns padrões adequados para utilização com soluções de serviço tabela. Além disso, verá como pode praticamente abordar alguns dos problemas e compromissos gerados anteriormente neste guia. O diagrama a seguir resume as relações entre os diferentes padrões:  

![][5]

O mapa de padrão acima destaca algumas relações entre padrões (azul) e padrões anti (cor de laranja) que estão documentados neste guia. Obviamente, não existem muitos padrões merecem considerar. Por exemplo, um dos principais cenários para o serviço de tabela está a utilizar o [padrão de vista materializada](https://msdn.microsoft.com/library/azure/dn589782.aspx) do [segregação de responsabilidade de consulta de comando (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) padrão.  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão de índice secundário intra partição
Armazenar várias cópias de cada entidade utilizando diferentes **RowKey** valores (na mesma partição) para ativar rápido e eficiente pesquisas e as ordens de ordenação alternada utilizando diferentes **RowKey** valores. Atualizações entre cópias podem ser mantidas consistentes através do EGT.  

#### <a name="context-and-problem"></a>Contexto e do problema
O serviço tabela indexa automaticamente entidades utilizando o **PartitionKey** e **RowKey** valores. Isto permite que uma aplicação de cliente para obter uma entidade eficiente, utilizando estes valores. Por exemplo, utilizando a estrutura da tabela abaixo, uma aplicação cliente pode utilizar uma ponto de consulta para obter uma entidade de empregado individuais utilizando o nome de departamento e o id de empregado (o **PartitionKey** e **RowKey** valores). Um cliente também pode obter entidades ordenadas por id de empregado dentro de cada departamento.

![][6]

Se pretender conseguir localizar uma entidade de empregado baseada no valor da propriedade outro, tal como endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isto acontece porque o serviço de tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários ordenados por uma ordem diferente que **RowKey** ordem.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade com cada cópia utilizando outro **RowKey** valor. Se armazenar uma entidade com as estruturas mostradas abaixo, pode obter eficientemente entidades de empregado com base no id de endereço ou empregado de correio eletrónico. O prefixo de valores para o **RowKey**, "empid_" e "email_" permitem-lhe consultar um empregado único ou um intervalo de funcionários através da utilização de um intervalo de endereços de e-mail ou os ids de funcionário.  

![][7]

Os seguintes critérios de dois filtro (um procurar por id de empregado e uma pesquisa por endereço de e-mail) ambos especificar ponto consultas:  

* $filter = (PartitionKey eq 'Vendas') e (RowKey eq 'empid_000223')  
* $filter = (PartitionKey eq 'Vendas') e (RowKey eq 'email_jonesj@contoso.com')  

Se a consulta para um intervalo de entidades de empregado, pode especificar um intervalo ordenados por ordem de id de empregado ou um intervalo ordenados por ordem de endereço de e-mail através da consulta para entidades com o prefixo adequado no **RowKey**.  

* Localizar todos os funcionários do departamento de vendas com um id de empregado a utilização de intervalo 000100 para 000199: $filter = (PartitionKey eq 'Vendas') e (RowKey ge 'empid_000100') e (RowKey le 'empid_000199')  
* Localizar todos os funcionários do departamento de vendas com um endereço de e-mail que comece com a letra "a" utilização: $filter = (PartitionKey eq 'Vendas') e (RowKey ge 'email_a') e (RowKey lt 'email_b')  
  
  Tenha em atenção que é a partir da API de REST do serviço tabela para obter mais informações, consulte a sintaxe de filtro utilizada nos exemplos acima [entidades de consulta](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* O Table storage é relativamente cheap possa utilizar a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma preocupação principal. No entanto, deve sempre avaliar o custo da estrutura com base nos seus requisitos de armazenamento previsto e adicione apenas duplicadas entidades para suportar as consultas que irá executar a aplicação cliente.  
* Porque as entidades de índice secundário são armazenadas na mesma partição como as entidades originais, deve certificar-se de que não exceda os destinos de escalabilidade para uma partição individual.  
* Pode manter as entidades duplicadas consistentes entre si utilizando EGTs atualizar atomically duas cópias da entidade. Isto implica que deve armazenar todas as cópias de uma entidade na mesma partição. Para obter mais informações, consulte a secção [utilizando transações de grupo de entidade](#entity-group-transactions).  
* O valor utilizado para o **RowKey** tem de ser exclusivo para cada entidade. Considere utilizar os valores da chave compostos.  
* Preenchimento valores numéricos a **RowKey** (por exemplo, o id de empregado 000223), permite corrigir ordenar e filtrar com base nas maiúscula e limites inferiores.  
* Não é necessariamente necessário duplicar todas as propriedades de entidade. Por exemplo, se as consultas que pesquisa as entidades com o e-mail de endereço no **RowKey** nunca precisam de idade do empregado, estas entidades podem ter a seguinte estrutura:

![][8]

* É, normalmente, é melhor armazenar dados duplicados e certifique-se de que todos os dados que precisa com uma única consulta, pode obter a para utilizar uma consulta para localizar uma entidade e outra para pesquisar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a aplicação cliente tem de obter entidades utilizando uma variedade de diferentes chaves, quando o cliente tem de obter as entidades das ordens de ordenação diferente e, em que possa identificar cada entidade utilizando uma variedade de valores exclusivos. No entanto, deve ser se de que não excede os limites de escalabilidade da partição quando estiver a efetuar pesquisas de entidade com os diferentes **RowKey** valores.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de índice secundário entre partição](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* [Transações de grupo de entidade](#entity-group-transactions)
* [Trabalhar com tipos de entidade heterogénea](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice secundário entre partição
Armazenar várias cópias de cada entidade utilizando diferentes **RowKey** valores separar em partições ou separadas em tabelas para ativar rápido e eficiente pesquisas e as ordens de ordenação alternada utilizando diferentes **RowKey** valores.  

#### <a name="context-and-problem"></a>Contexto e do problema
O serviço tabela indexa automaticamente entidades utilizando o **PartitionKey** e **RowKey** valores. Isto permite que uma aplicação de cliente para obter uma entidade eficiente, utilizando estes valores. Por exemplo, utilizando a estrutura da tabela abaixo, uma aplicação cliente pode utilizar uma ponto de consulta para obter uma entidade de empregado individuais utilizando o nome de departamento e o id de empregado (o **PartitionKey** e **RowKey** valores). Um cliente também pode obter entidades ordenadas por id de empregado dentro de cada departamento.  

![][9]

Se pretender conseguir localizar uma entidade de empregado baseada no valor da propriedade outro, tal como endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isto acontece porque o serviço de tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários ordenados por uma ordem diferente que **RowKey** ordem.  

São prevendo um volume muito elevado de transações em relação a estas entidades e pretende minimizar o risco do serviço tabela limitação dos clientes.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade com cada cópia utilizando diferentes **PartitionKey** e **RowKey** valores. Se armazenar uma entidade com as estruturas mostradas abaixo, pode obter eficientemente entidades de empregado com base no id de endereço ou empregado de correio eletrónico. O prefixo de valores para o **PartitionKey**, "empid_" e "email_" permitem-lhe identificar que o índice que pretende utilizar para uma consulta.  

![][10]

Os seguintes critérios de dois filtro (um procurar por id de empregado e uma pesquisa por endereço de e-mail) ambos especificar ponto consultas:  

* $filter = (PartitionKey eq ' empid_Sales') e (RowKey eq '000223')
* $filter = (PartitionKey eq ' email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se a consulta para um intervalo de entidades de empregado, pode especificar um intervalo ordenados por ordem de id de empregado ou um intervalo ordenados por ordem de endereço de e-mail através da consulta para entidades com o prefixo adequado no **RowKey**.  

* Localizar todos os funcionários do departamento de vendas com um id de empregado no intervalo **000100** para **000199** ordenado na utilização de ordem de id de empregado: $filter = (PartitionKey eq ' empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Localizar todos os funcionários do departamento de vendas com um endereço de e-mail que começa com "a" ordenado na utilização de ordem de endereço de e-mail: $filter = (PartitionKey eq ' email_Sales') e (RowKey ge "a") e (RowKey lt 'b')  

Tenha em atenção que é a partir da API de REST do serviço tabela para obter mais informações, consulte a sintaxe de filtro utilizada nos exemplos acima [entidades de consulta](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as entidades duplicadas eventualmente consistente entre si utilizando o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice principal e secundário.  
* O Table storage é relativamente cheap possa utilizar a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma preocupação principal. No entanto, deve sempre avaliar o custo da estrutura com base nos seus requisitos de armazenamento previsto e adicione apenas duplicadas entidades para suportar as consultas que irá executar a aplicação cliente.  
* O valor utilizado para o **RowKey** tem de ser exclusivo para cada entidade. Considere utilizar os valores da chave compostos.  
* Preenchimento valores numéricos a **RowKey** (por exemplo, o id de empregado 000223), permite corrigir ordenar e filtrar com base nas maiúscula e limites inferiores.  
* Não é necessariamente necessário duplicar todas as propriedades de entidade. Por exemplo, se as consultas que pesquisa as entidades com o e-mail de endereço no **RowKey** nunca precisam de idade do empregado, estas entidades podem ter a seguinte estrutura:
  
  ![][11]
* É, normalmente, é melhor armazenar dados duplicados e certifique-se de que pode obter todos os dados que precisa com uma única consulta a para utilizar uma consulta para localizar uma entidade com o índice secundário e outro para pesquisa os dados necessários no índice principal.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a aplicação cliente tem de obter entidades utilizando uma variedade de diferentes chaves, quando o cliente tem de obter as entidades das ordens de ordenação diferente e, em que possa identificar cada entidade utilizando uma variedade de valores exclusivos. Utilize este padrão quando pretender evitar exceder os limites de escalabilidade da partição quando estiver a efetuar pesquisas de entidade com os diferentes **RowKey** valores.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intra partição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogénea](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistente
Ative o comportamento eventualmente consistente em limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e do problema
EGTs ativar transações atómicas entre várias entidades que partilham a mesma chave de partição. Para o desempenho e as razões de escalabilidade, pode optar por armazenar as entidades que têm requisitos de consistência em partições separadas ou num sistema de armazenamento separada: neste cenário, não é possível utilizar EGTs para manter a consistência. Por exemplo, pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, nas tabelas diferentes, em contas de armazenamento diferente.  
* Uma entidade armazenados no serviço tabela e um blob armazenada no serviço Blob.  
* Uma entidade armazenada no serviço tabela e um ficheiro num sistema de ficheiros.  
* Um arquivo de entidade no serviço tabela indexado ainda utilizando o serviço de pesquisa do Azure.  

#### <a name="solution"></a>Solução
Ao utilizar as filas do Azure, pode implementar uma solução que fornece a consistência eventual entre dois ou mais partições ou sistemas de armazenamento.
Para ilustrar esta abordagem, partem do princípio de que tem um requisito para conseguir arquivar antigo entidades do empregado. Entidades de empregado antigo raramente são consultadas e devem ser excluídas da todas as atividades que lidam com os empregados atuais. Para implementar este requisito armazenar funcionários ativos no **atual** tabela e funcionários antigos o **arquivo** tabela. Arquivar um empregado requer a eliminar a entidade do **atual** uma tabela e adicionar a entidade de **arquivo** tabela, mas não é possível utilizar um EGT para efetuar estas duas operações. Para evitar o risco de uma falha faz com que uma entidade a aparecer em ambos os ou nenhuma das tabelas, a operação de arquivo tem de ser eventualmente consistente. O diagrama de sequência seguinte descreve os passos nesta operação. É fornecido mais detalhadamente para caminhos de exceção a seguir de texto.  

![][12]

Um cliente inicia a operação de arquivo por colocar uma mensagem sobre uma fila do Azure, neste exemplo, para arquivar empregado #456. Uma função de trabalho consulta a fila de novas mensagens; Quando encontrar um, lê a mensagem e mantém uma cópia oculta na fila. A função de trabalho seguinte obtém uma cópia da entidade do **atual** tabela, insere uma cópia no **arquivo** tabela e, em seguida, elimina original do **atual** tabela. Por fim, se não existirem erros de nenhum dos passos anteriores, a função de trabalho elimina a mensagem oculta da fila.  

Neste exemplo, o passo 4 insere o empregado para o **arquivo** tabela. -Adicionar o empregado para um blob no serviço Blob ou um ficheiro num sistema de ficheiros.  

#### <a name="recovering-from-failures"></a>Recuperar de falhas
É importante que as operações nos passos **4** e **5** tem de ser *idempotent* no caso da função de trabalho tem de reiniciar a operação de arquivo. Se estiver a utilizar o serviço de tabela para o passo **4** deve utilizar uma operação "Inserir ou substituir"; para o passo **5** deve utilizar um "eliminar se existe" operação na biblioteca de cliente estiver a utilizar. Se estiver a utilizar outro sistema de armazenamento, tem de utilizar uma operação idempotent adequado.  

Se a função de trabalho nunca conclui passo **6**, em seguida, após um tempo limite de mensagem reappears na fila pronta para a função de trabalho tentar reprocessá-lo. A função de trabalho pode verificar o número de vezes uma mensagem na fila foi ler e, se necessário, sinalizador é uma mensagem de "nocivas" para investigação enviando-os para uma fila separada. Para obter mais informações sobre a leitura de mensagens de fila e a verificar a contagem de dequeue, consulte [obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros dos serviços de tabela e fila são erros transitórios e a aplicação cliente deve incluir a lógica de repetição adequado para lidar com os mesmos.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução fornece para isolamento da transação. Por exemplo, um cliente foi possível ler o **atual** e **arquivo** tabelas quando a função de trabalho foi entre passos **4** e **5**e ver uma vista dos dados inconsistente. Tenha em atenção que os dados serão consistentes eventualmente.  
* Tem de ser se de que os passos 4 e 5 idempotent para garantir a consistência eventual.  
* Pode dimensionar a solução utilizando várias filas e instâncias de função de trabalho.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando pretende garantir a consistência eventual entre entidades que existe nas tabelas ou partições diferentes. Pode expandir este padrão para assegurar a consistência eventual para operações entre o serviço de tabela e o serviço Blob e outro armazenamento do Azure origens de dados, tais como a base de dados ou o sistema de ficheiros.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidade](#entity-group-transactions)  
* [Intercalação ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento da transação é importante para a sua solução, deve considerar ao reestruturar as tabelas para permitir a utilização EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter as entidades de índice para ativar pesquisas eficiente que devolvem apresenta uma lista de entidades.  

#### <a name="context-and-problem"></a>Contexto e do problema
O serviço tabela indexa automaticamente entidades utilizando o **PartitionKey** e **RowKey** valores. Isto permite que uma aplicação de cliente para obter uma entidade eficiente, utilizando uma consulta de ponto. Por exemplo, utilizando a estrutura da tabela abaixo, uma aplicação cliente pode eficientemente obter uma entidade de empregado individuais utilizando o nome de departamento e o id de empregado (o **PartitionKey** e **RowKey**).  

![][13]

Se pretender ser capaz de obter uma lista de entidades de empregado baseado no valor da propriedade não exclusivo outro, tais como os respetivos nomes último, também tem de utilizar uma análise de partição menos eficiente para localizar correspondências em vez de utilizarem um índice para procurá-los diretamente. Isto acontece porque o serviço de tabela não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para ativar pesquisa por nome da última com a estrutura de entidade mostrada acima, tem de manter as listas de ids de funcionário. Se pretender obter as entidades de empregado com um nome de último específico, como Jones, tem de primeiro localizar a lista de ids de empregado para os funcionários com Jones como o seu apelido e, em seguida, obter as entidades de empregado. Existem três opções principais para armazenar as listas de ids de empregado:  

* Utilize o blob storage.  
* Crie entidades do índice a mesma partição as entidades do empregado.  
* Crie entidades de índice numa partição separada ou tabela.  

<u>Opção #1: Armazenamento de BLOBs de utilização</u>  

Para a primeira opção, pode criar um blob para cada apelido exclusivo e, no arquivo de cada blob uma lista do **PartitionKey** (departamento) e **RowKey** (id de empregado) os valores para os empregados que tem essa apelido. Quando adicionar ou eliminar um empregado deve certificar-se de que o conteúdo do blob relevante é eventualmente consistente com as entidades do empregado.  

<u>Opção #2:</u> criar entidades de índice na mesma partição  

Para a segunda opção, utilizam as entidades de índice que armazenam os dados seguintes:  

![][14]

O **EmployeeIDs** propriedade contém uma lista de ids de empregado para os funcionários com o último nome armazenado no **RowKey**.  

Os passos seguintes descrevem o processo que deve seguir quando estiver a adicionar um empregado a nova se estiver a utilizar a segunda opção. Neste exemplo, que estamos a adicionar um empregado com Id 000152 e um apelido Jones no departamento de vendas:  

1. Obter a entidade de índice com um **PartitionKey** valor "Vendas" e o **RowKey** valor "Jones." Guarde o ETag desta entidade para utilizar no passo 2.  
2. Criar uma transação de grupo de entidade (ou seja, uma operação em lote) insere a entidade de empregado novo (**PartitionKey** valor "Vendas" e **RowKey** valor "000152") e atualiza a entidade de índice (**PartitionKey** valor "Vendas" e **RowKey** valor "Jones") ao adicionar o novo id de empregado à lista no campo EmployeeIDs. Para mais informações sobre transações do grupo de entidade, consulte [entidade grupo transações](#entity-group-transactions).  
3. Se a transação de grupo de entidade falhar devido a um erro de simultaneidade otimista (alguém apenas alterou a entidade de índice), terá de recomeçar novamente no passo 1.  

Pode utilizar uma abordagem semelhante para eliminar um empregado, se estiver a utilizar a segunda opção. A alteração do apelido um empregado é ligeiramente mais complexa porque terá de executar uma transação de grupo de entidade que atualiza três entidades: a entidade de empregado, a entidade de índice para o último nome antigo e a entidade de índice para o último nome novo. Tem de obter cada entidade antes de efetuar quaisquer alterações para obter os valores de ETag, em seguida, pode utilizar para efetuar as atualizações utilizando a simultaneidade otimista.  

Os passos seguintes descrevem o processo que deve seguir quando precisar de procurar todos os funcionários com um determinado apelido, um departamento, se estiver a utilizar a segunda opção. Neste exemplo, vamos são procurar todos os funcionários com apelido Jones no departamento de vendas:  

1. Obter a entidade de índice com um **PartitionKey** valor "Vendas" e o **RowKey** valor "Jones."  
2. Analisar a lista de Ids no campo EmployeeIDs do empregado.  
3. Se precisar de informações adicionais sobre cada um destes funcionários (por exemplo, os respetivos endereços de e-mail), cada uma das entidades empregado utilizando obter **PartitionKey** valor "Vendas" e **RowKey** valores da lista de funcionários que obteve no passo 2.  

<u>Opção #3:</u> criar entidades de índice numa partição separada ou tabela  

Para a terceira opção, utilizam as entidades de índice que armazenam os dados seguintes:  

![][15]

O **EmployeeIDs** propriedade contém uma lista de ids de empregado para os funcionários com o último nome armazenado no **RowKey**.  

A terceira opção, não é possível utilizar EGTs para manter a consistência porque as entidades do índice estão numa partição separada das entidades do empregado. Deve certificar-se de que as entidades de índice são eventualmente consistentes com as entidades do empregado.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução necessita, pelo menos, duas consultas para obter entidades correspondentes: um para consultar as entidades de índice para obter a lista de **RowKey** valores e, em seguida, consultas para obter a cada entidade na lista.  
* Uma vez que uma entidade individuais tem um tamanho máximo de 1 MB, opção #2 e a opção #3 na solução partem do princípio de que a lista de ids de empregado para qualquer apelido determinado nunca é superior a 1 MB. Se a lista de ids de empregado é provável que seja superior a 1 MB de tamanho, utilize a opção #1 e armazenar os dados do índice no blob storage.  
* Se utilizar a opção #2 (utilizando EGTs para lidar com a adição e eliminar os funcionários e a alteração do apelido um empregado), tem de avaliar se o volume de transações irão abordar os limites de escalabilidade numa determinada partição. Se for este o caso, deve considerar uma solução eventualmente consistente (opção #1 ou opção #3) que utiliza filas para processar os pedidos de atualização e permite-lhe armazenar as entidades de índice numa partição separada das entidades do empregado.  
* Opção #2 nesta solução parte do princípio de que pretende procurar por nome da última dentro de um departamento: por exemplo, pretende obter uma lista de funcionários com um apelido Jones no departamento de vendas. Se pretender ser capaz de procurar todos os funcionários com um apelido Jones em toda a organização, utilize a opção #1 ou a opção #3.
* Pode implementar uma solução baseada em fila que fornece a consistência eventual (consulte o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando pretender pesquisar um conjunto de entidades que todos os partilham um valor de propriedade comuns, como todos os funcionários com o último nome Jones.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Transações de grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogénea](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de denormalization
Combine dados relacionados em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e do problema
Numa base de dados relacional, normalizar normalmente dados a remover duplicação, resultando em consultas que obtêm dados de várias tabelas. Se normalizar os dados nas tabelas do Azure, tem de se vários ida e volta do cliente para o servidor para obter os dados relacionados. Por exemplo, com a estrutura da tabela abaixo necessitam de duas ida e volta ao obter os detalhes de um departamento: um para obter a entidade de departamento, que inclui o id do gestor e, em seguida, outro pedido para obter detalhes do gestor numa entidade empregado.  

![][16]

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, denormalize os dados e manter uma cópia dos detalhes do gestor na entidade departamento. Por exemplo:  

![][17]

Com entidades de departamento armazenadas com estas propriedades, agora pode obter todos os detalhes que terá sobre um departamento utilizando uma consulta de ponto.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Há alguns custos de sobrecarga associados ao armazenamento alguns dados duas vezes. O benefício de desempenho (resultantes de menos pedidos para o serviço de armazenamento), normalmente, prevalece sobre o aumento marginal custos de armazenamento (e este custo é parcialmente deslocamento por uma redução do número de transações que necessita para obter os detalhes de um departamento).  
* Tem de manter a consistência das duas entidades que armazenam informações sobre os gestores. Pode processar o problema de consistência utilizando EGTs para atualizar várias entidades numa única transação atómica: neste caso, a entidade de departamento e a entidade de empregado para o Gestor de departamento são armazenados na mesma partição.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando frequentemente terá de procurar informações relacionadas. Este padrão reduz o número de consultas que tem de se para obter os dados requer que o cliente.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogénea](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composta
Utilize composta **RowKey** valores para permitir que um cliente para procurar dados relacionados com um ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e do problema
Base de dados relacional, é bastante natural utilizar associações nas consultas para devolver relacionadas peças de dados para o cliente numa única consulta. Por exemplo, poderá utilizar o id de empregado consultar uma lista de entidades relacionadas que contenham o desempenho e reveja os dados para esse empregado.  

Suponha que estão a armazenar as entidades de empregado no serviço tabela utilizando a seguinte estrutura:  

![][18]

Terá também de armazenar dados históricos relacionadas com revisões e desempenho para cada ano que trabalhou o empregado para a sua organização e precisa de ser capazes de aceder a estas informações por ano. Uma das alternativas consiste em criar outra tabela que armazena as entidades com a estrutura seguinte:  

![][19]

Tenha em atenção que com esta abordagem, pode optar de duplicação de algumas informações (por exemplo, o nome próprio e apelido) na nova entidade que lhe permite obter os dados com um único pedido. No entanto, não é possível manter a consistência forte porque não é possível utilizar um EGT para atualizar as dois entidades atomically.  

#### <a name="solution"></a>Solução
Armazene um novo tipo de entidade na tabela original através de entidades com a estrutura seguinte:  

![][20]

Repare como o **RowKey** agora é uma chave composta constituída pelo id de empregado e o ano dos dados de revisão que permite-lhe obter o desempenho do empregado e analisar dados com um único pedido de uma única entidade.  

O exemplo a seguir descreve como pode obter todos os dados de revisão para um funcionário específico (por exemplo, um empregado 000123 no departamento de vendas):  

$filter = (PartitionKey eq 'Vendas') e (RowKey ge 'empid_000123') e (RowKey lt 'empid_000124') & $select = RowKey, Gestor de classificação, ponto a ponto de classificação, comentários  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Deve utilizar um caráter de separação adequado que torna mais fácil analisar o **RowKey** valor: por exemplo, **000123_2012**.  
* Também estão a armazenar esta entidade na mesma partição como outras entidades que contêm dados relacionados para o empregado mesmo, o que significa que pode utilizar EGTs para manter a consistência forte.
* Deve considerar a frequência irá consultar os dados para determinar se este padrão é apropriado.  Por exemplo, se irá aceder os dados de revisão com pouca frequência e os dados de empregado principal, muitas vezes, deve mantê-los como separadas entidades.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando é necessário para armazenar um ou mais atributos relacionados. entidades essa consulta frequentemente.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidade](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogénea](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de seguimento de registo
Obter o  *n*  entidades recentemente adicionadas a uma partição utilizando um **RowKey** valor ordena na data inversa e ordem de tempo.  

#### <a name="context-and-problem"></a>Contexto e do problema
Um requisito comuns está a conseguir obter as entidades criadas recentemente, por exemplo o mais recente dez despesa afirmações submetidas por um funcionário. Suporte de consulta de tabela um **$top** operação para devolver o primeiro de consulta  *n*  entidades de um conjunto: não há nenhuma operação de consulta equivalente para devolver as último entidades n num conjunto.  

#### <a name="solution"></a>Solução
Armazenar as entidades com um **RowKey** que naturalmente Ordena por ordem inversa de data/hora através da utilização de entrada, por isso, o mais recente é sempre o primeiro na tabela.  

Por exemplo, para conseguir obter as afirmações despesa mais recentes dez submetidas por um empregado, pode utilizar um valor de escala inversa derivado de data/hora atual. O exemplo de código do c# seguinte mostra uma forma de criar um valor de "inverted ticks" adequado para uma **RowKey** que ordena a partir da mais recente para mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Pode regressar ao valor de tempo da data utilizando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de tabela tem o seguinte aspeto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Tem de preencher o valor de escala inversa com líderes zeros para garantir que o valor da cadeia ordena conforme esperado.  
* Tem de ser em consideração os destinos de escalabilidade no nível de uma partição. Seja cuidadoso não criar partições do ponto ativo.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisar de aceder a entidades na ordem inversa de data/hora ou quando precisa de aceder as entidades mais recentemente adicionadas.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Preceder / acrescentar anti padrão](#prepend-append-anti-pattern)  
* [Obter entidades](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de eliminação de um volume elevado
Ativar a eliminação de um grande volume de entidades armazenando todas as entidades para eliminação em simultâneo na sua própria tabela separada; Elimine as entidades por eliminar a tabela.  

#### <a name="context-and-problem"></a>Contexto e do problema
Muitas aplicações eliminar dados antigos que já não tem de estar disponíveis para uma aplicação cliente ou que a aplicação tem arquivadas para outro meio de armazenamento. Normalmente, identificar esses dados por uma data: por exemplo, tiver um requisito para eliminar registos de todos os pedidos de início de sessão que são mais de 60 dias.  

Um design possível consiste em utilizar a data e hora do pedido no início de sessão a **RowKey**:  

![][21]

Esta abordagem evita a hotspots de partição porque a aplicação pode inserir e eliminar entidades de início de sessão para cada utilizador numa partição separada. No entanto, esta abordagem pode ser dispendiosa e demorada se tiver um grande número de entidades porque primeiro tem de executar uma análise da tabela para identificar todas as entidades para eliminar e, em seguida, tem de eliminar cada entidade antiga. Tenha em atenção que pode reduzir o número de ida e volta ao servidor necessário para eliminar as entidades antigas por vários pedidos de eliminação de criação de batches para EGTs.  

#### <a name="solution"></a>Solução
Utilize uma tabela em separado para cada dia de início de sessão falhadas. Pode utilizar o design de entidade acima para evitar a hotspots quando são inserir entidades e eliminar entidades antigas agora é simplesmente uma pergunta de eliminação de uma tabela de todos os dias (uma operação de armazenamento única) em vez de localizar e eliminar centenas e milhares de entidades de início de sessão individuais todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura suporta outras formas a aplicação utilizará os dados, tais como procurar entidades específicas, com outros dados ou gerar agregam informações de ligação?  
* O design evitar frequente oportunidades quando são inserir novas entidades?  
* Espere um atraso se pretender reutilizar o mesmo nome de tabela após eliminá-lo. É melhor utilizar sempre a nomes de tabela exclusivo.  
* Espere alguns limitação ao primeiro utilizar uma nova tabela enquanto o serviço tabela aprende os padrões de acesso e distribui as partições entre nós. Deve considerar a frequência tem de criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão se tiver um grande volume de entidades que tem de eliminar ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidade](#entity-group-transactions)
* [A modificação de entidades](#modifying-entities)  

### <a name="data-series-pattern"></a>Padrão de séries de dados
Série de dados completa de loja uma entidade única para minimizar o número de pedidos que efetuar.  

#### <a name="context-and-problem"></a>Contexto e do problema
Um cenário comum é para uma aplicação armazenar uma série de dados que, normalmente, tem de obter ao mesmo tempo. Por exemplo, a aplicação poderá registar como muitas mensagens de MI cada empregado envia a cada hora e, em seguida, cada utilizador enviado através de 24 horas anteriores, utilize estas informações para desenhar a quantidade de mensagens. Poderá ser uma estrutura armazenar 24 entidades para cada empregado:  

![][22]

Com esta conceção, pode facilmente localizar e atualizar a entidade a atualizar para cada empregado sempre que a aplicação tem de atualizar o valor de contagem de mensagens. No entanto, para obter as informações para desenhar um gráfico da atividade para as 24 horas anteriores, tem de obter 24 entidades.  

#### <a name="solution"></a>Solução
Utilize a seguinte estrutura com uma propriedade de diferente para armazenar a contagem de mensagens para cada hora:  

![][23]

Com esta conceção, pode utilizar uma operação de intercalação para atualizar a contagem de mensagens para um empregado para uma hora específica. Agora, pode obter todas as informações necessárias desenhar o gráfico com um pedido para uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Se a série de dados completo não se ajusta uma única entidade (uma entidade pode ter até 252 propriedades), utilize um arquivo de dados alternativos, tais como um blob.  
* Se tiver vários clientes em simultâneo a atualizar uma entidade, terá de utilizar o **ETag** para implementar a simultaneidade otimista. Se tiver muitos clientes, pode deparar-se elevada contenção.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessita de atualizar e obter uma série de dados associada a uma entidade individuais.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de entidades grandes](#large-entities-pattern)  
* [Intercalação ou substituir](#merge-or-replace)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) (se estiver a armazenar as séries de dados num blob)  

### <a name="wide-entities-pattern"></a>Padrão de entidades Wide
Utilize várias entidades físicas para armazenar as entidades lógicas com mais do que 252 propriedades.  

#### <a name="context-and-problem"></a>Contexto e do problema
Uma entidade individual pode ter mais do que 252 propriedades (excluindo as propriedades de sistema obrigatório) e não é possível armazenar mais de 1 MB de dados no total. Numa base de dados relacional, normalmente, obterá arredondar os limites de tamanho de uma linha ao adicionar uma nova tabela e impor uma relação de 1 para 1 entre eles.  

#### <a name="solution"></a>Solução
Utilizar o serviço tabela, pode armazenar várias entidades para representar um objeto de empresas de grandes dimensões única com mais do que 252 propriedades. Por exemplo, se pretender armazenar uma contagem do número de mensagens de MI enviadas por cada funcionário para os últimos 365 dias, pode utilizar a seguinte estrutura que utiliza duas entidades com diferentes esquemas:  

![][24]

Se precisar de efetuar uma alteração que necessita de atualizar as entidades para mantê-las sincronizados entre si pode utilizar um EGT. Caso contrário, pode utilizar uma operação de intercalação único para atualizar a contagem de mensagens de um dia específico. Para obter todos os dados para um empregado individuais tem de obter as entidades, o que pode fazer com dois pedidos eficiente que utilizam ambos um **PartitionKey** e um **RowKey** valor.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Obter uma entidade lógica completa envolve a, pelo menos, dois transações de armazenamento: uma para obter a cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessário para armazenar as entidades cujo tamanho ou o número de propriedades excede os limites de uma entidade no serviço tabela individuais.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Transações de grupo de entidade](#entity-group-transactions)
* [Intercalação ou substituir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de entidades grandes
Utilize o blob storage para armazenar os valores de propriedade grandes.  

#### <a name="context-and-problem"></a>Contexto e do problema
Uma entidade individuais não é possível armazenar mais de 1 MB de dados no total. Se uma ou várias das suas propriedades armazenarem valores que fazer com que o tamanho total de entidade exceder este valor, não é possível armazenar a entidade completa no serviço tabela.  

#### <a name="solution"></a>Solução
Se a entidade exceder 1 MB de tamanho, porque uma ou mais propriedades contém uma grande quantidade de dados, pode armazenar dados no serviço Blob e, em seguida, armazene o endereço do blob numa propriedade na entidade. Por exemplo, pode armazenar a fotografia de um empregado no blob storage e armazenar uma ligação para a fotografia no **fotografia** propriedade de entidade empregado:  

![][25]

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter a consistência eventual entre a entidade no serviço tabela e os dados no serviço Blob, utilize o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades.
* Obter uma entidade completa envolve a, pelo menos, dois transações de armazenamento: uma para obter a entidade e outra para obter os dados de Blobs.  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tiver de armazenar as entidades cujo tamanho excede os limites de uma entidade no serviço tabela individuais.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades Wide](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Preceder/acrescentar anti padrão
Aumente a escalabilidade quando tiver um grande volume de inserções por propagando-se a inserções em várias partições.  

#### <a name="context-and-problem"></a>Contexto e do problema
Prefixação ou acrescentar entidades para as entidades armazenadas normalmente resulta na aplicação adicionar novas entidades à partição do primeira ou última de uma sequência de partições. Neste caso, todos os inserções em qualquer momento estão a decorrer na mesma partição, criar um hotspot que impede que o serviço de tabela de inserções de balanceamento em vários nós e, possivelmente, fazendo com que a aplicação para os destinos de escalabilidade da partição de acessos de carga. Por exemplo, se tiver uma aplicação que os registos de rede e acesso a recursos pelos funcionários, em seguida, uma estrutura de entidade conforme mostrado abaixo pode resultar numa partição a hora atual se tornar um hotspot se o volume de transações atingir o destino de escalabilidade para uma partição individual:  

![][26]

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um hotspot em qualquer partição específica, como os eventos de registos de aplicações:  

![][27]

Aviso com este exemplo como tanto o **PartitionKey** e **RowKey** são chaves compostas. O **PartitionKey** utiliza o departamento e o empregado id para distribuir o registo através de várias partições.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura chave alternativa que evita a criação de partições frequente no inserções de forma eficiente suporta consultas que a aplicação cliente torna?  
* O volume previsto de transações significa que é provável que a atingir os objetivos de escalabilidade para uma partição individual e limitadas pelo serviço de armazenamento?  

#### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o padrão de anti prepend/acrescentar quando o volume de transações é provável que resultará na limitação pelo serviço de armazenamento ao aceder a uma partição de acesso frequente.  

#### <a name="related-patterns-and-guidance"></a>Padrões relacionados e as orientações
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de seguimento de registo](#log-tail-pattern)  
* [A modificação de entidades](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Padrão de anti de dados do registo
Normalmente, deve utilizar o serviço Blob em vez do serviço tabela para armazenar dados de registo.  

#### <a name="context-and-problem"></a>Contexto e do problema
Caso de utilização de um comuns para dados de registo são obter uma seleção de entradas de registo de um intervalo de data/hora específico: por exemplo, pretender localizar todos os erros e mensagens de evento crítico que a aplicação, registada entre 15:04 e 15:06 numa data específica. Não pretende utilizar a data e hora da mensagem de registo para determinar a partição Guardar entidades de registo para: que resulta numa partição frequente porque em qualquer momento, todas as entidades de registo irão partilhar o mesmo **PartitionKey** valor (consulte a secção [Prepend/acrescentar anti padrão](#prepend-append-anti-pattern)). Por exemplo, o esquema de entidade seguinte para uma mensagem do registo resulta numa partição frequente porque a aplicação escreve todas as mensagens de registo para a partição para a data atual e a hora:  

![][28]

Neste exemplo, o **RowKey** inclui a data e hora da mensagem de registo para garantir que as mensagens do registo são armazenadas ordenados por ordem de data/hora e inclui um id de mensagem no caso de várias mensagens de registo partilham a mesma data e hora.  

Outra abordagem é utilizar um **PartitionKey** que garante que a aplicação escreve mensagens através de um intervalo de partições. Por exemplo, se a origem da mensagem de registo fornece uma forma de distribuir mensagens através de várias partições, pode utilizar o esquema de entidade seguintes:  

![][29]

No entanto, o problema com este esquema é que, para obter todas as mensagens de registo para um intervalo de tempo específico tem pesquisa cada partição na tabela.

#### <a name="solution"></a>Solução
A secção anterior realçado o problema de tentar utilizar o serviço tabela para armazenar as entradas de registo e dois sugeridos, unsatisfactory, estruturas. Uma solução gerou uma partição com o risco de fraco desempenho ao escrever as mensagens do registo; frequente a solução de outra resultou no desempenho das consultas fraco devido à necessidade de analisar cada partição na tabela para obter mensagens de registo para um intervalo de tempo específico. O blob storage oferece a melhor solução para este tipo de cenário e este é o Azure como arquivos de análise de armazenamento de dados de registo recolhe.  

Esta secção descreve como o análise de armazenamento armazena dados de registo no armazenamento de BLOBs, como uma ilustração desta abordagem para armazenar dados que normalmente consulta por intervalo.  

Análise de armazenamento armazena as mensagens do registo num formato delimitado em vários blobs. O formato delimitado torna mais fácil para analisar os dados na mensagem do registo de uma aplicação de cliente.  

Análise de armazenamento utiliza uma convenção de nomenclatura de blobs que lhe permite localizar o blob (ou blobs) que contêm as mensagens de registo para o qual esteja a pesquisar. Por exemplo, um blob com o nome "queue/2014/07/31/1800/000001.log" contém mensagens de registo relacionados com o serviço de fila para a hora, começando 18:00 em 31 de Julho de 2014. "000001" indica que este é o primeiro ficheiro de registo para este período. Análise de armazenamento também regista carimbos das mensagens de registo primeiro e último armazenados no ficheiro como parte dos metadados do blob. A API para ativa de armazenamento de BLOBs localizar os blobs num contentor com base no prefixo de nome: para localizar todos os blobs que contêm dados de registo da fila para a hora, começando 18:00, pode utilizar o prefixo "fila/2014/07/31/1800".  

Memórias intermédias de análise do armazenamento registar mensagens internamente e, em seguida, periodicamente atualiza o blob adequado ou cria uma nova com o lote mais recente de entradas de registo. Isto reduz o número de gravações que tem de efetuar para o serviço blob.  

Se estiver a implementar uma solução semelhante na sua própria aplicação, tem de considerar como gerir o compromisso entre fiabilidade (escrever cada entrada de registo para o blob storage como ocorre) e o custo e escalabilidade (colocação em memória intermédia atualizações na sua aplicação e escrever em armazenamento de BLOBs em lotes).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Quando decidir como armazenar dados de registo, considere os seguintes pontos:  

* Se criar um design de tabela que evita partições frequente potenciais, pode constatar que não é possível aceder os dados de registo de forma eficiente.  
* Processar os dados de registo, um cliente, muitas vezes, tem de carregar registos muitos.  
* Apesar dos dados de registo, muitas vezes, estão estruturados, armazenamento de blob pode ser a melhor solução.  

### <a name="implementation-considerations"></a>Considerações de implementação
Esta secção descreve algumas das considerações para tenha em consideração quando implementar os padrões descritos nas secções anteriores. Na maioria desta secção utiliza exemplos escritos em c# que utilizam a biblioteca de clientes de armazenamento (versão 4.3.0 no momento da escrita).  

### <a name="retrieving-entities"></a>Obter entidades
Tal como explicado na secção [estrutura para consultar](#design-for-querying), mais eficiente consulta é uma consulta de ponto. No entanto, em alguns cenários poderá ter de obter várias entidades. Esta secção descreve algumas abordagens comuns para obter entidades utilizando a biblioteca de clientes de armazenamento.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Executar uma consulta de ponto de utilizar a biblioteca de clientes de armazenamento
A forma mais fácil para executar uma consulta de ponto de consiste em utilizar o **obter** operação de tabela, conforme mostrado no seguinte c# fragmento de código que obtém uma entidade com uma **PartitionKey** do valor "Vendas" e um **RowKey** do valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Repare como neste exemplo espera que a entidade obtém seja do tipo **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>A obter várias entidades com LINQ
Pode obter várias entidades utilizando LINQ com bibliotecas de cliente de armazenamento e especificar uma consulta com um **onde** cláusula. Para evitar uma análise da tabela, deve sempre incluir o **PartitionKey** valor nas where cláusula e se for possível o **RowKey** valor para evitar análises de tabela e partição. O serviço tabela suporta um conjunto limitado de operadores de comparação (maiores, maiores ou igual, menos a, menor que ou iguais, iguais e não igual) para utilizar nas where cláusula. O seguinte fragmento de código do c# localiza todos os funcionários cuja última começa de nome com "B" (partindo do princípio que o **RowKey** armazena o último nome) no departamento de venda (partindo do princípio de **PartitionKey** armazena o nome de departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Repare como a consulta especifica ambos um **RowKey** e um **PartitionKey** para garantir um melhor desempenho.  

O exemplo de código seguinte mostra a funcionalidade equivalente utilizando a API fluent (para obter mais informações sobre as APIs fluent em geral, consulte o artigo [melhores práticas para criar uma API Fluent](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> O exemplo nests vários **CombineFilters** métodos para incluir as três condições de filtro.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>A obter grande número de entidades de uma consulta
Uma consulta ideal devolve uma entidade individuais com base num **PartitionKey** valor e um **RowKey** valor. No entanto, em alguns cenários pode ter um requisito para devolver muitas entidades, da mesma partição ou mesmo a partir de várias partições.  

Deve sempre totalmente testar o desempenho da sua aplicação no tais cenários.  

Uma consulta contra o serviço de tabela pode devolver, no máximo de 1000 entidades em simultâneo e poderá ser executada para um máximo de cinco segundos. Se o conjunto de resultados contém mais de 1000 entidades, se a consulta não foi concluída dentro de cinco segundos, ou se a consulta atravesse o limite de partição, o serviço tabela devolve um token de continuação para ativar a aplicação de cliente pedir o seguinte conjunto de entidades. Para obter mais informações sobre como continuação tokens trabalho, consulte [tempo limite de consulta e paginação](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se estiver a utilizar a biblioteca de clientes de armazenamento, pode processar de automaticamente tokens de continuação para si como devolve entidades do serviço tabela. O seguinte c# exemplo de código utilizando a biblioteca de clientes do Storage automaticamente processa tokens de continuação se o serviço tabela devolve-os numa resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

O seguinte código c# processa tokens de continuação explicitamente:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Utilizando os tokens de continuação explicitamente, pode controlar quando a aplicação obtém o segmento de dados seguinte. Por exemplo, se a aplicação de cliente permite que os utilizadores percorrer as entidades armazenadas numa tabela, um utilizador pode optar por não percorrer todas as entidades obtidas pela consulta, pelo que a aplicação apenas utilizará um token de continuação para obter o segmento seguinte quando o utilizador tinha terminado paginação através de todas as entidades o segmento atual. Esta abordagem tem várias vantagens:  

* Permite-lhe limitar a quantidade de dados para obter a partir do serviço tabela e mover através da rede.  
* Permite-lhe efetuar e/s assíncrona no .NET.  
* Permite-lhe serializar o token de continuação para o armazenamento persistente, para poder continuar em caso de uma falha de aplicação.  

> [!NOTE]
> Um token de continuação normalmente devolve um segmento de 1.000 entidades, embora possam ser menos. Este é também o caso se limitar o número de entradas de uma consulta devolve utilizando **demorar** para devolver as entidades n primeiro que correspondam aos critérios de pesquisa: o serviço de tabela pode devolver um segmento que contenham menos de entidades n juntamente com um token de continuação para permitem-lhe obter as entidades restantes.  
> 
> 

O código do c# seguinte mostra como modificar o número de entidades devolvido dentro de um segmento de:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projecção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ter até 1 MB de tamanho. Quando a tabela de consulta e obter entidades, que pode não necessitar de todas as propriedades e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Pode utilizar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo seguinte é obtém apenas o **E-Mail** propriedade (juntamente com **PartitionKey**, **RowKey**, **Timestamp**, e **ETag**) de entidades selecionadas pela consulta.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Repare como o **RowKey** valor está disponível, apesar de não estava incluído na lista de propriedades a obter.  

### <a name="modifying-entities"></a>A modificação de entidades
A biblioteca de clientes de armazenamento permite-lhe modificar as entidades armazenadas no serviço tabela por a inserir, eliminar e atualizar as entidades. Pode utilizar EGTs para vários insert, update e operações delete em conjunto para reduzir o número de ida e volta necessárias do batch e melhorar o desempenho da sua solução.  

Tenha em atenção de que exceções acionadas quando a biblioteca de clientes de armazenamento executa um EGT normalmente incluem o índice da entidade que causou o lote falhar. Isto é útil quando está a depurar código que utiliza EGTs.  

Deve também considerar como a estrutura afeta a forma como a aplicação cliente processa as operações de simultaneidade e atualização.  

#### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por predefinição, o serviço tabela implementa otimista simultaneidade verifica a nível de entidades individuais para **inserir**, **intercalar**, e **eliminar** operações, embora seja possível para um cliente forçar o serviço de tabela para ignorar estas verificações. Para obter mais informações sobre como o serviço tabela gere a simultaneidade, consulte [gerir simultaneidade no armazenamento do Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Intercalação ou substituir
O **substituir** método o **TableOperation** classe substitui sempre a entidade concluída no serviço tabela. Se não incluir uma propriedade no pedido quando essa propriedade existe na entidade armazenada, o pedido remove essa propriedade da entidade armazenada. A menos que pretenda remover uma propriedade explicitamente a partir de uma entidade armazenada, tem de incluir cada propriedade no pedido.  

Pode utilizar o **intercalar** método o **TableOperation** classe para reduzir a quantidade de dados que envie para o serviço tabela quando pretender atualizar uma entidade. O **intercalar** método substitui quaisquer propriedades na entidade armazenada com valores de propriedade de entidade incluídos no pedido, mas deixa intactos quaisquer propriedades na entidade armazenada não estão incluídas no pedido. Isto é útil se tiver entidades grandes e apenas tem de atualizar um pequeno número de propriedades de um pedido.  

> [!NOTE]
> O **substituir** e **intercalar** métodos falharem se a entidade não existe. Como alternativa, pode utilizar o **InsertOrReplace** e **InsertOrMerge** métodos que se não existir, crie uma nova entidade.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidade heterogénea
O serviço de tabela é um *sem esquema* arquivo de tabela, que significa que uma única tabela pode armazenar as entidades de vários tipos de fornecer uma grande flexibilidade na sua estrutura. O exemplo seguinte ilustra uma tabela armazenar empregado e entidades do departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Tenha em atenção que cada entidade ainda tem de ter **PartitionKey**, **RowKey**, e **Timestamp** valores, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de entidade, exceto se optar por armazenar essas informações algures. Existem duas opções para identificar o tipo de entidade:  

* Preceder o tipo de entidade para o **RowKey** (ou possivelmente o **PartitionKey**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como **RowKey** valores.  
* Utilize uma propriedade de diferente para registar o tipo de entidade, conforme mostrado na tabela abaixo.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Empregado</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Empregado</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Departamento</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome próprio</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Empregado</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

A primeira opção, prefixação a entidade de tipo para o **RowKey**, é útil se existir a possibilidade de que duas entidades de diferentes tipos de podem ter o mesmo valor de chave. Também-grupos de entidades do mesmo tipo em conjunto na partição.  

As técnicas abordadas nesta secção são especialmente relevantes para o debate [relações de herança](#inheritance-relationships) anteriormente neste guia na secção [Modelling relações](#modelling-relationships).  

> [!NOTE]
> Deve considerar, incluindo um número de versão no valor de tipo de entidade para ativar aplicações de cliente evoluir POCO objetos e trabalhar com versões diferentes.  
> 
> 

O resto esta secção descreve algumas das funcionalidades na biblioteca de clientes de armazenamento que o facilitam a trabalhar com vários tipos de entidade na mesma tabela.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Obter os tipos de entidade heterogénea
Se estiver a utilizar a biblioteca de clientes do Storage, tem três opções para trabalhar com diversos tipos de entidade.  

Se conhecer o tipo da entidade armazenado com específico **RowKey** e **PartitionKey** valores, em seguida, pode especificar o tipo de entidade ao obter a entidade, conforme mostrado nos exemplos anteriores dois que obter entidades do tipo **EmployeeEntity**: [executar uma consulta de ponto de utilizar a biblioteca de clientes de armazenamento](#executing-a-point-query-using-the-storage-client-library) e [obter várias entidades com LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção consiste em utilizar o **DynamicTableEntity** tipo (uma matriz de propriedades) em vez de um tipo de entidade POCO concreto (esta opção pode também melhorar o desempenho porque não é necessário para serializar e anular a serialização a entidade para tipos .NET). O seguinte código c# potencialmente obtém várias entidades de diferentes tipos de tabela, mas devolve todas as entidades como **DynamicTableEntity** instâncias. Em seguida, utiliza o **EntityType** propriedade para determinar o tipo de cada entidade:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Tenha em atenção que para obter outras propriedades tem de utilizar o **TryGetValue** método no **propriedades** propriedade o **DynamicTableEntity** classe.  

É uma terceira opção combinar utilizando o **DynamicTableEntity** tipo e um **EntityResolver** instância. Isto permite-lhe resolver para vários tipos POCO da mesma consulta. Neste exemplo, o **EntityResolver** delegado está a utilizar o **EntityType** propriedade para distinguir entre os dois tipos de entidade que a consulta devolve. O **resolver** método utiliza o **resolução** delegado resolver **DynamicTableEntity** instâncias para **TableEntity** instâncias.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Modificar os tipos de entidade heterogénea
Não é necessário conhecer o tipo de uma entidade eliminá-la e sempre conhecer o tipo de uma entidade ao inseri-lo. No entanto, pode utilizar **DynamicTableEntity** tipo para atualizar uma entidade sem saberem o respetivo tipo e sem utilizar uma classe de entidade POCO. O exemplo de código seguinte obtém uma única entidade e verifica a **EmployeeCount** existe uma propriedade antes de atualizá-la.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Controlar o acesso com assinaturas de acesso partilhado
Pode utilizar os tokens de assinatura de acesso partilhado (SAS) para ativar aplicações de cliente a modificar (e consultar) as entidades da tabela diretamente sem a necessidade de autenticar diretamente com o serviço de tabela. Normalmente, existem três vantagens principais em utilizar SAS na sua aplicação:  

* Não é necessário distribuir a chave de conta de armazenamento para uma plataforma inseguras (por exemplo, um dispositivo móvel) para permitir que o dispositivo aceder e modificar entidades no serviço tabela.  
* Pode descarregar parte do trabalho que executam funções web e de trabalho na gestão as entidades para dispositivos de cliente tais como computadores do utilizador final e de dispositivos móveis.  
* Pode atribuir um restrita e tempo conjunto limitado de permissões para um cliente (por exemplo, permitir o acesso só de leitura a recursos específicos).  

Para obter mais informações sobre a utilização de SAS tokens com o serviço tabela, consulte [através de acesso partilhado assinaturas (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, tem ainda de gerar os tokens SAS que conceda uma aplicação de cliente para as entidades no serviço tabela: deve fazê-lo num ambiente que tem acesso seguro para as chaves de conta de armazenamento. Normalmente, pode utilizar uma função web ou de trabalho para gerar os tokens SAS e entregá-los para as aplicações de cliente que precisam de acesso ao seu entidades. Porque não há ainda um overhead inerente ao gerar e distribuição de SAS tokens para clientes, deve considerar a melhor para reduzir esta sobrecarga, especialmente em cenários de elevado volume.  

É possível gerar um token SAS, que concede acesso a um subconjunto das entidades numa tabela. Por predefinição, cria um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS conceder acesso para qualquer um intervalo de **PartitionKey** valores ou um intervalo de **PartitionKey** e **RowKey** valores. Pode optar por utilizar gerar tokens SAS para utilizadores individuais do seu sistema, de modo a que o token SAS de cada utilizador só permite-lhes acesso para os seus próprios entidades no serviço tabela.  

### <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Fornecidas são propagando-se os pedidos entre várias partições, pode melhorar a capacidade de resposta de débito e o cliente utilizando as consultas paralelas ou assíncronas.
Por exemplo, pode ter duas ou mais trabalho instâncias de função de aceder as tabelas em paralelo. Que disponha de funções de trabalho individuais responsáveis por conjuntos de específicos de partições, ou simplesmente tem vários trabalho instâncias de função, cada capazes de aceder a todas as partições numa tabela.  

Dentro de uma instância de cliente, pode melhorar o débito ao executar operações de armazenamento de forma assíncrona. A biblioteca de clientes de armazenamento torna mais fácil escrever consultas assíncronas e modificações. Por exemplo, poderá começar com o método síncrono que obtém todas as entidades numa partição conforme mostrado no código c# seguinte:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Pode facilmente modificar este código, para que a consulta é executada de forma assíncrona, da seguinte forma:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Neste exemplo assíncrona, pode ver as seguintes alterações da versão síncrona:  

* A assinatura de método inclui agora o **async** modificador e devolve um **tarefas** instância.  
* Em vez de chamar o **ExecuteSegmented** chama o método para obter resultados, o método agora o **ExecuteSegmentedAsync** método e utiliza o **await** modificador para obter resultados de forma assíncrona.  

A aplicação cliente pode chamar este método várias vezes (com valores diferentes para o **departamento** parâmetro), e cada consulta será executado num thread separado.  

Tenha em atenção que não há nenhuma versão assíncrona do **executar** método o **TableQuery** classe porque o **IEnumerable** interface não suporta a enumeração assíncrona.  

Também pode inserir, atualizar e eliminar entidades no modo assíncrono. O exemplo do c# seguinte mostra um método simple, síncrono inserir ou substituir uma entidade de empregado:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Pode facilmente modificar este código para que a atualização seja executado de forma assíncrona, da seguinte forma:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrona, pode ver as seguintes alterações da versão síncrona:  

* A assinatura de método inclui agora o **async** modificador e devolve um **tarefas** instância.  
* Em vez de chamar o **executar** chama o método para atualizar a entidade, o método agora o **ExecuteAsync** método e utiliza o **await** modificador para obter resultados de forma assíncrona.  

A aplicação cliente pode chamar vários métodos assíncronos como este, e cada invocação do método será executado num thread separado.  

### <a name="credits"></a>Créditos
Gostaríamos de obrigado os seguintes membros da equipa do Azure para as respetivas contribuições: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Irwin Jorge, Vamshidhar Kommineni, Vinay Shah e Serdar Ozler, bem como Hollander personalizada da Microsoft DX. 

Também gostaríamos de obrigado a seguinte Microsoft MVP para os seus comentários importantes ciclos de revisão: Igor Papirov e Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

