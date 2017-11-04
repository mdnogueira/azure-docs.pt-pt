---
title: Encontrar dados com pesquisas de registo no Log Analytics do Azure | Microsoft Docs
description: "As pesquisas de registos permitem combinar e relacionar quaisquer dados do computador provenientes de várias origens no seu ambiente."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.openlocfilehash: d679ca7a01a96bd398b26e6a545e33674ae33390
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="find-data-using-log-searches-in-log-analytics"></a>Localizar dados através de pesquisas de registo na análise de registos

>[!NOTE]
> Este artigo descreve as pesquisas de registo utilizando a linguagem de consulta legado na análise de registos.  Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de nova análise de registos](log-analytics-log-search-upgrade.md), em seguida, deve referir-se a [registo compreender procura no Log Analytics (novo)](log-analytics-log-search-new.md).


O núcleo da análise de registos é a funcionalidade de pesquisa de registo que permite-lhe combinar e correlacionar os dados da máquina de várias origens no seu ambiente. Soluções são também utiliza a tecnologia de registo de pesquisa para assegurar as métricas pivoted em torno de uma área de problema específico.

Na página de pesquisa, pode criar uma consulta e, em seguida, durante a pesquisa, pode filtrar os resultados ao utilizar controlos de aspeto de restrições. Também pode criar consultas avançadas para a transformação, o filtro e o relatório no seu resultados.

Consultas de pesquisa de registo comum apresentados na maioria das páginas de solução. Em toda a consola do OMS, pode clique nos mosaicos ou desagregar outros itens para ver os detalhes sobre o item, utilizando a pesquisa de registo.

Neste tutorial, vamos explicar através de exemplos para cobrir todas as noções básicas quando utilizar a pesquisa de registo.

Vamos começar com exemplos simples, práticos e, em seguida, criar nos mesmos, de modo a que pode obter uma compreensão dos casos de utilização práticas sobre como utilizar a sintaxe para extrair as informações que pretende que os dados.

