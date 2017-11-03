---
title: Funcionalidades de JSON de base de dados SQL do Azure | Microsoft Docs
description: "Base de dados SQL do Azure permite-lhe analisar, consulta e dados do formato na notação de JavaScript Object Notation (JSON)."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 55860105-2f5f-4b10-87a0-99faa32b5653
ms.service: sql-database
ms.custom: develop databases
ms.devlang: NA
ms.date: 11/15/2016
ms.author: jovanpop
ms.workload: On Demand
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 8877b0bb779501df003ce11d66d9625c2f99e9ce
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Introdução às funcionalidades JSON na SQL Database do Azure
Azure base de dados SQL permite-lhe analisar e consulta dados representados no JavaScript Object Notation [(JSON)](http://www.json.org/) formatar e exportar os dados relacionais como texto JSON.

JSON é um formato de dados popular utilizado para trocar dados de aplicações móveis e web moderna. JSON também é utilizado para armazenar dados semiestruturados nos ficheiros de registo ou nas bases de dados NoSQL como [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). Muitos serviços web REST devolve resultados formatados como texto JSON ou aceitam dados formatados como JSON. Azure maioria dos serviços, tais como [da Azure Search](https://azure.microsoft.com/services/search/), [Storage do Azure](https://azure.microsoft.com/services/storage/), e [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) tem pontos finais REST que devolvem ou consumam JSON.

Base de dados SQL do Azure permite-lhe trabalhar facilmente com dados JSON e integrar a sua base de dados com serviços modernos.

## <a name="overview"></a>Descrição geral
Base de dados SQL do Azure fornece as seguintes funções para trabalhar com dados JSON:

![Funções JSON](./media/sql-database-json-features/image_1.png)

Se tiver texto JSON, pode utilizar para extrair dados JSON ou certifique-se de que JSON corretamente formatado utilizando as funções incorporadas [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), e [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). O [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) função permite-lhe atualizar valor texto JSON. Para mais avançadas de consulta e análise, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) função pode transformar uma matriz de objetos JSON para um conjunto de linhas. Qualquer consulta de SQL Server pode ser executada no conjunto de resultados devolvida. Por fim, há um [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) cláusula que lhe permite formatar os dados armazenados no seu tabelas relacionais como texto JSON.

## <a name="formatting-relational-data-in-json-format"></a>Formatação dados relacionais no formato JSON
Se tiver um serviço web, que demora a camada de dados da base de dados e fornece uma resposta no JSON formate ou bibliotecas que aceitam dados ou de estruturas de JavaScript do lado do cliente formatados como JSON, pode formatar o conteúdo da base de dados como JSON diretamente numa consulta SQL. Já não tem de escrever código de aplicação que formata os resultados da base de dados do Azure SQL Server como JSON ou incluir algumas bibliotecas de serialização JSON para converter os resultados da consulta de tabela e, em seguida, serializar objetos para o formato JSON. Em vez disso, pode utilizar a cláusula FOR JSON para formatar resultados da consulta SQL como JSON na SQL Database do Azure e utilizá-lo diretamente na sua aplicação.

No exemplo seguinte, as linhas da tabela da Sales.Customer estejam formatadas como JSON, utilizando a cláusula FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A cláusula FOR JSON PATH formata os resultados da consulta como texto JSON. Os nomes das colunas são utilizados como chaves, embora os valores das células são gerados como valores JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

O conjunto de resultados é formatado como uma matriz JSON em que cada linha é formatada como um objeto JSON separado.

CAMINHO indica que pode personalizar o formato de saída do resultado seu JSON utilizando a notação de pontos no aliases de coluna. A consulta seguinte altera o nome da chave "NomeDoCliente" no formato JSON saída e coloca os números de telefone e fax no objeto "Contacte" secundárias:

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

O resultado desta consulta tem o seguinte aspeto:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Neste exemplo é devolvido um único objeto JSON em vez de uma matriz, especificando o [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) opção. Pode utilizar esta opção se souber que está a devolver um único objeto como resultado da consulta.

O valor principal da cláusula FOR JSON é o que permite-lhe devolver dados hierárquicos complexo da sua base de dados formatado como objetos JSON aninhados ou matrizes. O exemplo seguinte mostra como as ordens que pertencem ao cliente como uma matriz aninhada das ordens de incluir:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Em vez de enviarem separar consultas para obter dados de cliente e, em seguida, para obter uma lista das ordens relacionadas, pode obter os dados necessários com uma única consulta, conforme mostrado no seguinte exemplo:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Trabalhar com dados JSON
Se não tiver dados estruturados estritamente, se tiver dados hierárquicos, matrizes ou secundárias de objetos complexos, ou se evoluem estruturas de dados ao longo do tempo, o formato JSON pode ajudá-lo para representar qualquer estrutura de dados complexas.

JSON é um formato de texto que pode ser utilizado como qualquer outro tipo de cadeia SQL Database do Azure. Pode enviar ou armazenar dados JSON como um padrão NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Os dados JSON utilizados neste exemplo são representados através do tipo nvarchar (Max). JSON pode ser inserido nesta tabela ou fornecido como um argumento do procedimento armazenado utilizando a sintaxe de Transact-SQL padrão, conforme mostrado no exemplo seguinte:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualquer linguagem do lado do cliente ou a biblioteca que funciona com dados de cadeia na SQL Database do Azure também irá trabalhar com dados JSON. JSON pode ser armazenado em qualquer tabela que suporta o tipo NVARCHAR, como uma tabela com otimização de memória ou uma tabela com versão do sistema. JSON não está a introduzir qualquer restrição com o código do lado do cliente ou na camada de base de dados.

## <a name="querying-json-data"></a>Consultar os dados JSON
Se tiver dados a formatados como JSON armazenado nas tabelas de SQL do Azure, as funções JSON permitem utilizar estes dados em qualquer consulta SQL.

As funções JSON que estão disponíveis no permitem de base de dados SQL do Azure que trate dados formatados como JSON como qualquer outro tipo de dados do SQL Server. Pode facilmente extrair os valores de texto JSON e utilizar dados JSON em qualquer consulta:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A função JSON_VALUE extrai um valor de texto JSON armazenado na coluna de dados. Esta função utiliza um caminho como o JavaScript para fazer referência a um valor no texto JSON, a extrair. Ao valor extraído pode ser utilizado em qualquer parte da consulta SQL.

A função JSON_QUERY é semelhante à JSON_VALUE. Ao contrário de JSON_VALUE, esta função extrai o objeto complexo secundárias como matrizes ou os objetos que são colocados no texto JSON.

A função JSON_MODIFY permite-lhe especificar o caminho do valor de texto JSON que deve ser atualizado, bem como um novo valor que irá substituir o antigo um. Desta forma, pode facilmente atualizar texto JSON sem reparsing a estrutura de toda.

Uma vez que o JSON é armazenado num texto padrão, não existem não garante que os valores armazenados nas colunas de texto são corretamente formatados. Pode verificar que armazenados JSON coluna de texto corretamente formatado utilizando o padrão restrições de verificação da SQL Database do Azure e a função ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se o texto de entrada está formatado corretamente JSON, a função ISJSON devolve o valor de 1. Em cada insert ou update da coluna JSON, esta restrição irá verificar que o novo valor de texto não for JSON com formato incorreto.

## <a name="transforming-json-into-tabular-format"></a>Transformar JSON em formato tabular
Base de dados SQL do Azure também permite-lhe transformar coleções de JSON para dados JSON de formato e a carga ou a consulta de tabela.

OPENJSON é uma função de valor de tabela que analisa o texto JSON, localiza uma matriz de objetos JSON, itera através de elementos da matriz e devolve uma linha no resultado de saída para cada elemento da matriz.

![Tabela de JSON](./media/sql-database-json-features/image_2.png)

No exemplo acima, podemos pode especificar onde localizar a matriz JSON que deve ser aberta (em $. Caminho de ordens), que colunas devem ser devolvidas como resultado e onde encontrar os valores JSON que vai ser devolvidos como células.

Iremos pode transformar uma matriz JSON no @orders variável para um conjunto de linhas, analisar este conjunto de resultados ou inserir linhas numa tabela padrão:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
A coleção de ordens formatados como uma matriz JSON e fornecido como parâmetro para o procedimento armazenado pode ser analisado e inserido na tabela ordens.

## <a name="next-steps"></a>Passos seguintes
Para saber como integrar o JSON na sua aplicação, consulte estes recursos:

* [Blogue do TechNet](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [Documentação de MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Canal de 9 de vídeo](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Para saber mais sobre os vários cenários para integrar o JSON na sua aplicação, consulte as demonstrações deste [Channel 9 vídeo](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encontrar um cenário que corresponda ao seu caso de utilização no [mensagens do blogue de JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

