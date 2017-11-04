---
title: "Guia de programação para U-SQL do Azure Data Lake | Microsoft Docs"
description: "Saiba mais sobre o conjunto de serviços no Azure Data Lake que permitem-lhe criar uma plataforma de macrodados baseado na nuvem."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: saveenr
ms.openlocfilehash: bba8fff7997340e563c604f571604ee8d06eb719
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="u-sql-programmability-guide"></a>Guia de programação para U-SQL

U-SQL é uma linguagem de consulta que foi concebida para o tipo de dados grande de cargas de trabalho. Uma das funcionalidades exclusivas do U-SQL é a combinação do idioma declarativa com a extensibilidade e programação para que o c# como o SQL Server. Neste guia, vamos concentrar-se na programação para de linguagem U-SQL que está ativada por c# e extensibilidade.

## <a name="requirements"></a>Requisitos

Transfira e instale [ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Introdução ao U-SQL  

Observe o seguinte script U-SQL:

```
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Este script define dois conjuntos de linhas: `@a` e `@results`. Conjunto de linhas `@results` é definida do `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipos de c# e expressões no script U-SQL

Uma expressão de U-SQL é uma expressão de c# combinada com operações de lógicas de U-SQL, tais `AND`, `OR`, e `NOT`. As expressões de U-SQL pode ser utilizadas com SELECIONE, a EXTRAIR, WHERE, tendo, agrupar por e declarar. Por exemplo, o seguinte script analisa numa cadeia como um valor de DateTime.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

O fragmento seguinte analisa numa cadeia como valor de DateTime numa instrução DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Utilizar c# expressões para conversões de tipo de dados

O exemplo seguinte demonstra como pode efetuar uma conversão de dados datetime através de expressões do c#. Neste cenário específico, dados de datetime da cadeia são convertidos padrão datetime com notação de tempo de 00:00:00 meia-noite.

```
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Utilizar expressões de c# para a data de hoje

Para solicitar a data de hoje, podemos utilizar a seguinte expressão c#:`DateTime.Now.ToString("M/d/yyyy")`

Eis um exemplo de como utilizar esta expressão num script:

```
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Utilizar as assemblagens .NET

Modelo de extensibilidade do U-SQL depende de descontos elevados a capacidade de adicionar código personalizado de assemblagens .NET. 

### <a name="register-a-net-assembly"></a>Registar uma assemblagem .NET

Utilize o `CREATE ASSEMBLY` instrução colocar uma assemblagem .NET numa base de dados U-SQL. Seguidamente, scripts U-SQL podem utilizar esses assemblagens utilizando o `REFERENCE ASSEMBLY` instrução. 

O código seguinte mostra como registar uma assemblagem:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

O código seguinte mostra como fazer referência uma assemblagem:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Consulte o [instruções de registo de assemblagem](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) que abrange este tópico em maior detalhe.


### <a name="use-assembly-versioning"></a>Utilizar o controlo de versões da assemblagem
Atualmente, o U-SQL utiliza o .NET Framework versão 4.5. Por isso, certifique-se de que os seus próprios assemblagens são compatíveis com que a versão do tempo de execução.

Conforme mencionado anteriormente, código de execuções de U-SQL num formato de 64 bits (x64). Por isso certifique-se de que o seu código é compilado para executar em x64. Caso contrário, obterá o erro de formato incorreto, apresentado anteriormente.

Cada carregou a assemblagem DLL e ficheiro de recursos, tais como um tempo de execução diferente, uma assemblagem nativa ou um ficheiro de configuração, pode ter um máximo de 400 MB. O tamanho total dos recursos implementados, ou através de implementar recursos através de referências a assemblagens e respetivos ficheiros adicionais, não pode exceder 3 GB.

Por fim, tenha em atenção que cada base de dados do U-SQL só pode conter uma versão de qualquer assemblagem em questão. Por exemplo, se tiver a versão 7 e versão 8 da biblioteca NewtonSoft Json.Net, terá de registá-los no duas bases de dados diferentes. Além disso, cada script só pode fazer referência a uma versão de uma assemblagem em questão DLL. No que esta respeita U-SQL, segue-se a c# assemblagem gestão e o controlo de versões de semântica.

## <a name="use-user-defined-functions-udf"></a>Utilize as funções definidas pelo utilizador: UDF
As funções definidas pelo utilizador do U-SQL ou UDF, são programação rotinas que aceitam parâmetros, execute uma ação (por exemplo, um cálculo complexo) e o resultado da ação como um valor de retorno. O valor devolvido do UDF só pode ser uma escalar único. U-SQL UDF pode ser chamado no script de base de U-SQL, como quaisquer outro c# função escalar.

Recomendamos que inicializar U-SQL definidos pelo utilizador as funções como **pública** e **estático**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Primeiro vamos ver o exemplo simple de criação de um UDF.

Neste cenário de caso de utilização, é necessário determinar o período de fiscal, incluindo o trimestre fiscal e mês fiscal do primeiro início de sessão para o utilizador específico. O primeiro mês fiscal do ano no nosso cenário é Junho.

Para calcular o período de fiscal, iremos introduzir a seguinte função do c#:

```
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Basta calcula mês fiscal e trimestre e devolve um valor de cadeia. Junho, o primeiro mês do trimestre fiscal primeiro, vamos utilizar "Q1:P1". Para Julho, iremos utilizar "Q1:P2" e assim sucessivamente.

Este é uma regular função de c# que iremos utilizar no nosso projeto U-SQL.

Eis o aspeto da secção de code-behind neste cenário:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Agora, vamos chamar esta função do script U-SQL base. Para fazer isto, temos de fornecer um nome completamente qualificado para a função, incluindo o espaço de nomes, que neste caso é NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Segue-se o script de base do U-SQL real:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Segue-se o ficheiro de saída da execução do script:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Este exemplo demonstra a utilização de um simple de inline UDF em U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Manter o estado entre invocações de UDF
Objetos de programação para c# do U-SQL podem ser mais sofisticados, a utilização de interatividade através as variáveis globais por detrás do código. Vamos ver o seguinte cenário de caso de utilização de negócio.

Em grandes organizações, os utilizadores podem alternar entre varieties de aplicações internas. Pode incluir Microsoft Dynamics CRM, PowerBI e assim sucessivamente. Os clientes poderão pretender aplicar uma análise de telemetria do modo como os utilizadores alternar entre aplicações diferentes, quais são as tendências de utilização, e assim sucessivamente. O objetivo para as empresas é para otimizar a utilização da aplicação. Também poderá pretendem combinar rotinas de início de sessão específicas ou de diferentes aplicações.

Para atingir este objetivo, temos de determinar os IDs de sessão e lag tempo entre a última sessão que ocorreram.

É necessário localizar um anterior início de sessão e, em seguida, atribuir este início de sessão para todas as sessões que estão a ser geradas para a mesma aplicação. No primeiro desafio é que o script U-SQL de base não permite-na aplicar cálculos sobre colunas calculadas já com a função LAG. O desafio do segundo é que temos de manter a sessão específica para todas as sessões no mesmo período de tempo.

Para resolver este problema, utilizamos uma variável global dentro de uma secção code-behind: `static public string globalSession;`.

Esta variável global é aplicada para o conjunto de linhas completo durante a execução do script do nosso.

Segue-se a secção do code-behind do nosso programa U-SQL:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Este exemplo mostra a variável global `static public string globalSession;` utilizado dentro de `getStampUserSession` função e obter reiniciadas sempre que o parâmetro de sessão é alterado.

O script de base do U-SQL é o seguinte:

```
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Função `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` denomina aqui durante o segundo conjunto de linhas cálculo da memória. Estes passam a `UserSessionTimestamp` coluna e devolve o valor até `UserSessionTimestamp` foi alterada.

O ficheiro de saída é o seguinte:

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Este exemplo demonstra um cenário de caso de utilização mais complicado que utilizamos uma variável global dentro de uma secção de code-behind que é aplicada para o conjunto de linhas de memória de todo.

## <a name="use-user-defined-types-udt"></a>Utilizar tipos definidos pelo utilizador: UDT
Os tipos definidos pelo utilizador ou UDT, é outra programação para funcionalidade do U-SQL. U-SQL UDT funciona como um regular c# definido pelo utilizador tipo. C# é uma linguagem com tipo seguro que permita a utilização de tipos incorporadas e personalizadas definidas pelo utilizador.

U-SQL implicitamente não é possível serializar ou anular a serialização UDTs arbitrários, quando o UDT é transferida entre vértices em conjuntos de linhas. Isto significa que o utilizador tem de fornecer um formatador explícita utilizando a interface IFormatter. Isto fornece o U-SQL com a serializar e anular a serialização métodos para a UDT.

> [!NOTE]
> U-SQL extractors incorporadas e outputters atualmente não é possível serializar ou dados UDT de ficheiros, mesmo com o conjunto de IFormatter ou para anular a serialização. Por isso, quando estiver a escrever dados UDT para um ficheiro com a instrução de saída ou lê-lo com um extrator, tiver transmiti-lo como uma matriz de cadeia ou bytes. Em seguida, chame a serialização e anulação da serialização da código (ou seja, método ToString () da UDT) explicitamente. Definido pelo utilizador extractors e outputters, por outro lado, podem ler e escrever UDTs.

Se, experimente utilizar UDT EXTRATOR ou OUTPUTTER (fora SELECIONE anterior), conforme mostrado aqui:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Recebemos o seguinte erro:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Para trabalhar com UDT no outputter, podemos ter a serialização para cadeia com o método ToString () ou criar um outputter personalizado.

UDTs atualmente não é possível utilizar GROUP BY. Se for utilizado UDT no GROUP BY, é emitido o seguinte erro:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Para definir um UDT, temos de:

* Adicione os espaços de nomes seguintes:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Adicionar `Microsoft.Analytics.Interfaces`, que é necessário para as interfaces UDT. Além disso, `System.IO` poderá ser necessário definir a interface IFormatter.

* Defina um tipo definido pelo utilizado com o atributo SqlUserDefinedType.

**SqlUserDefinedType** é utilizada para marcar uma definição de tipo numa assemblagem como um tipo definido pelo utilizador (UDT) em U-SQL. As propriedades no atributo refletem as características físicas do UDT. Esta classe não pode ser herdada.

SqlUserDefinedType é um atributo necessário para a definição de UDT.

O construtor de classe:  

* SqlUserDefinedTypeAttribute (formatador de tipo)

* Escreva o formatador: necessário o parâmetro para definir um formatador UDT – especificamente, o tipo do `IFormatter` interface tem de ser transmitida aqui.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* UDT típico também requer a definição da IFormatter interface, conforme mostrado no exemplo seguinte:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

O `IFormatter` interface serializa e anular serializa um gráfico de objeto com o tipo de raiz de \<typeparamref name = "T" >.

\<typeparam name = "T" > o tipo de raiz para o gráfico de objeto para serializar e anular a serialização.

* **Anular a serialização**: anular serializa os dados no fluxo fornecido e reconstitutes o gráfico de objetos.

* **Serializar**: Serializa um objeto ou o gráfico de objetos, com a raiz fornecido no fluxo fornecido.

`MyType`instância: instância do tipo.  
`IColumnWriter`escritor / `IColumnReader` leitor: O fluxo subjacente da coluna.  
`ISerializationContext`contexto: enumeração que define um conjunto de sinalizadores que especifica o contexto de origem ou de destino para o fluxo durante a serialização.

* **Intermédio**: Especifica que o contexto de origem ou de destino não é um arquivo persistente.

* **Persistência**: Especifica que o contexto de origem ou de destino é um arquivo persistente.

Como um regular c# tipo, uma definição de U-SQL UDT pode incluir substituições para operadores como + /Safari/Chrome = = /! =. Também pode incluir métodos estáticos. Por exemplo, se, vamos utilizar este UDT como um parâmetro para uma função de agregação MIN U-SQL, é necessário definir < substituição de operador.

Anteriormente neste guia, iremos demonstrado um exemplo de identificação de período fiscal contar da data no formato específica `Qn:Pn (Q1:P10)`. O exemplo seguinte mostra como definir um tipo personalizado para valores fiscais.

Segue-se um exemplo de uma secção code-behind com interface UDT e IFormatter personalizada:

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

O tipo definido inclui dois números: trimestre e mês. Os operadores `==/!=/>/<` e método estático `ToString()` são definidas aqui.

Conforme mencionado anteriormente, UDT pode ser utilizado em expressões SELECT, mas não é possível utilizar OUTPUTTER/EXTRATOR sem serialização personalizada. Se tem de ser serializado como uma cadeia com `ToString()` utilizadas com um OUTPUTTER/EXTRATOR personalizado.

Agora vamos discutir sobre a utilização de UDT. Uma secção por detrás do código, Alterámos a nossa função GetFiscalPeriod ao seguinte:

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Como pode ver, devolve o valor do tipo nosso FiscalPeriod.

Aqui fornecemos um exemplo de como utilizá-la mais de script U-SQL de base. Este exemplo demonstra formas diferentes de invocação UDT do script U-SQL.

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Eis um exemplo de uma secção completo por detrás do código:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Utilize agregações definidas pelo utilizador: UDAGG
Os agregados definidos pelo utilizador são quaisquer funções relacionadas com a agregação que são fornecidos não out-of-a-box com U-SQL. O exemplo pode ser um agregado efetuar cálculos de bibliotecas personalizado, concatenations de cadeia, manipulações com cadeias e assim sucessivamente.

A definição de classe base agregado definido pelo utilizador é o seguinte:

```c#
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indica que o tipo deve ser registado como um agregado definido pelo utilizador. Esta classe não pode ser herdada.

Atributo de SqlUserDefinedType **opcional** para definição UDAGG.


A classe base poder passar os parâmetros abstratos três: dois como parâmetros de entrada e um como resultado. Os tipos de dados são variáveis e devem ser definidos durante a herança de classe.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** invoca uma vez para cada grupo durante o cálculo. Fornece uma rotina de inicialização para cada grupo de agregação.  
* **Acumular** é executado uma vez para cada valor. Fornece a funcionalidade principal para o algoritmo de agregação. Pode ser utilizada para agregados valores com vários tipos de dados que são definidos durante a herança de classe. Pode aceitar o dois parâmetros dos tipos de dados da variável.
* **Terminar** é executado uma vez por grupo de agregação no final de processamento para a saída de resultado para cada grupo.

Declarar os tipos de dados de saída e entrada correta, utilize a definição de classe da seguinte forma:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Primeiro parâmetro acumular
* T2: Primeiro parâmetro acumular
* TResult: Tipo de retorno da terminação

Por exemplo:

```
public class GuidAggregate : IAggregate<string, int, int>
```

ou

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Utilizar UDAGG em U-SQL
Para utilizar UDAGG, defini-lo no code-behind ou referenciá-lo a partir de programação para o DLL existente, tal como abordado anteriormente.

Em seguida, utilize a seguinte sintaxe:

```
AGG<UDAGG_functionname>(param1,param2)
```

Eis um exemplo de UDAGG:

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

E o script de U-SQL base:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Neste cenário de caso de utilização, iremos concatenar classe GUIDs para os utilizadores específicos.

## <a name="use-user-defined-objects-udo"></a>Utilizar os objetos definidos pelo utilizador: UDO
U-SQL permite-lhe definir os objetos de programação para personalizadas, que são chamados objetos definidos pelo utilizador ou UDO.

Segue-se uma lista de UDO em U-SQL:

* Extractors definido pelo utilizador
    * Extrair linha por linha
    * Utilizado para implementar a extração de dados de ficheiros estruturados personalizados

* Outputters definido pelo utilizador
    * Saída linha por linha
    * Utilizado para tipos de dados personalizada de saída ou de ficheiros personalizadas formatos

* Processadores definidos pelo utilizador
    * Obter uma linha e produzir uma linha
    * Utilizado para reduzir o número de colunas ou produzir novas colunas com valores que são derivadas de um conjunto de coluna existente

* Appliers definido pelo utilizador
    * Obter uma linha e produzir 0 para linhas n
    * Utilizado com OUTER/entre aplicar

* Combiners definido pelo utilizador
    * Combina os conjuntos de linhas – associações definidas pelo utilizador

* Reducers definido pelo utilizador
    * Tome n linhas e produzir uma linha
    * Utilizado para reduzir o número de linhas

UDO é normalmente designado explicitamente no script U-SQL como parte das seguintes declarações de U-SQL:

* EXTRAIR
* SAÍDA
* PROCESSO
* COMBINAR
* REDUZIR

> [!NOTE]  
> UDO é limitadas para consumir 0.5Gb de memória.  Esta limitação de memória não é aplicável a execuções locais.

## <a name="use-user-defined-extractors"></a>Utilizar extractors definido pelo utilizador
U-SQL permite-lhe importar dados externos, utilizando uma instrução de EXTRAÇÃO. Uma instrução de EXTRAÇÃO pode utilizar extractors UDO incorporadas:  

* *Extractors.Text()*: fornece a extração dos ficheiros de texto delimitado de codificações diferentes.

* *Extractors.Csv()*: fornece a extração de valores separados por vírgulas (CSV) os ficheiros do codificações diferentes.

* *Extractors*: fornece a extração de valores separados por separador ficheiros (TSV) de codificações diferentes.

Podem ser úteis para desenvolver um extrator personalizado. Isto pode ser útil durante a importação de dados se de que pretende efetuar qualquer uma das seguintes tarefas:

* Modificar os dados de entrada ao dividir colunas e modificar valores individuais. A funcionalidade do processador é melhor para a combinação de colunas.
* Analisar dados não estruturados, tais como páginas Web e os e-mails ou dados por não estruturados como XML/JSON.
* Analisar dados na codificação não suportada.

Para definir um extrator definido pelo utilizador ou UIR, temos de criar um `IExtractor` interface. Todos os parâmetros para extrator, tais como delimitadores de linha/coluna, de entrada e de codificação, têm de ser definidos no construtor da classe. O `IExtractor` interface deve conter também uma definição para o `IEnumerable<IRow>` substituir da seguinte forma:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

O **SqlUserDefinedExtractor** atributo indica que o tipo deve ser registado como um extrator definido pelo utilizador. Esta classe não pode ser herdada.

SqlUserDefinedExtractor é um atributo opcional para a definição de UIR. Utilizá-la para definir a propriedade AtomicFileProcessing para o objeto de UIR.

* bool AtomicFileProcessing   

* **Verdadeiro** = indica que este extrator requer atómicos os ficheiros de entrada (JSON, XML,...)
* **FALSO** = indica que este extrator pode lidar com ficheiros divididos / distribuídos (CSV, SEQ,...)

Os objetos de programação para UIR principais são **entrada** e **saída**. O objeto de entrada é utilizado para enumerar os dados de entrada como `IUnstructuredReader`. O objeto de resultado é utilizado para definir os dados de saída como resultado a atividade de extrator.

Os dados de entrada são acedidos através do `System.IO.Stream` e `System.IO.StreamReader`.

Para a enumeração de colunas de entrada, vamos primeiro dividir o fluxo de entrada utilizando um delimitador de linha.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Em seguida, divida mais linhas de entrada em partes de coluna.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Para definir os dados de saída, utilizamos o `output.Set` método.

É importante compreender que o extrator personalizado produz apenas colunas e valores que estão definidos com a saída. Definir a chamada de método.

```
output.Set<string>(count, part);
```

A saída de extrator real é acionada ao chamar `yield return output.AsReadOnly();`.

Segue-se ao exemplo extrator:

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

Neste cenário de caso de utilização, o extrator gera de novo o GUID para a coluna "guid" e converte os valores da coluna "utilizador" em maiúsculas. Extractors personalizados podem produzir resultados mais complicados pela análise de dados de entrada e manipulá-lo.

Segue-se base script U-SQL que utiliza um extrator personalizado:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Utilizar outputters definido pelo utilizador
Definido pelo utilizador outputter é outra UDO U-SQL que lhe permita expandir a funcionalidade de U-SQL incorporada. Semelhante para o extrator, existem várias outputters incorporadas.

* *Outputters.Text()*: escreve dados para os ficheiros de texto delimitado de codificações diferentes.
* *Outputters*: escreve dados para os ficheiros de ficheiro de valores separados por vírgulas (CSV) de codificações diferentes.
* *Outputters.Tsv()*: escreve dados para os ficheiros de ficheiro de valores separados por separador (TSV) de codificações diferentes.

Outputter personalizada permite-lhe escrever dados num formato personalizado definido. Isto pode ser útil para as seguintes tarefas:

* Escrever os dados não estruturados ou semiestruturados ficheiros.
* Escrita de dados não suportado codificações.
* A modificação de dados de saída ou adicionar atributos personalizados.

Para definir outputter definido pelo utilizador, temos de criar o `IOutputter` interface.

Segue-se a base de `IOutputter` implementação de classe:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Todos os parâmetros de entrada para outputter, tais como delimitadores de linha/coluna, codificação e assim sucessivamente, tem de ser definida no construtor da classe. O `IOutputter` interface deve conter também uma definição para `void Output` substituir. O atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` , opcionalmente, pode ser definido para o processamento do ficheiro atomic. Para obter mais informações, consulte os seguintes detalhes.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output`é chamado para cada linha de entrada. Devolve o `IUnstructuredWriter output` conjunto de linhas.
* A classe de construtor é utilizada para passar os parâmetros para outputter definido pelo utilizador.
* `Close`é utilizada para substituir, opcionalmente, a versão Estado dispendiosas ou a determinar quando a última linha foi escrita.

**SqlUserDefinedOutputter** atributo indica que o tipo deve ser registado como um outputter definido pelo utilizador. Esta classe não pode ser herdada.

SqlUserDefinedOutputter é um atributo opcional para uma definição de outputter definido pelo utilizador. É utilizado para definir a propriedade AtomicFileProcessing.

* bool AtomicFileProcessing   

* **Verdadeiro** = indica que este outputter requer ficheiros de saída atómico (JSON, XML,...)
* **FALSO** = indica que este outputter pode lidar com ficheiros divididos / distribuídos (CSV, SEQ,...)

Os objetos de programação para principais são **linha** e **saída**. O **linha** objeto é utilizado para enumerar os dados de saída como `IRow` interface. **Saída** é utilizado para definir os dados de saída para o ficheiro de destino.

Os dados de saída são acedidos através do `IRow` interface. Dados de saída são transmitidos uma linha de cada vez.

Os valores individuais são enumerados ao chamar o método Get da IRow interface:

```
row.Get<string>("column_name")
```

É possível determinar os nomes das colunas individuais ao chamar `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Esta abordagem permite-lhe criar um outputter flexível para qualquer esquema metadata.

Os dados de saída são escritos no ficheiro utilizando `System.IO.StreamWriter`. O parâmetro de fluxo é definido como `output.BaseStrea` como parte da `IUnstructuredWriter output`.

Tenha em atenção que é importante esvaziar a memória intermédia de dados para o ficheiro após cada iteração de linha. Além disso, o `StreamWriter` objeto tem de ser utilizado com o atributo Disposable ativada (predefinição) e com o **utilizando** palavra-chave:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Caso contrário, chame o método Flush() explicitamente após cada iteração. Vamos mostrar no exemplo seguinte.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Definir cabeçalhos e rodapés de página para outputter definido pelo utilizador
Para definir um cabeçalho, utilize o fluxo de execução única iteração.

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

O código na primeira `if (isHeaderRow)` bloco é executado apenas uma vez.

Para o rodapé, utilize a referência à instância do `System.IO.Stream` objeto (`output.BaseStream`). Escrever o rodapé no método Close() do `IOutputter` interface.  (Para obter mais informações, consulte o exemplo seguinte).

Segue-se um exemplo de um outputter definido pelo utilizador:

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close().
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

E o script U-SQL de base:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Este é um outputter HTML, que cria um ficheiro HTML com a dados da tabela.

### <a name="call-outputter-from-u-sql-base-script"></a>Chamada outputter do script U-SQL de base
Para chamar um outputter personalizado do script U-SQL base, a nova instância do objeto outputter tem de ser criado.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar a criação de uma instância do objeto de script de base, podemos criar um dispositivo de moldagem de função, conforme mostrado no nosso exemplo anterior:

```c#
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Neste caso, a chamada original o seguinte aspeto:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Utilizar processadores definidos pelo utilizador
Processador definido pelo utilizador ou UDP, é um tipo de UDO U-SQL que lhe permite processar as linhas de entrada, aplicando as funcionalidades de programação para. UDP permite-lhe combinar as colunas, modificar os valores e adicionar novas colunas, se necessário. Basicamente, ajuda a processar um conjunto de linhas para produzir os elementos de dados necessários.

Para definir um UDP, temos de criar um `IProcessor` interface com o `SqlUserDefinedProcessor` atributo, o que é opcional para UDP.

Esta interface deve conter a definição para o `IRow` conjunto de linhas de interface substituir, conforme mostrado no exemplo seguinte:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** indica que o tipo deve ser registado como um processador definido pelo utilizador. Esta classe não pode ser herdada.

O atributo SqlUserDefinedProcessor **opcional** para a definição do UDP.

Os objetos de programação para principais são **entrada** e **saída**. O objeto de entrada é utilizado para enumerar as colunas de entrada e saída e para definir os dados de saída como resultado a atividade de processador.

Para a enumeração de colunas de entrada, utilizamos o `input.Get` método.

```
string column_name = input.Get<string>("column_name");
```

O parâmetro `input.Get` método é uma coluna que é transmitida como parte do `PRODUCE` cláusula do `PROCESS` declaração do script U-SQL base. É necessário utilizar o tipo de dados correto aqui.

Para a saída, utilize o `output.Set` método.

É importante ter em atenção que produtor personalizado produz apenas colunas e valores que são definidos com o `output.Set` chamada de método.

```
output.Set<string>("mycolumn", mycolumn);
```

A saída de processador real é acionada ao chamar `return output.AsReadOnly();`.

Segue-se um exemplo de processador:

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

Neste cenário de caso de utilização, o processador está a gerar uma nova coluna chamada "full_description" ao combinar as colunas existentes – neste caso, "utilizador" em maiúsculas e "des". Também gera de novo um GUID e devolve os valores GUID originais e nova.

Como pode ver do exemplo anterior, pode chamar métodos c# durante `output.Set` chamada de método.

Segue-se um exemplo de script U-SQL base que utiliza um processador personalizado:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Utilizar appliers definido pelo utilizador
Um applier do U-SQL definidos pelo utilizador permite-lhe invocar uma função de c# personalizada para cada linha que é devolvida pela expressão de tabela externa de uma consulta. A entrada à direita é avaliada para cada linha da entrada à esquerda e as linhas que são produzidas são combinadas para o resultado final. A lista de colunas que são produzidos pelo operador APPLY são a combinação de conjunto de colunas no lado esquerdo e a entrada à direita.

Applier definido pelo utilizador está a ser invocado como parte da expressão USQL SELECIONAR.

A chamada típica applier definido pelo utilizador tem o seguinte aspeto:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Para obter mais informações sobre como utilizar appliers numa expressão SELECT, consulte [U-SQL SELECIONE selecionar de entre aplicar e OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx).

A definição de classe base applier definido pelo utilizador é o seguinte:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Para definir um applier definido pelo utilizador, temos de criar o `IApplier` interface com o [`SqlUserDefinedApplier`] atributo, o que é opcional para uma definição applier definido pelo utilizador.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Aplicar é chamado para cada linha da tabela externa. Devolve o `IUpdatableRow` de saída do conjunto de linhas.
* A classe de construtor é utilizada para passar os parâmetros para applier definido pelo utilizador.

**SqlUserDefinedApplier** indica que o tipo deve ser registado como um applier definido pelo utilizador. Esta classe não pode ser herdada.

**SqlUserDefinedApplier** é **opcional** para uma definição applier definido pelo utilizador.


Os objetos de programação para principais são os seguintes:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Conjuntos de linhas de entrada são transmitidos como `IRow` entrada. As linhas de saída são geradas como `IUpdatableRow` interface de saída.

É possível determinar os nomes das colunas individuais ao chamar o `IRow` método de esquema.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Para obter os valores de dados real a partir de entrada `IRow`, utilizamos o método Get() `IRow` interface.

```
mycolumn = row.Get<int>("mycolumn")
```

Ou, utilizamos o nome de coluna do esquema:

```
row.Get<int>(row.Schema[0].Name)
```

Os valores de saída tem de ser definidos com `IUpdatableRow` saída:

```
output.Set<int>("mycolumn", mycolumn)
```

É importante compreender que appliers personalizados saída apenas colunas e valores que estão definidos com `output.Set` chamada de método.

A saída real é acionada ao chamar `yield return output.AsReadOnly();`.

Os parâmetros applier definido pelo utilizador podem ser transmitidos a este construtor. Applier pode devolver um número de colunas que necessitam de ser definido durante a chamada applier no Script de U-SQL base variável.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Eis o exemplo applier definido pelo utilizador:

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Segue-se o script U-SQL base para este applier definido pelo utilizador:

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Neste cenário de cenários de utilização, applier definido pelo utilizador atua como um analisador de valor delimitado por vírgulas para as propriedades de fleet carro. As linhas do ficheiro de entrada tem o seguinte aspeto:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

É um ficheiro do TSV típico de ' delimitada por separador ' para com uma coluna de propriedades que contém as propriedades de car como a marca e modelo. Essas propriedades devem ser analisadas para as colunas da tabela. Applier fornecida também permite-lhe gerar um número dinâmico de propriedades no conjunto de linhas do resultado, com base no parâmetro que é transmitido. Pode gerar todas as propriedades ou um conjunto específico de propriedades apenas.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Pode ser chamado applier definido pelo utilizador como uma nova instância do objeto applier:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Ou com a invocação de um método de fábrica do dispositivo de moldagem:

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Utilizar combiners definido pelo utilizador
Combinação definido pelo utilizador ou UDC, permite-lhe combinar as linhas da esquerda e direita conjuntos de linhas, com base na lógica personalizada. Combinação definido pelo utilizador é utilizada com a expressão de combinação.

Uma combinação está a ser invocada com a expressão de combinação que fornece as informações necessárias sobre os conjuntos de linhas de entrada, as colunas de agrupamento, o esquema de resultado esperado e informações adicionais.

Para chamar uma combinação num script U-SQL base, podemos utilizar a seguinte sintaxe:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Para obter mais informações, consulte [COMBINAR expressão (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

Para definir uma combinação de definido pelo utilizador, temos de criar o `ICombiner` interface com o [`SqlUserDefinedCombiner`] atributo, o que é opcional para uma definição de combinação definido pelo utilizador.

Base `ICombiner` definição de classe:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

A implementação personalizada de um `ICombiner` interface deve conter a definição de um `IEnumerable<IRow>` combinar substituição.

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

O **SqlUserDefinedCombiner** atributo indica que o tipo deve ser registado como uma combinação de definido pelo utilizador. Esta classe não pode ser herdada.

**SqlUserDefinedCombiner** é utilizado para definir a propriedade de modo de combinação. É um atributo opcional para uma definição de combinação definido pelo utilizador.

Modo de CombinerMode

Enumeração de CombinerMode pode efetuar os seguintes valores:

* Completo (0) cada linha de saída depende potencialmente todas as linhas entradas da esquerda e à direita com o mesmo valor de chave.

* À esquerda (1) cada linha de saída depende de uma única linha de entrada à esquerda (e, potencialmente, todas as linhas a partir da direita com o mesmo valor de chave).

* À direita (2) cada linha de saída depende de uma única linha de entrada à direita (e, potencialmente, todas as linhas da esquerda com o mesmo valor de chave).

* Interna (3) cada linha de saída depende de uma única linha de entrada da esquerda e à direita com o mesmo valor.

Exemplo: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Os objetos de programação para principais são:

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Conjuntos de linhas de entrada são transmitidos como **esquerdo** e **direita** `IRowset` tipo de interface. Ambos os conjuntos de linhas tem de ser enumerados para processamento. Apenas pode enumerar cada interface de uma só vez, para que tenhamos enumerar e cache-la, se necessário.

Para efeitos de colocação em cache, podemos criar uma lista\<T\> tipo de estrutura de memória como resultado de uma LINQ execução da consulta, especificamente lista <`IRow`>. O tipo de dados anónimos pode ser utilizado durante a enumeração bem.

Consulte [introdução às consultas do LINQ (c#)](https://msdn.microsoft.com/library/bb397906.aspx) para obter mais informações sobre consultas LINQ, e [IEnumerable\<T\> Interface](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) para obter mais informações sobre IEnumerable\<T\> interface.

Para obter os valores de dados real a partir de entrada `IRowset`, utilizamos o método Get() `IRow` interface.

```
mycolumn = row.Get<int>("mycolumn")
```

É possível determinar os nomes das colunas individuais ao chamar o `IRow` método de esquema.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ou utilizando o nome de coluna do esquema:

```
c# row.Get<int>(row.Schema[0].Name)
```

A enumeração geral com LINQ tem o seguinte aspeto:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Após a enumerar os dois conjuntos de linhas, vamos percorrer todas as linhas. Para cada linha no conjunto de linhas à esquerda, vamos para localizar todas as linhas que satisfaçam a condição do nosso combinação.

Os valores de saída tem de ser definidos com `IUpdatableRow` saída.

```
output.Set<int>("mycolumn", mycolumn)
```

A saída real é acionada por chamar a `yield return output.AsReadOnly();`.

Segue-se um exemplo de combinação:

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

Neste cenário de caso de utilização, iremos está a criar um relatório de análise para o revendedor. O objetivo é encontrar todos os produtos que custo mais de 20.000 $ e que propor através do Web site mais rápido do que através do revendedor regular num determinado período de tempo.

Eis o script U-SQL base. Pode comparar a lógica entre uma associação regular e uma combinação:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Pode ser chamada uma combinação de definido pelo utilizador como uma nova instância do objeto applier:

```
USING new MyNameSpace.MyCombiner();
```


Ou com a invocação de um método de fábrica do dispositivo de moldagem:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Utilizar reducers definido pelo utilizador

U-SQL permite-lhe escrever reducers de conjunto de linhas personalizadas em c# utilizando a estrutura de extensibilidade de operador definido pelo utilizador e implementar uma interface IReducer.

Reducer definido pelo utilizador ou UDR, pode ser utilizado para eliminar linhas desnecessárias durante a extração de dados (importar). Também pode ser utilizado para manipular e avaliar linhas e colunas. Com base na lógica de programação para, também pode definir que linhas tem de ser extraído.

Para definir uma classe UDR, temos de criar um `IReducer` interface com opcional `SqlUserDefinedReducer` atributo.

Esta interface de classe deve conter uma definição para o `IEnumerable` substituir o conjunto de linhas de interface.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

O **SqlUserDefinedReducer** atributo indica que o tipo deve ser registado como um reducer definido pelo utilizador. Esta classe não pode ser herdada.
**SqlUserDefinedReducer** é um atributo opcional para uma definição de reducer definido pelo utilizador. É utilizado para definir a propriedade IsRecursive.

* bool IsRecursive    
* **Verdadeiro** = indica se esta Reducer é associative e commutative

Os objetos de programação para principais são **entrada** e **saída**. O objeto de entrada é utilizado para enumerar as linhas de entrada. Resultado é utilizado para definir as linhas de saída como resultado de reduzir a atividade.

Para a enumeração de linhas de entrada, utilizamos o `Row.Get` método.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

O parâmetro de `Row.Get` método é uma coluna que é transmitida como parte do `PRODUCE` classe do `REDUCE` declaração do script U-SQL base. É necessário utilizar o tipo de dados correto aqui bem.

Para a saída, utilize o `output.Set` método.

É importante compreender que reducer personalizado produz apenas valores que são definidos com o `output.Set` chamada de método.

```
output.Set<string>("mycolumn", guid);
```

A saída de reducer real é acionada ao chamar `yield return output.AsReadOnly();`.

Segue-se um exemplo de reducer:

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

Neste cenário de caso de utilização, o reducer está a ignorar as linhas com um nome de utilizador vazio. Para cada linha no conjunto de linhas, lê de cada coluna necessária, em seguida, avalia o comprimento do nome de utilizador. A linha real-produz apenas se o comprimento do valor de nome de utilizador é superior a 0.

Segue-se base script U-SQL que utiliza um reducer personalizado:

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