Depois de já familiarizado com as técnicas de pesquisa, pode rever o [referência de pesquisa de registo de análise de registos](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Utilizar filtros básicos
A primeira coisa saber é que a primeira parte de uma procura consultar, antes de qualquer "|" caráter de barra vertical, é sempre um *filtro*. Pode considerá-la como uma cláusula WHERE na TSQL - determina *que* subconjunto de dados para solicitar a partir do arquivo de dados do OMS. Procurar no arquivo de dados é amplamente sobre Especifica as características dos dados que pretende extrair, pelo que é natural que uma consulta seria começar a utilizar a cláusula WHERE.

Os filtros mais básicos, pode utilizar são *palavras-chave*, tais como 'error' ou 'timeout' ou um nome de computador. Estes tipos de consulta simple, geralmente, devolvem diversas formas de dados dentro do mesmo conjunto de resultados. Isto acontece porque a análise de registos tem diferentes *tipos* dos dados no sistema.

### <a name="to-conduct-a-simple-search"></a>Para realizar uma pesquisa simple
1. No portal do OMS, clique em **pesquisa registo**.  
    ![mosaico de pesquisa](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. No campo de consulta, escreva `error` e, em seguida, clique em **pesquisa**.  
    ![Erro de pesquisa](./media/log-analytics-log-searches/oms-search-error.png)  
    Por exemplo, a consulta para `error` na imagem seguinte devolvido 100 000 **eventos** (recolhidos pelo registo Management), os registos 18 **ConfigurationAlert** registos (gerados pela configuração Avaliação) e 12 **ConfigurationChange** registos (capturados pelo controlo de alterações).   
    ![resultados da pesquisa](./media/log-analytics-log-searches/oms-search-results01.png)  

Estes filtros não são realmente tipos/classes de objetos. *Tipo* é apenas uma etiqueta, ou uma propriedade ou uma cadeia/nome/categoria, que está ligada a um conjunto de dados. Alguns documentos no sistema são etiquetados como **tipo: ConfigurationAlert** e alguns são etiquetados como **tipo: desempenho**, ou **tipo: evento**, e assim sucessivamente. Cada resultado da pesquisa, o documento, o registo ou a entrada apresenta todas as propriedades não processadas e os respetivos valores para cada um dessas peças de dados e pode utilizar esses nomes de campo para especificar no filtro quando pretender obter apenas os registos em que o campo tem que deu um valor.

*Tipo* é realmente apenas um campo com todos os registos, não é diferente de qualquer outro campo. Isto foi estabelecido com base no valor do campo de tipo. Esse registo terão uma outra forma. Incidentally, **tipo = desempenho**, ou **tipo = eventos** também se a sintaxe que precisa de saber consultar dados de desempenho ou eventos.

Pode utilizar ponto e vírgula (:) ou um sinal de igual (=) depois do nome de campo e antes do valor. **Tipo: evento** e **tipo = eventos** são equivalentes no significado, pode escolher o estilo que preferir.

Se Sim, o tipo de = desempenho registos tem um campo chamado 'CounterName', em seguida, pode escrever uma consulta que se assemelha a `Type=Perf CounterName="% Processor Time"`.

Isto irá dar-lhe apenas os dados de desempenho em que o nome do contador de desempenho é "% de tempo do processador".

### <a name="to-search-for-processor-time-performance-data"></a>Para procurar dados de desempenho de tempo do processador
* No campo de consulta de pesquisa, escreva`Type=Perf CounterName="% Processor Time"`

Também pode ser mais específicos e utilizar **InstanceName = _ 'Total'** da consulta, que é um contador de desempenho do Windows. Também pode selecionar um aspeto e outra **: valor de campo**. O filtro é adicionado automaticamente ao seu filtro na barra de consulta. Pode ver este na imagem seguinte. Mostra onde pode clicar para adicionar **InstanceName: Total'** para a consulta sem escrever qualquer coisa.

![aspeto de pesquisa](./media/log-analytics-log-searches/oms-search-facet.png)

A consulta agora fica`Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

Neste exemplo, não terá de especificar **tipo = desempenho** para obter o resultado deste. Porque os campos CounterName e InstanceName só existem para registos do tipo = desempenho, a consulta é específica o suficiente para devolver os mesmos resultados que o tempo, anterior:

```
CounterName="% Processor Time" InstanceName="_Total"
```

Isto acontece porque todos os filtros da consulta são avaliados como estando *e* entre si. Efetivamente, os campos de mais de adicionar aos critérios, obter menos mais resultados específicos e avançados.

Por exemplo, a consulta `Type=Event EventLog="Windows PowerShell"` é idêntico ao `Type=Event AND EventLog="Windows PowerShell"`. Devolve todos os eventos que foram registados no e recolhidos a partir do registo de eventos do Windows PowerShell. Se adicionar um filtro várias vezes selecionando repetidamente o mesmo aspeto, em seguida, o problema é puramente cosmético – poderá clutter a barra de pesquisa, mas ainda devolve os mesmos resultados porque o operador AND implícito existe sempre.

Pode facilmente Inverte o operador AND implícito através da utilização de um operador não explicitamente. Por exemplo:

`Type:Event NOT(EventLog:"Windows PowerShell")`ou equivalente `Type=Event EventLog!="Windows PowerShell"` devolver todos os eventos a partir de todos os registos que não sejam o registo do Windows PowerShell.

Em alternativa, pode utilizar outro operador booleano como 'Ou'. A seguinte consulta devolve registos para o qual o registo de eventos é um sistema ou da aplicação.

```
EventLog=Application OR EventLog=System
```

A consulta acima irá obter entradas para ambos os registos no mesmo conjunto de resultados.

No entanto, se remover o ou deixando os implícita e no local, em seguida, a seguinte consulta irá não produzir qualquer resultados porque não é uma entrada de registo de eventos que pertença a ambos os registos. Cada entrada de registo de eventos foi escrita para apenas um dos dois registos.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>Utilizar filtros adicionais
A seguinte consulta devolve entradas para 2 registos de eventos para todos os computadores que enviaram dados.

```
EventLog=Application OR EventLog=System
```

![resultados de pesquisa](./media/log-analytics-log-searches/oms-search-results03.png)

Selecionar um dos campos ou filtros irá limitar a consulta para um computador específico, excluindo todos os outros diferentes. A consulta resultante iria-se ao seguinte.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Qual é equivalente ao seguinte, devido ao AND. implícita

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Cada consulta é avaliada pela seguinte ordem explícita. Tenha em atenção o parêntesis.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Tal como o campo de registo de eventos, poderá obter dados apenas para um conjunto de computadores específicos, adicionando ou. Por exemplo:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

De igual modo, isto a seguinte consulta devolver **% tempo de CPU** para os dois computadores selecionados apenas.

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>Tipos de campo
Quando criar os filtros, deve compreender as diferenças no trabalhar com diferentes tipos de campos devolvidos pelo registo pesquisas.

**Campos pesquisáveis** Mostrar azul nos resultados da pesquisa.  Pode utilizar campos pesquisáveis em condições de pesquisa específicos para o campo como as seguintes:

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

**Liberte campos pesquisáveis texto** são apresentados a cinzento nos resultados da pesquisa.  Estes não podem ser utilizados com condições de pesquisa específicas para o campo como campos pesquisáveis.  Se apenas serão pesquisados quando executar uma consulta em todos os campos, tais como o seguinte.

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>Operadores Booleanos
Com datetime e os campos numéricos, pode procurar valores utilizando *maior*, *menor que*, e *menor ou igual*. Pode utilizar os operadores simples como >, <>, =, < =,! = na barra de pesquisa de consulta.

Pode consultar o registo de eventos específico durante um período de tempo específico. Por exemplo, as últimas 24 horas é expressa com a seguinte expressão mnemónica.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>A procura utilizando um operador booleano
* No campo de consulta de pesquisa, escreva`EventLog=System TimeGenerated>NOW-24HOURS`  
    ![Procurar com booleano](./media/log-analytics-log-searches/oms-search-boolean.png)

Embora pode controlar o intervalo de tempo graficamente e a maioria das vezes, poderá querer fazê-lo, existem vantagens em incluindo um filtro de tempo diretamente para a consulta. Por exemplo, isto funciona great com dashboards onde pode substituir o tempo para cada mosaico, independentemente do *global* Seletor de tempo na página do dashboard. Para obter mais informações, consulte [é importante de tempo no Dashboard](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Quando fizer a filtragem por hora, tenha em atenção que pode obter resultados para o *intersecção* dos dois períodos de tempo: especificada no portal do OMS (S1) e especificada na consulta (S2).

![intersecção](./media/log-analytics-log-searches/oms-search-intersection.png)

Isto significa que, se os períodos de tempo não intersect, por exemplo no portal do OMS onde escolher **desta semana** e na consulta onde definir **última semana**, em seguida, não existe nenhum intersecção e não irão receber qualquer resultados.

Operadores de comparação utilizados para o campo de TimeGenerated também são úteis noutras situações. Por exemplo, com os campos numéricos.

Por exemplo, dado que os alertas da avaliação de configuração tem os seguintes valores de gravidade:

* 0 = informações
* 1 = aviso
* 2 = crítico

Pode consultar os alertas de aviso e críticas e também excluir informativas aqueles com a seguinte consulta:

```
Type=ConfigurationAlert  Severity>=1
```


Também pode utilizar consultas de intervalo. Isto significa que pode fornecer o intervalo de início e fim de valores numa sequência. Por exemplo, se pretender que os eventos de registo de eventos do Operations Manager onde o EventID é maior que ou igual a 2100 mas não superior a 2199, em seguida, a seguinte consulta devolvam-los.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> A sintaxe de intervalo tem de utilizar é o separador de campo: valor de vírgula (:) e *não* do sinal de igual (=). Coloque o fim superior e inferior do intervalo entre parênteses Retos e separá-los com dois pontos (.).
>
>

## <a name="manipulate-search-results"></a>Manipular os resultados da pesquisa
Quando está a procurar dados, poderá ser útil refinar a consulta de pesquisa e têm um bom nível de controlo sobre os resultados. Quando os resultados são obtidos, pode aplicar comandos para transformá-los.

Comandos em procuras de análise de registos *tem* siga após o caráter de barra vertical (|). Um filtro sempre tem de ser a primeira parte de uma cadeia de consulta. Define o conjunto de dados que está a trabalhar e encaminha, então,"" esses resultados para um comando. Em seguida, pode utilizar o pipe para adicionar os comandos adicionais. Isto é aproximadamente ligado semelhante ao pipeline do Windows PowerShell.

Em geral, o idioma de pesquisa de análise de registos tenta a seguir o estilo do PowerShell e diretrizes para torná-lo semelhante para os profissionais de TI e para facilitar a curva de aprendizagem.

Comandos têm nomes dos verbos para facilmente pode dizer o que fazer.  

### <a name="sort"></a>Ordenar
O comando de ordenação permite-lhe definir a ordenação por um ou vários campos. Mesmo que não utilize, por predefinição, uma vez por ordem descendente é imposta. Os resultados mais recentes são sempre na parte superior dos resultados da pesquisa. Isto significa que, quando executa uma pesquisa com `Type=Event EventID=1234` que realmente é executado para é:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Isto acontece porque é o tipo de experiência de que se familiarizar com nos registos. Por exemplo, no Visualizador de eventos do Windows.

Pode utilizar a ordenação para alterar a forma como os resultados são devolvidos. Os exemplos seguintes mostram como isto funciona.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Os exemplos simples acima mostram-lhe como funcionam os comandos - se alterar a forma dos resultados que o filtro devolveu.

### <a name="limit-and-top"></a>Limite e superior
Outro comando menos conhecido é limite. Limite é de um verbo como o PowerShell. Limite é funcionalmente idêntico ao comando superior. As seguintes consultas de devolvem os mesmos resultados.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>A procura utilizando superior
* No campo de consulta de pesquisa, escreva`Type=Event EventID=600 | Top 1`   
    ![parte superior de pesquisa](./media/log-analytics-log-searches/oms-search-top.png)

A imagem acima, existem registos de 358 thousand com EventID = 600. Os campos, facetas e os filtros à esquerda sempre apresentam informações sobre os resultados devolvidos *por parte do filtro* da consulta, que é a parte antes de qualquer caráter de pipe. O **resultados** painel só devolve o 1 resultado mais recente, porque o comando de exemplo em forma e transformados os resultados.

### <a name="select"></a>Selecione
O comando de selecção tem o mesmo comportamento Select-Object no PowerShell. Devolve resultados filtrados que não dispõe de todas as respetivas propriedades originais. Em vez disso, seleciona apenas as propriedades que especificar.

#### <a name="to-run-a-search-using-the-select-command"></a>Para executar uma pesquisa utilizando o comando selecionado
1. Na pesquisa, escreva `Type=Event` e, em seguida, clique em **pesquisa**.
2. Clique em **+ mostrar mais** dos resultados para ver todas as propriedades que têm os resultados.
3. Selecione algumas dessas explicitamente e a consulta é alterado para `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![Selecione de pesquisa](./media/log-analytics-log-searches/oms-search-select.png)

Este comando é particularmente útil quando pretende controlar o resultado de pesquisa e selecione apenas as partes de dados que realmente interessam para a exploração, o qual, muitas vezes, não o registo completo. Isto também é útil quando tem de registos de diferentes tipos de *algumas* propriedades comuns, mas não *todos os* as respetivas propriedades são comuns. Pode gerar resultados com aspeto mais naturalmente como uma tabela ou funciona bem quando exportado para um ficheiro CSV e, em seguida, massaged no Excel.

## <a name="use-the-measure-command"></a>Utilize o comando de medida
MEDIDA é um dos comandos mais versátil em procuras de análise de registos. Permite-lhe aplicar análises *funções* aos seus dados e agrupados por um determinado campo de resultados de agregação. Existem várias funções de análises que suporta a medida.

### <a name="measure-count"></a>Medida existente
A função de estatística de primeiro para trabalhar com e das mais simples compreender o *existente* função.

Resultados de qualquer consulta de pesquisa, tal como `Type=Event`, mostra os filtros, também designado por facetas no lado esquerdo de resultados da pesquisa. Os filtros de mostram uma distribuição dos valores por um determinado campo para os resultados na pesquisa foi executada.

![Contagem de medidas de pesquisa](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Por exemplo, a imagem acima irá ver o **computador** campo e mostra que dentro de eventos de quase 739 thousand nos resultados, existem 68 valores exclusivos e distintos para os **computador** campo nos registos. O mosaico mostra apenas os primeiros 5, que são mais comuns 5 valores que são escritos no **computador** campos), ordenado pelo número de documentos que contenham esse valor específico nesse campo. Na imagem pode ver que – entre esses eventos quase 369 thousand – 90 thousand provenientes de computador OpsInsights04.contoso.com, 83 thousand do computador DB03.contoso.com e assim sucessivamente.

E se pretende ver todos os valores, uma vez que o mosaico apenas mostra apenas os primeiros 5?

Que é que o comando de medida pode fazer com a função existente. Esta função não utiliza parâmetros. Especificar apenas o campo através do qual pretende agrupar por – o **computador** campo neste caso:

`Type=Event | Measure count() by Computer`

![Contagem de medidas de pesquisa](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

No entanto, **computador** é apenas um campo utilizado *no* cada conjunto de dados – não existem bases de dados relacionais envolvido e não existe nenhum separado **computador** objeto em qualquer lugar. Apenas os valores *no* os dados podem descrevem a que entidade gerado-los e várias outras características e aspetos dos dados –, por conseguinte, o termo *aspeto*. No entanto, acabou também pode agrupar por outros campos. Porque os resultados originais dos eventos de quase 739 thousand que são direcionados para o comando de medidas também tem um campo chamado **EventID**, pode aplicar a mesma técnica para agrupar por esse campo e obter uma contagem de eventos por EventID:

```
Type=Event | Measure count() by EventID
```

Se não estiver interessado na contagem de registo real que contém um valor específico, mas em vez disso, se pretender que apenas uma lista de valores próprios, pode adicionar um *selecione* comando no final do mesmo e selecione apenas a primeira coluna:

```
Type=Event | Measure count() by EventID | Select EventID
```

Em seguida, pode obter mais intricate e previamente ordenar os resultados da consulta, ou apenas pode clicar as colunas na grelha, demasiado.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>A procura utilizando a medida de contagem
* No campo de consulta de pesquisa, escreva`Type=Event | Measure count() by EventID`
* Acrescentar `| Select EventID` ao fim da consulta.
* Por fim, acrescentar `| Sort EventID asc` ao fim da consulta.

Existem alguns pontos importantes para reparar e destacar:

Em primeiro lugar, os resultados, consulte o artigo não são os resultados em bruto originais deixam de ser. Em vez disso, são agregados resultados – essencialmente grupos de resultados. Esta operação não é um problema, mas deve compreender de que está a interagir com uma forma muito diferente de dados que diferem da forma em bruto original que é criada no momento devido a função de agregação estatísticas.

Segundo, **medidas de contagem** atualmente devolve apenas os resultados distintos primeiros 100. Este limite não é aplicável a outras funções estatísticas. Por isso, normalmente, terá de utilizar um filtro de modo mais preciso primeiro para procurar itens específicos antes de aplicar medidas existente.

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Utilize as funções max e min com o comando de medida
Existem vários cenários onde **medidas Max()** e **medidas Min()** são úteis. No entanto, uma vez que cada função é oposta de si, iremos irá ilustrar Max() e pode experimentar Min() por si.

Se a consulta para eventos de segurança, têm um **nível** propriedade que pode variar. Por exemplo:

```
Type=SecurityEvent
```

![início de contagem de medidas de pesquisa](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Se pretender ver o valor mais alto para todos os a segurança de eventos fornecidos um computador comum, o grupo por campo, pode utilizar o

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![computador de máximo de medidas de pesquisa](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Apresentará que para os computadores que tinham **nível** registos, a maioria dos mesmos tem, pelo menos, 8, do nível muitas tinham um nível de 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![tempo máximo de medidas de pesquisa gerado de computador](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Esta função funciona bem com números, mas também funciona com os campos DateTime. É útil verificar se o carimbo de hora do último ou mais recente para qualquer conjunto de dados indexados para cada computador. Por exemplo: quando foi o evento de segurança mais recente comunicado para cada máquina?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Utilize a função avg com o comando de medida
A função de análises de Avg() utilizada com medidas permite-lhe calcular o valor médio para algumas campo e os resultados de grupo, o mesmo ou outro campo. Isto é útil numa variedade de cenários, tais como os dados de desempenho.

Iremos começar com dados de desempenho. Tenha em atenção que o OMS atualmente recolhe os contadores de desempenho para as máquinas do Windows e Linux.

Para procurar *todos os* dados de desempenho, a consulta mais básica é:

```
Type=Perf
```

![início de média de pesquisa](./media/log-analytics-log-searches/oms-search-avg01.png)

A primeira coisa que irá notar é que Log Analytics mostra-lhe três perspetivas: lista, que mostra-lhe que mostra os registos reais atrás os gráficos; Tabela, que mostra uma vista em tabela de dados do contador de desempenho; e métricas, que mostra gráficos dos contadores de desempenho.

Na imagem acima, existem dois conjuntos de campos marcados que indicam o seguinte:

* O primeiro conjunto identifica o nome de contador de desempenho do Windows, o nome do objeto e o nome de instância no filtro de consulta. Estes são os campos, provavelmente, será mais comum é utilizar como facetas/filtros
* **CounterValue** é o valor real do contador. Neste exemplo, o valor é *75*.
* **TimeGenerated** é 12:51, no formato de 24 horas.

Eis uma vista das métricas num gráfico.

![início de média de pesquisa](./media/log-analytics-log-searches/oms-search-avg02.png)

Depois de ler sobre a forma de registo de desempenho e ter ler sobre outras técnicas de pesquisa, pode utilizar medidas Avg() para agrega este tipo de dados numéricos.

Eis um exemplo simples:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![Procurar samplevalue média](./media/log-analytics-log-searches/oms-search-avg03.png)

Neste exemplo, selecione o desempenho de tempo de CPU Total contador e médio por computador. Se pretender restringir os resultados para apenas os último 6 horas, pode utilizar o controlo de filtro de tempo ou especifique na sua consulta do seguinte modo:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Para utilizar a função avg com o comando de medidas de pesquisa
* Na caixa de consulta de pesquisa, escreva `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.

Pode agregar e correlacionar dados *em* computadores. Por exemplo, imagine que tem um conjunto de anfitriões em algumas ordenação de farm em que cada nó é igual para qualquer outro e apenas as mesmas escreva do trabalho e deve ser aproximadamente com balanceamento de carga. Pode obter os contadores de que todos os num aceda com o seguinte de consulta e obter médias para todo o farm. Pode começar a, escolhendo os computadores com o exemplo seguinte:

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Agora que tem os computadores, que também apenas pretende selecionar dois indicadores chave de desempenho (KPIs): % utilização da CPU e % espaço livre em disco. Por isso, torna-se que parte da consulta:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Agora pode adicionar computadores e contadores com o exemplo seguinte:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Porque tem uma seleção muito específica, o **medir Avg()** comando pode devolver a média não pelo computador, mas em farm, simplesmente ao agrupar por CounterName. Por exemplo:

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Isto dá-lhe uma visão compacta útil dos alguns KPIs do seu ambiente.

![agrupamento de média de pesquisa](./media/log-analytics-log-searches/oms-search-avg04.png)

Pode utilizar facilmente a consulta de pesquisa num dashboard. Por exemplo, foi possível guardar a consulta de pesquisa e criar um dashboard do mesmo com o nome *Web Farm KPIs*. Para saber mais sobre como utilizar os dashboards, consulte [criar um dashboard personalizado na análise de registos](log-analytics-dashboards.md).

![dashboard de média de pesquisa](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Utilize a função sum com o comando de medida
A função sum é semelhante a outras funções do comando de medida. Pode ver um exemplo sobre como utilizar a função de soma na [pesquisa de registos W3C IIS no Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Pode utilizar Max() e Min() com números, tempos de data e cadeias de texto. Com cadeias de texto, estes são ordenados por ordem alfabética e obter primeiro e último.

No entanto, não é possível utilizar Sum() com diferente de campos numéricos. Isto também se aplica a Avg().

### <a name="use-the-percentile-function-with-the-measure-command"></a>Utilize a função de percentil com o comando de medida
A função de percentil é semelhante a Avg() e Sum() só pode utilizá-lo para os campos numéricos. Pode utilizar qualquer percentil entre 1 a 99 num campo numérico. Também pode utilizar ambos **percentil** e **pct** comandos. Seguem-se alguns exemplos:  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Utilizar where de comandos
O onde comando funciona como um filtro, mas pode ser aplicada no pipeline para continuar a filtrar resultados agregados que foram produzidos por um comando de medida – em vez de resultados não processados que são filtrados no início de uma consulta.

Por exemplo:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Pode adicionar outro pipe "|" caráter e onde o comando para obter apenas os computadores cuja média CPU é superior a 80%, com o exemplo seguinte:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Se estiver familiarizado com o Microsoft System Center - Operations Manager, pode considerar where comando em termos de pacote de gestão. Se o exemplo uma regra, a primeira parte da consulta seria a origem de dados e where comando seria a deteção de condição.

Pode utilizar a consulta como um mosaico no **My Dashboard**, como um monitor de tipos, quando o computador CPUs estão sobreutilizados. Para saber mais sobre os dashboards, consulte o artigo [criar um dashboard personalizado na análise de registos](log-analytics-dashboards.md). Também pode criar e utilizar dashboards com a aplicação móvel. Para obter mais informações, consulte [OMS Mobile App ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). Os mosaicos inferior dois da imagem seguinte, pode ver o monitor apresentada uma lista e como um número. Essencialmente, sempre pretende que o número a ser igual a zero e a lista para estar em branco. Caso contrário, indica uma condição de alerta. Se necessário, pode utilizá-lo para vê-lo em que máquinas estão sob pressão.

![dashboard móvel](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Utilize o operador em
O *IN* operador, juntamente com *não IN* permite-lhe utilizar subsearches, que são pesquisas incluem outra pesquisa como um argumento. Estes estão contidos na {} de chavetas dentro de outra *primário* ou *externa* pesquisa. O resultado de uma subsearch, muitas vezes, uma lista de resultados distintos, em seguida, é utilizado como um argumento na sua pesquisa primário.

Pode utilizar subsearches para corresponder a subconjuntos de dados que não é possível descrevem diretamente numa expressão de pesquisa, mas que podem ser geradas a partir de uma pesquisa. Por exemplo, se estiver interessado em utilizar uma procura para localizar todos os eventos de *computadores atualizações de segurança em falta*, em seguida, terá de estruturar uma subsearch que identifica pela primeira vez que *computadores atualizações de segurançaemfalta* antes que encontrar eventos que pertencem nesses anfitriões.

Por isso, foi express *computadores atualmente em falta necessário atualizações de segurança* com a seguinte consulta:

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![EXEMPLO de pesquisa](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Assim que tiver a lista, pode utilizar a pesquisa como uma pesquisa interna para feed a lista de computadores para uma pesquisa (principal) externa que irá procurar eventos para esses computadores. Pode fazê-lo ao envolvente a pesquisa interna chavetas e feeding dos resultados como os valores possíveis para um filtro campo na pesquisa externo utilizando o operador IN. A consulta deverá assemelhar-se:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![EXEMPLO de pesquisa](./media/log-analytics-log-searches/oms-search-in02-revised.png)

Também aviso de filtro de tempo utilizada na pesquisa interna porque a avaliação de atualização do sistema guarda um instantâneo de todos os computadores a cada 24 horas. Pode efetuar a consulta interna mais simples e exata procurando apenas um dia. A pesquisa externa em vez disso, utiliza a seleção de tempo na interface de utilizador, a obtenção de eventos dos últimos 7 dias. Consulte [operadores booleanos](#boolean-operators) para obter mais informações sobre os operadores de tempo.

Porque é realmente utilize apenas os resultados da pesquisa interna como um filtro de valor para o exterior, pode continuar a aplicar comandos na pesquisa externa. Por exemplo, ainda pode agrupar os eventos acima com outro comando de medidas:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![EXEMPLO de pesquisa](./media/log-analytics-log-searches/oms-search-in03-revised.png)

Geralmente, quer a consulta interna ao executar rapidamente porque análise de registos com tempos limite do lado do serviço para a mesma e também para devolver uma pequena quantidade de resultados. Se a consulta interna devolve resultados mais, a lista de resultados obtém truncada, que pode provocar, potencialmente, a procura externa para devolver resultados incorretos.

Outra regra é que a pesquisa interna atualmente tem de fornecer *agregados* resultados. Por outras palavras, tem de conter um *medidas* comando; atualmente não é possível feed resultados não processados para uma procura externa.

Além disso, pode haver apenas um operador de IN e tem de ser o último filtro na consulta. Não não possível múltiplos operadores IN ou seria – isto essencialmente impede a executar várias subsearches: o ponto importante é que apenas uma pesquisa de sub/interna possível para cada procura externa.

Mesmo com estes limites IN permite muitos tipos de pesquisas correlacionados e permite-lhe definir algo semelhante a grupos, tais como computadores, utilizadores ou ficheiros – qualquer os campos nos seus dados contêm. Seguem-se exemplos mais:

**Todas as atualizações em falta a partir de computadores onde a definição de atualização automática está desativada**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Todos os eventos de erro de computadores com o SQL Server (= onde foi executada a avaliação do SQL Server)**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Todos os eventos de segurança de computadores que são controladores de domínio (= onde ficou AD avaliação)**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Que outras contas de tem sessão iniciada para os mesmos computadores onde a conta BACONLAND\jochan tem sessão iniciada?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Utilize o comando distinto
Como o nome sugere, este comando fornece uma lista de valores distintos de um campo. É bastante útil mas surprisingly simples. O mesmo pode ser alcançado com comandos do medida existente bem, como mostrado abaixo.

```
Type=Event | Measure count() by Computer
```

![Exemplo de comando de pesquisa distintos](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

No entanto, se todos os estiver interessados em é apenas uma lista de valores distintos e não a contagem de documentos que tenham o que pode fornecer valores, em seguida, DISTINCT, limpeza e mais fáceis de ler os resultados e sintaxe mais curto, como mostrado abaixo.

```
Type=Event | Distinct Computer
```
![Exemplo de comando de pesquisa distintos](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Utilize a função de countdistinct com o comando de medida
A função de countdistinct conta o número de valores distintos dentro de cada grupo. Por exemplo, pode ser utilizado para contabilizar o número de computadores exclusivos para cada tipo de relatório:

```
* | measure countdistinct(Computer) by Type
```

![Countdistinct do OMS](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Utilize o comando de intervalo de medida
Com perto de recolha de dados de desempenho em tempo real, pode recolher e visualizar quaisquer contador de desempenho de análise de registos. Basta introduzir a consulta **tipo: desempenho** devolverá milhares de métricos gráficos com base no número de contadores e os servidores no seu ambiente de análise de registos. Com agregação de métrica a pedido, pode ver as métricas globais no seu ambiente, um nível elevado e uma descrição profunda sobre os dados mais granulares, conforme necessário para.

Digamos que pretende saber o que é a CPU média em todos os seus computadores. Observar a média da CPU para todos os computadores poderão não ser útil porque poderão obter smoothed resultados. Parecer-se para obter mais detalhes, pode agregar o resultado de um período de tempo mais pequeno segmentos de janela e aspeto numa série de tempo em dimensões diferentes. Por exemplo, pode realizar a média de utilização da CPU por hora em todos os seus computadores da seguinte forma:

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![intervalo de média de medida](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Por predefinição, estes resultados serão apresentados num gráfico de linhas interativa de várias séries.  Este gráfico suporta série ativando ou desativando (com o eixo y rescaling), zoom e posicionado.  A opção de apresentação da tabela está ainda disponível para visualizar os dados não processados, se necessário.

Também pode agrupar por outros campos. Neste exemplo, estou à procura em todos os contadores de % de um computador específico e pretender saber o que é os percentiles de hora a hora 70 de cada contador:

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Um aspeto a ter em atenção é que estas consultas não estão limitadas os contadores de desempenho. Pode aplicá-los a qualquer métrica. Neste exemplo, estou à procura registos de W3C IIS. Pretende saber o que é o tempo máximo que demora ao longo de um intervalo de 5 minutos para processar cada pedido:

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Utilizar várias agregados numa consulta
Pode especificar múltiplas cláusulas agregadas num comando de medida.  Cada um deles pode ser um alias de forma independente.  Se este não é dado um alias o nome do campo resultante será a função de agregação que era utilizado (ou seja, "avg(CounterValue)" para avg(CounterValue)).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![Multiaggregates1 do OMS](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Segue-se outro exemplo:

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre pesquisas de registo, consulte:

* Utilize [campos personalizados na análise de registos](log-analytics-custom-fields.md) para expandir as pesquisas de registo.
* Reveja o [referência de pesquisa de registo de análise de registos](log-analytics-search-reference.md) para ver todos os campos de pesquisa e facetas disponíveis na análise de registos.
