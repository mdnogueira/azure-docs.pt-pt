---
title: Dados dependentes de encaminhamento com a SQL Database do Azure | Microsoft Docs
description: "Como utilizar a classe de ShardMapManager nas aplicações de .NET de dados dependentes do encaminhamento, uma funcionalidade de bases de dados em partição horizontal na SQL Database do Azure"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: cad09e15-5561-4448-aa18-b38f54cda004
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: ddove
ms.openlocfilehash: ef88d6072cfa95842703c31ec8e95ff4664f91ff
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="data-dependent-routing"></a>Encaminhamento dependente de dados
**Dados dependentes encaminhamento** é a capacidade de utilizar os dados numa consulta para encaminhar o pedido para uma base de dados adequada. Este é um padrão fundamental ao trabalhar com a bases de dados. O contexto do pedido também pode ser utilizado para encaminhar o pedido, especialmente se a chave de fragmentação não faz parte da consulta. Cada consulta específicas ou a transação de uma aplicação utilizar dados dependentes encaminhamento está restrita a aceder a uma base de dados individual por pedido. Para as ferramentas do Azure SQL da base de dados elásticas, este encaminhamento é efetuado com o **ShardMapManager** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager)) classe.

A aplicação não precisa de controlar várias localizações de BD associadas a diferentes setores de dados no ambiente em partição horizontal ou cadeias de ligação. Em vez disso, o [Gestor de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md) abre-se ligações para as bases de dados corretos quando for necessário, com base nos dados existentes o mapa de partições horizontais e o valor da chave de fragmentação que é o destino do pedido da aplicação. A chave é, geralmente, o *customer_id*, *tenant_id*, *date_key*, ou alguns outro identificador específico que é um parâmetro fundamental do pedido da base de dados). 

Para obter mais informações, consulte [dimensionamento fora do SQL Server com o encaminhamento de dados dependentes](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Transferir a biblioteca de clientes
Para transferir:
* a versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).
* a versão de Java da biblioteca, consulte [repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Utilizar um ShardMapManager numa aplicação encaminhamento dependentes dados
As aplicações devem instanciar o **ShardMapManager** durante a inicialização, utilizando a chamada de fábrica **GetSQLShardMapManager** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx), [Java ](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager)). Neste exemplo, tanto um **ShardMapManager** e específico **ShardMap** nele contidos são inicializados. Este exemplo mostra o GetSqlShardMapManager e GetRangeShardMap ([.NET](https://msdn.microsoft.com/library/azure/dn824173.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getrangeshardmap)) métodos.

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Utilizar credenciais de privilégio mais baixas possíveis para obter o mapa de partições horizontais
Se uma aplicação não é manipular o mapa de partições horizontais próprio, as credenciais utilizadas no método de fábrica devem ter permissões apenas só de leitura **Global mapa de partições horizontais** base de dados. Estas credenciais são normalmente diferentes das credenciais utilizadas para abrir as ligações para o Gestor de mapa de partições horizontais. Consulte também [credenciais utilizadas para aceder a biblioteca de clientes de base de dados elástica](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>Chamar o método OpenConnectionForKey
O **ShardMap.OpenConnectionForKey método** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey)) devolve uma ligação pronto para emitir comandos para a base de dados adequada com base no valor da **chave** parâmetro. Informações de partições horizontais é colocado em cache da aplicação pelo **ShardMapManager**, pelo que estes pedidos não normalmente envolvem uma pesquisa de base de dados contra o **Global mapa de partições horizontais** base de dados. 

```csharp
// Syntax: 
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* O **chave** parâmetro é utilizado como uma chave de pesquisa para o mapa de partições horizontais para determinar a base de dados adequada para o pedido. 
* O **connectionString** é utilizado para transferir as credenciais de utilizador para a ligação pretendida. Nenhum nome de base de dados ou o nome do servidor estão incluídos neste *connectionString* , uma vez que o método irá determinar a base de dados e o servidor a utilizar o **ShardMap**. 
* O **connectionOptions** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._connection_options)) deve ser definido como **ConnectionOptions.Validate** se um ambiente onde podem os mapas de partições horizontais linhas e de alterações podem mover a outras bases de dados devido a operações de intercalação ou divisão. Isto envolve a uma consulta breve para o mapa de partições horizontais local no destino da base de dados (não para o mapa de partições horizontais global) antes da ligação é entregue à aplicação. 

Se a falha de validação de contra o mapa de partições horizontais local (com a indicação de que a cache está incorreta), o Gestor de mapa de partições horizontais irá consultar o mapa de partições horizontais global para obter o novo valor correto para a pesquisa, atualizar a cache e obter e voltar a ligação de base de dados adequado. 

Utilize **ConnectionOptions.None** apenas quando as alterações de mapeamento de partições horizontais não são esperadas enquanto uma aplicação está online. Nesse caso, os valores em cache podem assumir-se sempre esteja correto, e a chamada de validação reportadas round-trip extra para a base de dados de destino pode ser ignorada com segurança. Que reduz o tráfego de base de dados. O **connectionOptions** também pode ser definido através de um valor de um ficheiro de configuração para indicar se as alterações de fragmentação esperadas ou não durante um período de tempo.  

Este exemplo utiliza o valor da chave de um número inteiro **CustomerID**, utilizando um **ShardMap** objeto com o nome **customerShardMap**.  

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

// Connect to the shard for that customer ID. No need to call a SqlConnection 
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
{ 
    // Execute a simple command. 
    SqlCommand cmd = conn.CreateCommand(); 
    cmd.CommandText = @"UPDATE Sales.Customer 
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID"; 

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
    cmd.ExecuteNonQuery(); 
}  
```

O **OpenConnectionForKey** método devolve uma nova ligação já aberta na base de dados correto. Ligações utilizadas desta forma continuar a tirar partido do agrupamento de ligações. 

O **OpenConnectionForKeyAsync método** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkeyasync)) também está disponível se a aplicação faz com que utilize programação assíncrona com ADO.Net.

