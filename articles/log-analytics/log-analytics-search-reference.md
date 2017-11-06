---
title: "Referência de pesquisa de análise de registos do Azure | Microsoft Docs"
description: "A análise de registos de referência de pesquisa descreve o idioma de pesquisa e fornece a sintaxe de consulta geral opções pode utilizar quando procurar dados e a filtragem de expressões para ajudar a limitar a pesquisa."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 264ea071dc0b15964af07c68cbf0dee896b07a3e
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="log-analytics-search-reference"></a>Referência de pesquisa de análise de registos

>[!NOTE]
> Este artigo descreve as pesquisas de registo utilizando a linguagem de consulta legado na análise de registos.  Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, deve referir-se a [a referência de linguagem para o novo idioma](https://go.microsoft.com/fwlink/?linkid=856079).

A seguinte secção de referência sobre o idioma de pesquisa descreve as opções de sintaxe de consulta gerais que pode utilizar quando procurar dados e a filtragem de expressões para ajudar a limitar a pesquisa. Também descreve os comandos que pode utilizar para executar ações nos dados obtidos.

Pode ler sobre os campos devolvidos na procura e as facetas que o ajudar a descobrir mais informações sobre categorias semelhantes de dados, no [secção de referência de campo de procura e aspeto](#search-field-and-facet-reference).

## <a name="general-query-syntax"></a>Sintaxe de consulta geral
A sintaxe para consultar geral é o seguinte:

```
filterExpression | command1 | command2 …
```

A expressão de filtro (`filterExpression`) define a condição "where" para a consulta. Os comandos aplicam-se com os resultados devolvidos pela consulta. Vários comandos tem de ser separados pelo caráter de pipe (|).

### <a name="general-syntax-examples"></a>Exemplos de sintaxe geral
Exemplos:

```
system
```

Esta consulta devolve resultados que contêm o word *sistema* qualquer campo que tenha sido indexado para o texto completo ou de termos de pesquisa.

> [!NOTE]
> Nem todos os campos são indexados desta forma, mas as mais comuns textual campos (por exemplo, nomes e descrições), normalmente, são.
>
>

```
system error
```

Esta consulta devolve resultados que contêm as palavras *sistema* e *erro*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Esta consulta devolve resultados que contêm as palavras *sistema* e *erro*. Em seguida, ordena os resultados pelo *ManagementGroupName* campo (por ordem ascendente) e, em seguida, o *TimeGenerated* campo (por ordem descendente). Demora apenas os primeiras 10 resultados.

> [!IMPORTANT]
> Todos os nomes de campo e os valores para os campos de cadeia e o texto são sensíveis a maiúsculas e minúsculas.
>
>

## <a name="filter-expressions"></a>Expressões de filtro
As subsecções seguintes explicam as expressões de filtro.

### <a name="string-literals"></a>Literais de cadeia
Um literal de cadeia é qualquer cadeia que não é reconhecida pelo parser como uma palavra-chave ou um tipo de dados predefinida (por exemplo, um número ou uma data).

Exemplos:

```
These all are string literals
```

Esta consulta pesquisa para obter os resultados que contêm as ocorrências de todas as cinco palavras. Para efetuar uma pesquisa de cadeia complexa, coloque a cadeia literal aspas. Por exemplo:

```
"Windows Server"
```

Esta ação devolve apenas os resultados com exatos correspondências para *do Windows Server*.

### <a name="numbers"></a>Números
O analisador suporta o número inteiro decimal e a sintaxe de número de vírgula flutuante de campos numéricos.

Exemplos:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="dates-and-times"></a>As datas e horas
Cada conjunto de dados no sistema tem um *TimeGenerated* propriedade, o que representa a data e hora do registo original. Alguns tipos de dados podem ter campos de hora e data adicional (por exemplo, *LastModified*).

A linha cronológica **gráfico/hora** Seletor no Log Analytics do Azure mostra a distribuição de resultados ao longo do tempo (em conformidade com a consulta atual a ser executada). Isto baseia-se no *TimeGenerated* campo. Campos de data e hora tem um formato de cadeia específica que pode ser utilizado nas consultas para restringir a consulta para um determinado período de tempo. Também pode utilizar a sintaxe para fazer referência aos intervalos de tempo relativo (por exemplo, "entre há 3 dias e horas 2 há").

Seguem-se válido formulários da sintaxe para as datas e horas:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Por exemplo:

```
TimeGenerated:2013-10-01T12:20
```

O comando anterior devolve apenas os registos com um *TimeGenerated* valor exatamente 12:20 em 1 de Outubro de 2013.

O analisador também suporta o valor de data/hora mnemónica, agora. (Não é provável que este irá produzir resultados, porque os dados não torná-lo através do sistema que rápido.)

Estes exemplos são os blocos modulares a utilizar para datas relativas e absolutas. As três subsecções, verá como utilizá-los em filtros mais avançados, com exemplos que utilizam os intervalos de data relativo.

### <a name="datetime-math"></a>Contas de data/hora
Utilize os operadores de contas de data/hora para o deslocamento ou arredondar o valor de data/hora, utilizando os cálculos de data/hora simples.

Sintaxe:

```
datetime/unit
```

```
datetime[+|-]count unit
```

| operador | Descrição |
| --- | --- |
| / |Arredonda por data/hora para a unidade especificada. Por exemplo, agora / dia arredonda a data/hora atual meia-noite do dia atual. |
| + ou - |Desvia os data/hora pelo número especificado de unidades. Por exemplo, agora + 1 hora desvia os a data/hora atual por diretamente uma hora. 2013-10-01T12:00-10 dias desvia os o valor de data Retroceder por 10 dias. |

Pode encadear os operadores de contas de data/hora em conjunto. Por exemplo:

```
NOW+1HOUR-10MONTHS/MINUTE
```

A tabela seguinte lista as unidades de data/hora suportadas.

| Unidade de data/hora | Descrição |
| --- | --- |
| ANO, ANOS |Arredonda para o ano atual, ou desvios pelo número especificado de anos. |
| MÊS, MESES |Arredonda para o mês atual, ou desvios pelo número de meses especificado. |
| DIA, DIAS, DATA |Arredonda por excesso para o dia atual do mês ou desvia os pelo número especificado de dias. |
| HORAS DE UMA HORA |Arredonda para a hora atual, ou desvios pelo número especificado de horas. |
| MINUTO, MINUTOS |Arredonda para o minuto atual, ou desvios pelo número de minutos especificado. |
| SEGUNDO, SEGUNDOS |Arredonda por segundo para atual ou desvia os por número de segundos especificado. |
| MILISSEGUNDO, EM MILISSEGUNDOS, MILLI, MILLIS |Arredonda milissegundo atual ou desvios pelo número especificado de milissegundos. |

### <a name="field-facets"></a>Facetas do campo
Ao utilizar as facetas de campo, pode especificar a condição de pesquisa de campos específicos e os respetivos valores exatos. Este padrão difere dos escrever consultas "texto livre" para vários termos em todo o índice. Já ter visto esta técnica em vários exemplos anteriores. Seguem-se exemplos mais complexos.

**Sintaxe**

```
field:value
```

```
field=value
```

**Descrição**

Procura o campo para o valor específico. O valor pode ser um literal de cadeia, número, ou data e hora.

Por exemplo:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Sintaxe**

*campo > valor*

*campo < valor*

*campo de > = valor*

*campo < = valor*

*campo! = valor*

**Descrição**

Fornece comparações.

Por exemplo:

```
TimeGenerated>NOW+2HOURS
```

**Sintaxe**

```
field:[from..to]
```

**Descrição**

Fornece facetamento do intervalo.

Por exemplo:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="in"></a>EM
O **IN** palavra-chave permite-lhe selecionar numa lista de valores. Consoante a sintaxe que utiliza, isto pode ser uma lista de valores que fornece simple ou uma lista de valores de uma agregação.

Sintaxe 1:

```
field IN {value1,value2,value3,...}
```

Esta sintaxe permite-lhe incluir todos os valores numa lista simple.



Exemplos:

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

Sintaxe 2:

```
(Outer query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Esta sintaxe permite-lhe criar uma agregação. Em seguida, pode feed da lista de valores de que a agregação para outra pesquisa (principal) externa que procura para eventos com essas valor. Pode fazê-lo ao envolvente a pesquisa interna chavetas e feeding dos resultados como os valores possíveis para um campo na pesquisa externa ao utilizar o operador IN.

Exemplo de consulta interna: *computadores atualmente em falta atualizações de segurança* com a seguinte consulta de agregação:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

A consulta final que localiza *todos os eventos do Windows para computadores que atualmente em falta atualizações de segurança* é semelhante ao seguinte:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="contains"></a>Contains
O **contém** palavra-chave permite-lhe filtrar registos com um campo que contém uma cadeia especificada. Isto é sensível a maiúsculas e minúsculas, funciona apenas com campos de cadeia e não pode incluir quaisquer carateres de escape.

Sintaxe:

```
field:contains("string")
```

Exemplo:

```
Type:contains("Event")
```

Esta ação devolve registos com um tipo que contém a cadeia "Event". Os exemplos incluem **eventos**, **SecurityEvent**, e **ServiceFabricOperationEvent**.



### <a name="regular-expressions"></a>Expressões regulares
Pode especificar uma condição de pesquisa de um campo com uma expressão regular, utilizando o **Regex** palavra-chave. Para obter uma descrição completa da sintaxe que pode utilizar expressões regulares, consulte [utilizando expressões regulares para filtrar a pesquisa de registo na análise de registos](log-analytics-log-searches-regex.md).

Sintaxe:

```
field:Regex("Regular Expression")
```

Exemplo:

```
Computer:Regex("^C.*")
```

### <a name="logical-operators"></a>Operadores lógicos
Os idiomas de consulta suportam os operadores lógicos (*e*, *ou*, e *não*) e os aliases de estilo C (*&&*,  *||* , e *!*, respetivamente). Pode utilizar parênteses para agrupar destes operadores.

Exemplos:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

Pode omitir o operador lógico para os argumentos de filtro de nível superior. Neste caso, é assumido o operador AND.

| Expressão de filtro | Equivalente ao |
| --- | --- |
| Erro de sistema |sistema e erro |
| sistema "Windows Server" ou gravidade: 1 |sistema e ("Windows Server" ou gravidade: 1) |

### <a name="wildcarding"></a>Wildcarding
O idioma de consulta suporta a utilização da ( \* ) para representar um ou mais carateres para um valor numa consulta.

Exemplo:

 Localize todos os computadores com o "SQL" no nome, por exemplo, "Redmond-SQL".

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> Neste momento, não não possível utilizar carateres universais quotations. Por exemplo, a mensagem `"*This text*"` considera que a (\*) utilizado como um literal (\*) caráter.


## <a name="commands"></a>Comandos


Os comandos aplicam-se com os resultados são devolvidos pela consulta. Utilize o caráter de pipe (|) para aplicar um comando para os resultados obtidos. Vários comandos tem de ser separados pelo caráter de pipe.

> [!NOTE]
> Nomes de comandos podem ser escritos em maiúsculas ou minúsculas, ao contrário dos nomes de campo e os dados.
>
>

### <a name="sort"></a>Ordenar
Sintaxe:

    sort field1 asc|desc, field2 asc|desc, …

Ordena os resultados por campos específicos. O sufixo de asc/desc para ordenar os resultados na ordem ascendente ou descendente é opcional. Se for omitido, o *asc* é assumida a sequência de ordenação. Para o **TimeGenerated** campo, *desc* ordenação pressupõe-se, pelo que devolve os resultados mais recentes primeiro por predefinição.

### <a name="toplimit"></a>Parte superior/limite
Sintaxe:

    top number


    limit number
Limita a resposta para os resultados de principais N.

Exemplo:

    Type:Alert errors detected | top 10

Devolve os 10 resultados de correspondência superiores.

### <a name="skip"></a>Ignorar
Sintaxe:

    skip number

Ignora o número de resultados listados.

Exemplo:

    Type:Alert errors detected | top 10 | skip 200

Devolve resultados correspondentes superiores 10 começando resultado 200.

### <a name="select"></a>Selecione
Sintaxe:

    select field1, field2, ...

Limita os resultados para os campos que escolher.

Exemplo:

    Type:Alert errors detected | select Name, Severity

Limita os campos de resultados devolvidos para *nome* e *gravidade*.

### <a name="measure"></a>medidas
O *medidas* comando é utilizado para aplicar funções estatísticas para os resultados da pesquisa não processados. Isto é muito útil para obter *Agrupar por* vistas sobre os dados. Quando utiliza o comando de medidas, análise de registos de pesquisa mostra uma tabela com resultados agregadas.

**Sintaxe:**

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Agrega os resultados por *groupField*e calcula os valores de medida agregados utilizando *aggregatedField*.

| Função de análises de medida | Descrição |
| --- | --- |
| *aggregateFunction* |O nome da função de agregação (sensível a maiúsculas e minúsculas). São suportadas as seguintes funções de agregação: CONTAGEM, MAX, MIN, soma, média, STDDEV, COUNTDISTINCT, percentil # # ou PCT # # (# # é qualquer número entre 1 e 99). |
| *aggregatedField* |O campo que está a ser agregado. Este campo é opcional para a função de agregação COUNT, mas tem de ser um campo numérico existente para a soma, MAX, MIN, AVG, STDDEV, percentil # # ou PCT # # (# # é qualquer número entre 1 e 99). O aggregatedField também pode ser qualquer uma do **expanda** funções suportadas. |
| *fieldAlias* |O alias (opcional) para o valor calculado agregado. Se não for especificado, o nome do campo é **AggregatedValue**. |
| *groupField* |O nome do campo que o resultado definido é agrupado por. |
| *Intervalo* |O intervalo de tempo, no formato:**nnnNAME**. **nnn**é o número de número inteiro positivo. **NOME** é o nome de intervalo. Intervalo suportado nomes são sensíveis a maiúsculas e minúsculas e incluem: MILISSEGUNDO [S]. o segundo [S] minuto [S] hora [S] dia [S], [S] do mês e ano [S]. |

A opção de intervalo só pode ser utilizada em campos de grupo de data/hora (tais como *TimeGenerated* e *TimeCreated*). Atualmente, isto não é imposto pelo serviço, mas um campo sem data/hora é passado para o back-end causa um erro de tempo de execução. Quando a validação de esquema é implementada, a API do serviço rejeita consultas que utilizam campos sem data/hora para agregação de intervalo. Atual *medidas* implementação suporta agrupamento do intervalo para qualquer função de agregação.

Se a cláusula BY for omitida, mas foi especificado um intervalo (como uma sintaxe de segundo), o *TimeGenerated* campo é assumido por predefinição.

Exemplos:

**Exemplo 1**

    Type:Alert | measure count() as Count by ObjectId

Agrupa alertas por *ObjectID*e calcula o número de alertas para cada grupo. O valor agregado é devolvido como o *contagem* campo (alias).

**Exemplo 2**

    Type:Alert | measure count() interval 1HOUR

Agrupa os alertas por intervalos de 1 hora, utilizando o *TimeGenerated* campo e devolve o número de alertas em cada intervalo.

**Exemplo 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

Mesmo do exemplo anterior, mas com um alias de campo agregados (*AlertsPerHour*).

**Exemplo 4**

    * | medida existente pelo TimeCreated intervalo 5DAYS

Agrupa os resultados por intervalos de 5 dias, utilizando o *TimeCreated* campo e devolve o número de resultados em cada intervalo.

**Exemplo 5**

    Type:Alert | measure max(Severity) by WorkflowName

Agrupa os alertas por nome de carga de trabalho e devolve o valor máximo gravidade do alerta para cada fluxo de trabalho.

**Exemplo 6**

    Type:Alert | measure min(Severity) by WorkflowName

Mesmo do exemplo anterior, mas com o *min* agregado função.

**Exemplo 7**

    Type:Perf | measure avg(CounterValue) by Computer

Grupos de desempenho por computador e calcula a média (média).

**8 de exemplo**

    Type:Perf | measure sum(CounterValue) by Computer

Mesmo que o exemplo anterior, mas utiliza *soma*.

**Exemplo 9**

    Type:Perf | measure stddev(CounterValue) by Computer

Mesmo que o exemplo anterior, mas utiliza *stddev*.

**Exemplo 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

Mesmo que o exemplo anterior, mas utiliza *percentile70*.

**Exemplo 11**

    Type:Perf | measure pct70(CounterValue) by Computer

Mesmo que o exemplo anterior, mas utiliza *pct70*. Tenha em atenção que *PCT # #* é apenas um alias para *percentil # #* função.

**Exemplo 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

Grupos de desempenho primeiro por computador e, em seguida, CounterName e calcula a média (média).

**Exemplo 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

Obtém os fluxos de trabalho de cinco das principais com o número máximo de alertas.

**14 de exemplo**

    * | medidas countdistinct(Computer) por tipo

Conta o número de computadores exclusivos para cada tipo de relatório.

**15 de exemplo**

    * | medidas countdistinct(Computer) intervalo de 1 hora

Conta o número de computadores exclusivos Reporting Services para cada hora.

**Exemplo 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

Grupos de % de tempo de processador por computador e devolve a média para cada hora.

**Exemplo 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

Agrupa W3CIISLog pelo método e devolve o número máximo de cada 5 minutos.

**Exemplo 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

Grupos de % de tempo de processador por computador e devolve o mínimo, média, 75th percentil e máximo para cada hora.

**Exemplo 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

% De tempo do processador primeiro por computador e, em seguida, nome de instância de grupos e devolve o mínimo, média, 75th percentil e máximo para cada hora.

**Exemplo de 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

Calcula o número máximo de escritas em disco por minuto para cada disco no seu computador.

### <a name="where"></a>onde
Sintaxe:

```
**where** AggregatedValue>20
```

Só pode ser utilizada após um *medidas* resulta de comando para continuar a filtrar o agregado que o *medidas* numa função de agregação tem produzidos.

Exemplos:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)



### <a name="dedup"></a>Eliminação de duplicados
Sintaxe:

    Dedup FieldName

Devolve o primeiro documento encontrado para cada valor exclusivo do campo especificado.

Exemplo:

    Type=Event | Dedup EventID | sort TimeGenerated DESC

Neste exemplo devolve um evento (evento mais recente) por EventID.

### <a name="join"></a>Associar
Associa os resultados das duas consultas para formar um conjunto de resultado único.  Suporta vários tipos de associações descritos na tabela a seguir.

| Tipo de associação | Descrição |
|:--|:--|
| interna | Devolva apenas os registos com um valor de correspondência em ambas as consultas. |
| externa | Devolva todos os registos de ambas as consultas.  |
| À esquerda  | Devolva todos os registos da consulta à esquerda e registos correspondentes da consulta à direita. |


- Associações atualmente não suportam consultas que incluem o **IN** palavra-chave, o **medidas** comando ou o **expanda** comando se-destina-se um campo da consulta à direita.
- Pode incluir atualmente apenas um único campo numa associação.
- Uma única pesquisa não pode incluir mais do que uma associação.

**Sintaxe**

```
<left-query> | JOIN <join-type> <left-query-field-name> (<right-query>) <right-query-field-name>
```

**Exemplos**

Para ilustrar os tipos de associação diferentes, considere a associar a um tipo de dados recolhidos a partir de um registo personalizado denominado MyBackup_CL com o heartbeat para cada computador.  Estes tipos de dados tem os seguintes dados.

`Type = MyBackup_CL`

| TimeGenerated | Computador | LastBackupStatus |
|:---|:---|:---|
| 20/4/2017 01:26:32.137 AM | Srv01.contoso.com | Êxito |
| 20/4/2017 02:13:12.381 AM | SRV02.contoso.com | Êxito |
| 20/4/2017 02:13:12.381 AM | srv03.contoso.com | Falha |

`Type = Hearbeat`(Apenas um subconjunto de campos a apresentar)

| TimeGenerated | Computador | ComputerIP |
|:---|:---|:---|
| 21/4/2017 12:01:34.482 PM | Srv01.contoso.com | 10.10.100.1 |
| 21/4/2017 12:02:21.916 PM | SRV02.contoso.com | 10.10.100.2 |
| 21/4/2017 12:01:47.373 PM | srv04.contoso.com | 10.10.100.4 |

#### <a name="inner-join"></a>associação interna

`Type=MyBackup_CL | join inner Computer (Type=Heartbeat) Computer`

Devolve os seguintes registos em que o campo do computador corresponde para ambos os tipos de dados.

| Computador| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 20/4/2017 01:26:32.137 AM | Êxito | 21/4/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| SRV02.contoso.com | 20/4/2017 02:13:12.381 AM | Êxito | 21/4/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |


#### <a name="outer-join"></a>associação externa

`Type=MyBackup_CL | join outer Computer (Type=Heartbeat) Computer`

Devolve os seguintes registos para ambos os tipos de dados.

| Computador| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 20/4/2017 01:26:32.137 AM | Êxito  | 21/4/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| SRV02.contoso.com | 20/4/2017 02:14:12.381 AM | Êxito  | 21/4/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |
| srv03.contoso.com | 20/4/2017 01:33:35.974 AM | Falha  | 21/4/2017 12:01:47.373 PM | | |
| srv04.contoso.com |                           |          | 21/4/2017 12:01:47.373 PM | 10.10.100.2 | Heartbeat |



#### <a name="left-join"></a>associação à esquerda

`Type=MyBackup_CL | join left Computer (Type=Heartbeat) Computer`

Devolve os seguintes registos de MyBackup_CL com quaisquer campos correspondentes de Heartbeat.

| Computador| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 20/4/2017 01:26:32.137 AM | Êxito | 21/4/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| SRV02.contoso.com | 20/4/2017 02:13:12.381 AM | Êxito | 21/4/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |
| srv03.contoso.com | 20/4/2017 02:13:12.381 AM | Falha | | | |


### <a name="extend"></a>Expansão
Permite-lhe criar campos de tempo de execução nas consultas. Tenha em atenção que os campos de tempo de execução não podem ser utilizados com o comando de medida para executar a agregação.

**Exemplo 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Mostra ponderado pontuação de recomendação para recomendações de avaliação do SQL Server.

**Exemplo 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Mostra o valor do contador num KBs em vez de bytes.

**Exemplo 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Ajusta o valor de WireData TotalBytes, de forma a que todos os resultados são entre 0 e 100.

**Exemplo 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
Os valores do contador de desempenho inferior a 50 por cento como baixa e outros utilizadores como elevado de etiquetas.

**Exemplo 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
Calcula o número máximo de escritas em disco por minuto para cada disco no seu computador.

**Funções suportadas**

| Função | Descrição | Exemplos de sintaxe |
| --- | --- | --- |
| Abs |Devolve o valor absoluto de função ou o valor especificado. |`abs(x)` <br> `abs(-5)` |
| ACOS |Devolve o arco de co-seno de uma função ou um valor. |`acos(x)` |
| e |Devolve um valor true se e apenas se todos os operandos avaliadas como verdadeiras. |`and(not(exists(popularity)),exists(price))` |
| asin |Devolve o arco de seno de uma função ou um valor. |`asin(x)` |
| ATAN |Devolve o arco de tangente de uma função ou um valor. |`atan(x)` |
| ATAN2 |Devolve o ângulo resultantes de conversão das coordenadas de retangular x, y para coordenadas polar. |`atan2(x,y)` |
| cbrt |Raiz de cubo. |`cbrt(x)` |
| ceil |Arredonda por excesso para um número inteiro. |`ceil(x)`  <br> `ceil(5.6)`Devolve 6 |
| cos |Devolve co-seno de um ângulo. |`cos(x)` |
| COSH |Devolve o co-seno hiperbólico de um ângulo. |`cosh(x)` |
| def |Abreviatura de predefinição. Devolve o valor do campo "campo". Se o campo não existe, devolve o valor predefinido especificado e gera o primeiro valor onde: `exists()==true`. |`def(rating,5)`. Esta função def() devolve a classificação ou não se for especificada nenhuma classificação no documento, devolve 5. <br> `def(myfield, 1.0)`é equivalente ao `if(exists(myfield),myfield,1.0)`. |
| deg |Converte radianos graus. |`deg(x)` |
| Div |`div(x,y)`Divide x, y. |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| DIST |Devolve a distância entre dois vetores de, (pontos) num espaço de n dimensional. Aceita o energia, além de duas ou mais, instâncias de ValueSource e calcula as distâncias entre os vetores de dois. Cada ValueSource tem de ser um número. Tem de ser um número par de instâncias de ValueSource transmitido e o método assume que o primeiro meio representam o vetor de primeira e segunda metade representam o vetor de segundo. |`dist(2, x, y, 0, 0)`Calcula a distância Euclidean entre (0,0) e (x, y) para cada documento. <br> `dist(1, x, y, 0, 0)`Calcula a distância Manhattan (taxicab) entre (0,0) e (x, y) para cada documento. <br> `dist(2,,x,y,z,0,0,0)`Euclidean distância entre (0,0,0) e (x, y, z) para cada documento.<br>`dist(1,x,y,z,e,f,g)`Distância Manhattan entre (x, y, z) e (i, f, g), onde cada letra é um nome de campo. |
| existe |Membro TRUE se qualquer devolve o campo existe. |`exists(author)`Devolve TRUE para qualquer documento que tem um valor no campo "autor".<br>`exists(query(price:5.00))`Devolve TRUE se corresponder a "preço", "5.00". |
| EXP |Devolve o número de Euler elevado à potência x. |`exp(x)` |
| piso |Arredonda para baixo para um número inteiro. |`floor(x)`  <br> `floor(5.6)`Devolve 5 |
| hypo |Devolve sqrt(sum(pow(x,2),pow(y,2))) sem intermédia capacidade excedida ou capacidade insuficiente. |`hypo(x,y)`  <br> ` |
| Se |Permite consultas de função condicional. No `if(test,value1,value2)`, teste ou referir-se a um valor lógico ou expressão que devolve um valor lógico (TRUE ou FALSE). `value1`é o valor devolvido pela função se teste gera TRUE. `value2`é o valor devolvido pela função se teste gera FALSE. Uma expressão pode ser qualquer função que produz os valores booleanos. Também pode ser uma função devolver valores numéricos, na qual maiúsculas valor 0 é interpretado como false ou devolver cadeias, no qual uma cadeia vazia caso é interpretado como false. |`if(termfreq(cat,'electronics'),popularity,42)`Esta função verifica cada documento para ver se contém o termo "electronics" no campo cat. Se existir, em seguida, é devolvido o valor do campo popularidade. Caso contrário, é devolvido o valor de 42. |
| linear |Implementa `m*x+c`, onde m e c são constantes e x é uma função arbitrária. Isto é equivalente ao `sum(product(m,x),c)`, mas ligeiramente mais eficiente como está implementado como uma única função. |`linear(x,m,c) linear(x,2,4)`Devolve`2*x+4` |
| ln |Devolve o registo natural da função especificada. |`ln(x)` |
| Registo |Devolve o registo de base 10 da função especificada. |`log(x)   log(sum(x,100))` |
| Mapa |Mapeia os valores de uma função de entrada x que enquadra-se nas min e max, inclusive para o destino especificado. O argumentos min e max têm de ser constantes. O destino de argumentos e predefinido podem ser constantes ou funções. Se o valor de x não se encontra entre min e max, em seguida, é devolvido o valor x, ou um valor predefinido é devolvido se especificado como um argumento 5th. |`map(x,min,max,target) map(x,0,0,1)`Altera os valores de 0 a 1. Isto pode ser útil no processamento de valores predefinidos 0.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`Altera os valores entre 0 e 100 como 1 e todos os outros valores como -1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`Alterações quaisquer valores entre 0 e 100 x + 599 e todos os outros valores de frequência do período da solr no texto do campo. |
| Máx. |Devolve o valor numérico máximo de várias funções aninhadas ou constantes, que são especificadas como argumentos: `max(x,y,...)`. A função max também pode ser úteis para "bottoming out" outra função ou campo alguns especificado constante.  Utilize o `field(myfield,max)` sintaxe para selecionar o valor máximo de um único campo com múltiplos valores. |`max(myfield,myotherfield,0)` |
| min. |Devolve o valor numérico mínimo de várias funções aninhadas de constantes, que são especificadas como argumentos: `min(x,y,...)`. Também pode ser útil para fornecer um "limite superior" uma função de uma constante a utilizar a função min. Utilize o `field(myfield,min)` sintaxe para selecionar o valor mínimo de um único campo com múltiplos valores. |`min(myfield,myotherfield,0)` |
| MOD |Calcula o módulo da função x pelo y função. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| MS |Devolve milissegundos de diferença entre os argumentos. Datas são relativamente ao Unix ou POSIX época de tempo, meia-noite, 1 de Janeiro de 1970 UTC. Argumentos podem ser o nome de um TrieDateField indexada, ou contas de data com base numa data constante ou agora. `ms()`é equivalente ao `ms(NOW)`, número de milissegundos desde a época. `ms(a)`Devolve o número de milissegundos desde a época que representa o argumento. `ms(a,b)`Devolve o número de milissegundos que b ocorre antes de a, que é `a - b`. |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| não |O valor logicamente negated da função encapsulada. |`not(exists(author))`VERDADEIRO se `exists(author)` é falso. |
| ou |Uma disjunção lógica. |`or(value1,value2)`TRUE se qualquer um dos value1 ou value2 é verdadeiro. |
| Pow |Gera a base especificada à potência especificada. `pow(x,y)`gera um x à potência de y. |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`Igual sqrt. |
| Produto |Devolve o produto de vários valores ou funções, que são especificadas numa lista separada por vírgulas. `mul(...)`Pode também ser utilizado como um alias para esta função. |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |Executa uma função reciprocal com `recip(x,m,a,b)` implementar `a/(m*x+b)`, onde m, a, b são constantes e x é qualquer função arbitrariamente complexa. Quando um e b são igual e x > = 0, esta função tem um valor máximo de 1 ignora como x aumenta. Aumentar o valor de uma e b resultados em conjunto num movimento da função toda a flatter parte a curva. Estas propriedades podem tornar isto uma função ideal para os aumentos documentos mais recentes quando x é `rord(datefield)`. |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |Converte graus radianos. |`rad(x)` |
| rint |Arredonda para o número inteiro mais próximo. |`rint(x)`  <br> `rint(5.6)`Devolve 6 |
| único |Seno devolve de um ângulo. |`sin(x)` |
| sinh |Devolve o seno hiperbólico de um ângulo. |`sinh(x)` |
| Escala |Dimensiona os valores da função x, de forma a que se enquadram-se entre o minTarget especificado e maxTarget, inclusive. A implementação atual atravessa todos os valores de função para obter o min e max, pelo que pode escolher o dimensionamento correto. A implementação atual não é possível distinguir quando documentos tem sido eliminados, ou documentos que não tenham nenhum valor. Utiliza 0.0 valores para estes casos. Isto significa que se os valores são normalmente todos os maiores do que 0,0, um pode ainda acaba por ficar com 0,0 como o valor mínimo para mapear. Nestes casos, um adequado `map()` função podia ser utilizada como uma solução para alterar 0,0 para um valor no intervalo real, conforme mostrado aqui:`scale(map(x,0,0,5),1,2)` |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`Dimensiona os valores x, de forma a que todos os valores são entre 1 e 2 inclusive. |
| sqrt |Devolve a raiz quadrada de função ou o valor especificado. |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |Calcula a distância entre duas cadeias. Utiliza a interface de StringDistance do Verificador de ortográfica Lucene e suporta todas as implementações disponíveis no pacote. Também permite que aplicações de plug-in as seus próprios, através de recursos do Solr capacidades de carregamento. strdist demora `(string1, string2, distance measure)`. Os valores possíveis para a medida de distância são:<ul><li>jw: Jaro Winkler</li><li>Editar: distância Levenstein ou editar</li><li>ngram: NGramDistance o, se for especificado, pode, opcionalmente, passar o tamanho de ngram demasiado. Predefinição é 2.</li><li>FQN: Totalmente qualificado classe nome para uma implementação da StringDistance interface. Tem de ter um construtor não arg.</li></ul> |`strdist("SOLR",id,edit)` |
| sub |Devolve o x-y de `sub(x,y)`. |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| soma |Devolve a soma de vários valores ou funções, que são especificadas numa lista separada por vírgulas. `add(...)`pode ser utilizado como um alias para esta função. |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |Devolve o número de vezes que o termo aparece no campo para esse documento. |termfreq(text,'memory') |
| tan |Tangente devolve de um ângulo. |`tan(x)` |
| TANH |Devolve a tangente hiperbólica de um ângulo. |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>Referência de campo e aspeto de restrições de pesquisa
Quando utilizar o registo de pesquisa para localizar dados, os resultados apresentam vários campo e facetas. Algumas das informações podem não aparecer muito descritivas. Utilize as seguintes informações para ajudar a compreender os resultados.

| Campo | Tipo de pesquisa | Descrição |
| --- | --- | --- |
| TenantId |Todos |Utilizado para dados de partição. |
| TimeGenerated |Todos |Utilizado para a linha cronológica, timeselectors (na pesquisa e outros ecrãs) da unidade. Representa quando o conjunto de dados foi gerado (normalmente no agente). A hora é expresso em formato ISO e é sempre UTC. No caso de tipos que se baseiam em instrumentação existente (ou seja, eventos num registo), isto é normalmente o em tempo real que a entrada/linha/registo foi registado. Para alguns dos outros tipos de que são produzidos através de pacotes de gestão ou na nuvem (por exemplo, as recomendações ou alertas), o tempo representa algo diferente. Este é o tempo quando este novo conjunto de dados com um instantâneo de uma configuração de algumas ordenação foi recolhido, ou um recomendação/alerta foi produzido com base no mesmo. |
| EventID |Evento |EventID no registo de eventos do Windows. |
| Registo de eventos |Evento |Registo de eventos em que o evento foi registado pelo Windows. |
| EventLevelName |Evento |Crítico/aviso/informações/com êxito |
| eventLevel |Evento |Valor numérico críticos/aviso/informações/com êxito (utilize EventLevelName em vez disso, para consultas mais fácil/mais legíveis). |
| SourceSystem |Todos |Onde vêm os dados (em termos de anexar modo para o serviço). Os exemplos incluem o Microsoft System Center Operations Manager e o armazenamento do Azure. |
| ObjectName |PerfHourly |Nome de objeto de desempenho do Windows. |
| InstanceName |PerfHourly |Nome de instância do contador de desempenho do Windows. |
| CounteName |PerfHourly |Nome de contador de desempenho do Windows. |
| ObjectDisplayName |PerfHourly, ConfigurationObjectProperty ConfigurationAlert, ConfigurationObject, |Nome a apresentar do objeto visado por uma regra de recolha de desempenho no Operations Manager. Também pode ser o nome a apresentar do objecto detetado pelas informações operacionais, ou em relação a que o alerta foi gerado. |
| RootObjectName |PerfHourly, ConfigurationObjectProperty ConfigurationAlert, ConfigurationObject, |Nome a apresentar do principal do elemento principal (numa relação de alojamento aspas) do objeto visado por uma regra de recolha de desempenho no Operations Manager. Também pode ser o nome a apresentar do objecto detetado pelas informações operacionais, ou em relação a que o alerta foi gerado. |
| Computador |A maioria dos tipos |Nome do computador que pertence os dados. |
| DeviceName |ProtectionStatus |Nome do computador os dados pertence a (igual ao "Computador"). |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |Classificação de estado de ameaça é uma representação numérica do Estado de ameaça. A classificação semelhante para códigos de resposta HTTP, tem em falta irregulares entre os números (que é a razão pela qual não ameaças é 150 e não 100 ou 0), deixando espaço para adicionar os novos Estados. Para um rollup do Estado de ameaças e o estado de proteção, a intenção é para mostrar o pior estado de que o computador está a ser durante o período de tempo selecionado. Os números de classificar que Estados diferentes, pelo que pode procurar o registo com o número mais elevado. |
| ThreatStatus |ProtectionStatus |Descrição do ThreatStatus, mapeia 1:1, com ThreatStatusRank. |
| TypeofProtection |ProtectionStatus |Produtos de antimalware, que é detetado no computador: none, a ferramenta de remoção de software maligno da Microsoft, Forefront e assim sucessivamente. |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus, RequiredUpdate |ID de funcionamento do agente neste computador. |
| HealthServiceId |A maioria dos tipos |ID de funcionamento do agente neste computador. |
| ManagementGroupName |A maioria dos tipos |Nome do grupo de gestão de agentes de ligação do Operations Manager. Caso contrário, é nulo/vazio. |
| objectType |ConfigurationObject |Escreva (tal como no. o tipo/class do pacote de gestão do Operations Manager) para este objeto, detetado através da avaliação de configuração de análise de registos. |
| UpdateTitle |RequiredUpdate |Nome da atualização que foi encontrado não instalado. |
| PublishDate |RequiredUpdate |Quando a atualização foi publicada no Microsoft Update. |
| Servidor |RequiredUpdate |Nome do computador os dados pertence a (igual ao "Computador"). |
| Produto |RequiredUpdate |Produto que se aplica a atualização. |
| UpdateClassification |RequiredUpdate |Tipo de atualização (por exemplo, o update rollup ou service pack). |
| KBID |RequiredUpdate |ID do artigo que descreve esta melhor prática ou atualização. |
| WorkflowName |ConfigurationAlert |Nome da regra ou monitor que provocou o alerta. |
| Gravidade |ConfigurationAlert |Gravidade do alerta. |
| Prioridade |ConfigurationAlert |Prioridade do alerta. |
| IsMonitorAlert |ConfigurationAlert |Este alerta é gerado por um monitor (true) ou uma regra (false)? |
| AlertParameters |ConfigurationAlert |XML com os parâmetros do alerta de análise de registos. |
| Contexto |ConfigurationAlert |XML com o contexto do alerta de análise de registos. |
| Carga de trabalho |ConfigurationAlert |Carga de trabalho que o alerta se refere ao ou tecnologia. |
| AdvisorWorkload |Recomendação |Carga de trabalho que se refira a recomendação para ou tecnologia. |
| Descrição |ConfigurationAlert |Descrição do alerta (abreviado). |
| DaysSinceLastUpdate |UpdateAgent |Quantos dias há (relativo ao TimeGenerated deste registo) este agente instalar qualquer atualização do Windows Server Update Service (WSUS) ou Microsoft Update? |
| DaysSinceLastUpdateBucket |UpdateAgent |Com base no DaysSinceLastUpdate, uma categorização nos registos de hora de há quanto tempo um computador instalado pela última vez qualquer atualização do WSUS/Microsoft Update. |
| AutomaticUpdateEnabled |UpdateAgent |Está a verificar a atualização automática ativada ou desativada neste agente? |
| AutomaticUpdateValue |UpdateAgent |A atualização automática está a verificar o conjunto para transferir automaticamente e instalar, apenas transferir ou verificar apenas? |
| WindowsUpdateAgentVersion |UpdateAgent |Número de versão do agente do Microsoft Update. |
| WSUSServer |UpdateAgent |O servidor WSUS se destina este agente de atualização |
| OSVersion |UpdateAgent |Versão do sistema operativo que este agente de atualização está em execução. |
| Nome |Recomendação, ConfigurationObjectProperty |Nome do/título do recomendação, ou o nome da propriedade da avaliação de configuração de análise de registos. |
| Valor |ConfigurationObjectProperty |Valor de uma propriedade de avaliação de configuração de análise de registos. |
| KBLink |Recomendação |URL para o artigo KB, que descreve esta melhor prática ou atualização. |
| RecommendationStatus |Recomendação |As recomendações estão entre os tipos de alguns cujos registos for atualizados, não apenas adicionadas para o índice de pesquisa. Este estado é alterado se a recomendação está ativa/aberta ou se a análise de registos Deteta que foi resolvido. |
| RenderedDescription |Evento |Descrição composta (reutilizado texto com parâmetros preenchidos) de um evento do Windows. |
| ParameterXml |Evento |XML com os parâmetros na secção de dados de um evento do Windows (conforme verificado no Visualizador de eventos). |
| EventData |Evento |XML com a secção de dados completa de um evento do Windows (conforme verificado no Visualizador de eventos). |
| Origem |Evento |Fonte de registo de eventos que gerou o evento. |
| EventCategory |Evento |Categoria do evento, diretamente a partir do registo de eventos do Windows. |
| Nome de utilizador |Evento |Nome de utilizador do evento do Windows (normalmente, AUTHORITY\LOCALSYSTEM NT). |
| SampleValue |PerfHourly |Valor médio para a agregação horária de um contador de desempenho. |
| Mín. |PerfHourly |Valor mínimo do intervalo de hora de uma agregação de hora a hora de contador de desempenho. |
| Máx |PerfHourly |Valor máximo do intervalo de hora de uma agregação de hora a hora de contador de desempenho. |
| Percentile95 |PerfHourly |O valor de percentil 95th para o intervalo de hora de uma agregação de hora a hora de contador de desempenho. |
| SampleCount |PerfHourly |Amostras de contador de desempenho em bruto quantas foram utilizadas para produzir este registo de agregação horário. |
| Ameaças |ProtectionStatus |Nome de malware encontrado. |
| StorageAccount |W3CIISLog |Conta de armazenamento do Azure o registo foi lida. |
| AzureDeploymentID |W3CIISLog |ID de implementação do Azure do serviço de nuvem, o registo pertence. |
| Função |W3CIISLog |Função do serviço em nuvem do Azure no registo pertence. |
| RoleInstance |W3CIISLog |RoleInstance da função do Azure que pertence o registo. |
| sSiteName |W3CIISLog |Web site do IIS que pertence o registo (notação metabase); o campo de s-sitename no registo original. |
| sComputerName |W3CIISLog |O campo de s-computername no registo original. |
| sIP |W3CIISLog |Pedido de endereço de HTTP de IP do servidor foi endereçado a. O campo de s-ip no registo original. |
| csMethod |W3CIISLog |Método de HTTP (por exemplo, GET/POST) utilizado pelo cliente no pedido de HTTP. Cs-method no registo original. |
| cIP |W3CIISLog |Pedido de endereço de HTTP de IP do cliente provém o tempo limite. O campo de c-ip no registo original. |
| csUserAgent |W3CIISLog |O agente de utilizador HTTP declarado pelo cliente (browser ou, caso contrário,). O cs-utilizador-agente no registo original. |
| scStatus |W3CIISLog |Código de estado HTTP (por exemplo, 200/403/500) devolvido pelo servidor ao cliente. O estado cs no registo original. |
| timeTaken |W3CIISLog |Como longa (em milissegundos) que o pedido demorou a concluir. O campo de timetaken no registo original. |
| csUriStem |W3CIISLog |O URI relativo (sem o endereço do anfitrião, que é, / procurar) que foi pedido. O campo de cs uristem no registo original. |
| csUriQuery |W3CIISLog |Consulta URI. Consultas URI são necessárias apenas para páginas dinâmicas, tais como as páginas ASP, pelo que este campo contém, normalmente, um hífen para páginas estáticos. |
| Porta |W3CIISLog |Porta do servidor que o pedido HTTP foi enviado para (e escuta que o IIS, uma vez que o se o captado). |
| csUserName |W3CIISLog |Autenticar o nome de utilizador, se o pedido é autenticada e não anónimo. |
| csVersion |W3CIISLog |Versão de protocolo HTTP utilizada no pedido (por exemplo, HTTP/1.1). |
| csCookie |W3CIISLog |Informações do cookie. |
| csReferer |W3CIISLog |Site que o utilizador visitou pela última vez. Este site forneceu uma hiperligação para o site atual. |
| csHost |W3CIISLog |Cabeçalho de anfitrião (por exemplo, www.mysite.com) que foi pedido. |
| scSubStatus |W3CIISLog |Código de erro de subestado. |
| scWin32Status |W3CIISLog |Código de estado do Windows. |
| csBytes |W3CIISLog |Bytes enviados no pedido do cliente para o servidor. |
| scBytes |W3CIISLog |Total de bytes devolvido na resposta do servidor para o cliente. |
| ConfigChangeType |ConfigurationChange |Tipo de alteração (por exemplo, WindowsServices/Software). |
| ChangeCategory |ConfigurationChange |Categoria da alteração (modificados/adicionadas/removidas). |
| SoftwareType |ConfigurationChange |Tipo de software (atualização/aplicação). |
| SoftwareName |ConfigurationChange |Nome do software (apenas aplicável a alterações de software). |
| Publicador |ConfigurationChange |Fornecedor que publica o software (apenas aplicável a alterações de software). |
| SvcChangeType |ConfigurationChange |Tipo de alteração que foi aplicado um serviço do Windows (estado/StartupType/caminho/ServiceAccount). Isto só é aplicável a alterações de serviço do Windows. |
| SvcDisplayName |ConfigurationChange |Nome a apresentar do serviço que foi alterado. |
| SvcName |ConfigurationChange |Nome do serviço que foi alterado. |
| SvcState |ConfigurationChange |Novo Estado (atual) do serviço. |
| SvcPreviousState |ConfigurationChange |Anterior conhecida do Estado do serviço (apenas é aplicável se alterar o estado do serviço). |
| SvcStartupType |ConfigurationChange |Tipo de arranque do serviço. |
| SvcPreviousStartupType |ConfigurationChange |Tipo de arranque de serviço anterior (apenas é aplicável se alterar o tipo de arranque do serviço). |
| SvcAccount |ConfigurationChange |Conta de serviço. |
| SvcPreviousAccount |ConfigurationChange |Anterior conta de serviço (apenas é aplicável se a alteração da conta de serviço). |
| SvcPath |ConfigurationChange |Caminho para o executável do serviço Windows. |
| SvcPreviousPath |ConfigurationChange |Anterior caminho do executável para o serviço do Windows (apenas aplicável no caso-alterada). |
| SvcDescription |ConfigurationChange |Descrição do serviço. |
| Anterior |ConfigurationChange |Estado anterior deste software (versão instalada/não instalada/anterior). |
| Atual |ConfigurationChange |Estado mais recente deste software (versão instalada/não instalada/atual). |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre pesquisas de registo:

* Familiarize-se com as [pesquisas de registos](log-analytics-log-searches.md) para ver informações detalhadas recolhidas pelas soluções.
* Utilize [campos personalizados na análise de registos](log-analytics-custom-fields.md) para expandir as pesquisas de registo.
