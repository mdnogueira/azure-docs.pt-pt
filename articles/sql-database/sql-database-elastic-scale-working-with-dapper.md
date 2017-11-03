---
title: "Com a biblioteca de clientes de base de dados elástica Dapper | Microsoft Docs"
description: "Utilizar a biblioteca de cliente de bases de dados elásticas com Dapper."
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: 463d2676-3b19-47c2-83df-f8c50492c9d2
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.openlocfilehash: c258b1859e14d9783a3dfa75431b69bef4d640fd
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Com a biblioteca de cliente de bases de dados elásticas com Dapper
Este documento destina-se que os programadores que dependem Dapper para criar aplicações, mas também querem aderir ao [ferramentas de base de dados elástica](sql-database-elastic-scale-introduction.md) para criar aplicações que fragmentação de implementar para ampliar a respetiva camada de dados.  Este documento ilustra as alterações nas aplicações baseadas em Dapper que são necessárias para integrar com ferramentas de base de dados elásticas. O nosso foco-se a gestão de partições horizontais de base de dados elástica e o encaminhamento de dados dependentes com Dapper de composição. 

**Código de exemplo**: [ferramentas de base de dados elástica para o SQL Database do Azure - integração Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrar **Dapper** e **DapperExtensions** com a base de dados elástica é fácil a biblioteca de cliente para a SQL Database do Azure. As aplicações podem utilizar o encaminhamento do dependentes de dados ao alterar a criação e abrir de novo [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos para utilizar o [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) chamada a partir do [biblioteca de clientes ](http://msdn.microsoft.com/library/azure/dn765902.aspx). Isto limita as alterações na sua aplicação apenas onde novas ligações são criadas e abertas. 

## <a name="dapper-overview"></a>Descrição geral do dapper
**Dapper** é um objeto relacional mapeador. Mapas de objetos de .NET a partir da sua aplicação para uma base de dados relacional (e vice-versa). A primeira parte do código de exemplo ilustra como pode integrar a biblioteca de clientes de base de dados elásticas com aplicações baseadas em Dapper. A segunda parte do código de exemplo ilustra como integrar ao utilizar Dapper e DapperExtensions.  

A funcionalidade do mapeador do Dapper fornece métodos de extensão em ligações de base de dados que simplificam a submeter instruções T-SQL para execução ou consultar a base de dados. Por exemplo, Dapper torna mais fácil para mapeamento entre os objetos de .NET e os parâmetros de instruções SQL para **executar** chamadas, ou para consumir os resultados das suas consultas SQL para objetos .NET utilizando **consulta** chamadas a partir Dapper. 

Quando utilizar DapperExtensions, já não precisam de fornecer as instruções SQL. Métodos de extensões, tais como **GetList** ou **inserir** através da ligação de base de dados criar as instruções SQL em segundo plano.

Outra vantagem de Dapper e também DapperExtensions é que a aplicação controla a criação da ligação da base de dados. Isto ajuda a interagir com a biblioteca de cliente de base de dados elástica que as ligações com base no mapeamento de shardlets às bases de dados da base de dados de mediadores.

Para obter as assemblagens Dapper, consulte [Dapper dot net](http://www.nuget.org/packages/Dapper/). Para as extensões Dapper, consulte [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Ver rapidamente a biblioteca de clientes de base de dados elástica
Com a biblioteca de clientes de base de dados elástica, é possível definir partições dos seus dados de aplicação chamados *shardlets*, mapeá-los para bases de dados além de distingui-los por *chaves de fragmentação*. Pode ter como muitas bases de dados conforme necessário e distribuir o shardlets entre estas bases de dados. O mapeamento dos valores de chave de fragmentação para as bases de dados é armazenado por um mapa de partições horizontais fornecido pelo APIs da biblioteca. Esta capacidade é denominada **gestão de mapa de partições horizontais**. O mapa de partições horizontais também serve como o Mediador de ligações de base de dados para pedidos que tem uma chave de fragmentação. Esta capacidade é denominada **encaminhamento de dados dependentes**.

![Mapas de partições horizontais e o encaminhamento de dados dependentes][1]

O Gestor de mapa de partições horizontais protege os utilizadores de vistas inconsistentes em shardlet dados que podem ocorrer quando estavam a acontecer nas bases de dados em simultâneo shardlet operações de gestão. Para tal, os maps de partições horizontais Mediador de ligações de base de dados para uma aplicação construída com a biblioteca. Quando as operações de gestão de partições horizontais pode afetar o shardlet, isto permite que a funcionalidade de mapa de partições horizontais eliminar automaticamente uma ligação de base de dados. 

Em vez de utilizar a forma tradicional de criar ligações para Dapper, tem de utilizar o [OpenConnectionForKey método](http://msdn.microsoft.com/library/azure/dn824099.aspx). Isto garante que todos os validação ocorre e as ligações são corretamente geridas quando move os dados entre shards.

### <a name="requirements-for-dapper-integration"></a>Requisitos para a integração Dapper
Ao trabalhar com a biblioteca de clientes de base de dados elástica e as APIs Dapper, que pretenda manter as seguintes propriedades:

* **Aumentar horizontalmente**: queremos adicionar ou remover bases de dados da camada de dados da aplicação em partição horizontal conforme necessário para os pedidos de capacidade da aplicação. 
* **Consistência**: uma vez que a aplicação é ampliada utilizando fragmentação, terá de efetuar o encaminhamento de dados dependentes. Queremos utilizar as capacidades de encaminhamento de dados dependentes da biblioteca para o fazer. Em particular, que pretenda manter a validação e garante a consistência fornecida pelas ligações mediadas através do Gestor de mapa de partições horizontais para evitar danos ou os resultados da consulta errado. Isto garante que as ligações a uma determinada shardlet são rejeitadas ou paradas se (por exemplo) a shardlet atualmente for movido para um ID de partição horizontal diferentes com APIs de divisão/intercalação.
* **Mapeamento de objeto**: Queremos manter a conveniência de mapeamentos fornecida pelo Dapper traduzir entre classes na aplicação e as estruturas de base de dados subjacente. 

A seguinte secção fornece orientação para os requisitos seguintes para aplicações com base nas **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Documentação de orientação técnica
### <a name="data-dependent-routing-with-dapper"></a>Encaminhamento de dados dependentes com Dapper
Com Dapper, a aplicação é normalmente responsável pela criação e abrir as ligações à base de dados subjacente. Um tipo T indicado pela aplicação, Dapper devolve os resultados da consulta como coleções de .NET do tipo T. Dapper efetua o mapeamento das linhas de resultado de T-SQL para os objetos do tipo T. Da mesma forma, Dapper mapeia objetos .NET para parâmetros de instruções de idioma (DML) de manipulação de dados ou valores SQL. Dapper oferece esta funcionalidade através de métodos de extensão no regular [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objeto a partir das bibliotecas de cliente de SQL do ADO .NET. A ligação SQL devolvida pelo APIs de dimensionamento flexível para DDR também são regular [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos. Isto permite-na utilização de diretamente extensões Dapper sobre o tipo devolvido pela API de DDR da biblioteca de clientes, como também se trata de uma ligação de cliente do SQL Server simple.

Estes observações torná-lo simples utilizar ligações mediadas pela biblioteca de clientes de base de dados elástica para Dapper.

Este exemplo de código (do exemplo associado) ilustra a abordagem em que a chave de fragmentação é fornecida pela aplicação para a biblioteca de Mediador a ligação para o ID de partição horizontal direita.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

A chamada para o [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API substitui a criação de predefinição e a abertura de uma ligação de cliente do SQL Server. O [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) chamada assume os argumentos que sejam necessários para o encaminhamento de dados dependentes: 

* O mapa de partições horizontais para aceder as interfaces de encaminhamento de dados dependentes
* A chave de fragmentação para identificar o shardlet
* As credenciais (nome de utilizador e palavra-passe) para estabelecer ligação com o ID de partição horizontal

O objeto de mapa de partições horizontais cria uma ligação para o ID de partição horizontal que contém o shardlet para a chave de fragmentação especificado. O cliente de base de dados elástica APIs Etiquetar também a ligação ao implementar as garantias de consistência. A chamada para [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) devolve um objeto de ligação de cliente do SQL Server regular, a chamada subsequente para o **executar** método de extensão de Dapper segue a prática Dapper padrão.

As consultas funcionam de forma muito semelhante à - primeira vez que abrir a ligação utilizando [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) do cliente de API. Em seguida, utilize os métodos de extensão Dapper regular para mapear os resultados da sua consulta SQL em objetos .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Tenha em atenção que o **utilizando** bloquear com os âmbitos de ligação de DDR todas as operações de base de dados dentro do bloco de para o ID de partição um horizontal onde tenantId1 é mantida. A consulta devolve apenas blogues armazenados no ID de partição horizontal atual, mas não as armazenados em qualquer outros shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Encaminhamento de dados dependentes com Dapper e DapperExtensions
Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais conveniência e abstração da base de dados quando desenvolver aplicações de base de dados. DapperExtensions é um exemplo. 

Utilizar DapperExtensions na sua aplicação não se altera como ligações de base de dados são criadas e geridas. Ainda é responsabilidade da aplicação para abrir as ligações e objetos de ligação do SQL Client regulares são esperados através dos métodos de extensão. Pode precisamos do [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) conforme descrito acima. Como mostram os exemplos de código seguinte, a única alteração é que já não terá de escrever as declarações de T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E Eis o exemplo de código para a consulta: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Processamento de falhas transitórias
A equipa do Microsoft Patterns & práticas publicado a [transitório falhas de processamento Application Block](http://msdn.microsoft.com/library/hh680934.aspx) para ajudar os programadores de aplicações mitigar condições de erro transitório comuns encontradas quando em execução na nuvem. Para obter mais informações, consulte [Perseverance, o segredo de todos os Triumphs: utilizando o bloco de aplicação de processamento de falhas transitórias](http://msdn.microsoft.com/library/dn440719.aspx).

O exemplo de código baseia-se na biblioteca para proteção contra falhas transitórias erro transitório. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10 e 5 segundos de tempo entre tentativas de espera. Se estiver a utilizar transações, certifique-se de que o âmbito de repetição fica voltar ao início da transação no caso de uma falha transitória.

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento entail algumas limitações:

* O código de exemplo para este documento não demonstram como gerir o esquema através de partições horizontais.
* Tendo em conta um pedido, partimos do princípio que todo o seu processamento da base de dados está contido dentro de um ID de partição horizontal único, conforme identificado pelo fragmentação chave fornecida pelo pedido. No entanto, este pressuposto sempre tiver, por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isto, a biblioteca de clientes de base de dados elástica inclui o [MultiShardQuery classe](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa uma abstração de ligação para consultas através de várias partições horizontais. Utilizar MultiShardQuery em combinação com Dapper está fora do âmbito deste documento.

## <a name="conclusion"></a>Conclusão
As aplicações que utilizam Dapper e DapperExtensions facilmente podem beneficiar de ferramentas de base de dados elástica para a SQL Database do Azure. Os passos descritos neste documento, essas aplicações podem utilizar a capacidade da ferramenta para o encaminhamento de dados dependentes ao alterar a criação e abrir de novo [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos para utilizar o [ OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) chamada da biblioteca de clientes de base de dados elásticas. Isto limita as alterações de aplicação necessárias para os locais onde novas ligações são criadas e abertas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