## <a name="integrating-with-transient-fault-handling"></a>Integração com o processamento de falhas transitórias
É uma prática de segurança no desenvolvimento de aplicações de acesso de dados na nuvem garantir que as falhas transitórias são detetadas pela aplicação e que as operações são repetidas várias vezes antes de gerar um erro. Erro transitório de processamento para aplicações em nuvem é abordado no processamento de erros transitórios ([.NET](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx), [Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling)). 

Processamento de falhas transitórias pode coexistir naturalmente com o padrão de dados dependentes encaminhamento. É o requisito-chave para repetir a incluindo de pedido de acesso do dados todos o **utilizando** bloco que obteve a ligação de encaminhamento de dados dependentes. O exemplo acima pode ser foi reescrito, da seguinte forma (alterar nota realçada). 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo - dependentes de encaminhamento com o processamento de falhas transitórias de dados
```csharp
int customerId = 12345; 
int newPersonId = 4321; 

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; 
{
    // Connect to the shard for a customer ID. 
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 

        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 

        Console.WriteLine("Update completed"); 
    } 
}); 
```


Pacotes necessários para implementar o processamento de falhas transitórias serão transferidas automaticamente quando criar a aplicação de exemplo de bases de dados elásticas. 

## <a name="transactional-consistency"></a>Consistência transacional
Propriedades transacionais garantidas para todas as operações locais para um ID de partição horizontal. Por exemplo, as transações submetidas através de encaminhamento de dados dependentes executar no âmbito do ID de partição horizontal destino para a ligação. Neste momento, não existem nenhum capacidades fornecidas para inscrever várias ligações para uma transação e, por conseguinte, não existem nenhum garantias transacionais para operações executadas em partições horizontais.

## <a name="next-steps"></a>Passos seguintes
Anular a exposição de um ID de partição horizontal ou reattach um ID de partição horizontal, consulte [utilizando a classe de RecoveryManager para corrigir problemas de mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

