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
ms.date: 03/27/2017
ms.author: ddove
ms.openlocfilehash: 2246dd12b922fcbc2e2b58890b3d56253810849c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="data-dependent-routing"></a>Encaminhamento dependente de dados
**Dados dependentes encaminhamento** é a capacidade de utilizar os dados numa consulta para encaminhar o pedido para uma base de dados adequada. Este é um padrão fundamental ao trabalhar com a bases de dados. O contexto do pedido também pode ser utilizado para encaminhar o pedido, especialmente se a chave de fragmentação não faz parte da consulta. Cada consulta específicas ou a transação de uma aplicação utilizar dados dependentes encaminhamento está restrita a aceder a uma base de dados individual por pedido. Para as ferramentas do Azure SQL da base de dados elásticas, este encaminhamento é efetuado com o  **[ShardMapManager classe](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)**  em aplicações do ADO.NET.

A aplicação não precisa de controlar várias localizações de BD associadas a diferentes setores de dados no ambiente em partição horizontal ou cadeias de ligação. Em vez disso, o [Gestor de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md) abre-se ligações para as bases de dados corretos quando for necessário, com base nos dados existentes o mapa de partições horizontais e o valor da chave de fragmentação que é o destino do pedido da aplicação. A chave é, geralmente, o *customer_id*, *tenant_id*, *date_key*, ou alguns outro identificador específico que é um parâmetro fundamental do pedido da base de dados). 

Para obter mais informações, consulte [dimensionamento fora do SQL Server com o encaminhamento de dados dependentes](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Transferir a biblioteca de clientes
Para obter a classe, instale o [biblioteca de clientes de base de dados elásticas](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). 

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Utilizar um ShardMapManager numa aplicação encaminhamento dependentes dados
As aplicações devem instanciar o **ShardMapManager** durante a inicialização, utilizando a chamada de fábrica  **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**. Neste exemplo, tanto um **ShardMapManager** e específico **ShardMap** nele contidos são inicializados. Este exemplo mostra o GetSqlShardMapManager e [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx) métodos.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Utilizar credenciais de privilégio mais baixas possíveis para obter o mapa de partições horizontais
Se uma aplicação não é manipular o mapa de partições horizontais próprio, as credenciais utilizadas no método de fábrica devem ter permissões apenas só de leitura **Global mapa de partições horizontais** base de dados. Estas credenciais são normalmente diferentes das credenciais utilizadas para abrir as ligações para o Gestor de mapa de partições horizontais. Consulte também [credenciais utilizadas para aceder a biblioteca de clientes de base de dados elástica](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>Chamar o método OpenConnectionForKey
O  **[ShardMap.OpenConnectionForKey método](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)**  devolve uma ligação do ADO.Net pronto para emitir comandos para a base de dados adequada com base no valor da **chave** parâmetro. Informações de partições horizontais é colocado em cache da aplicação pelo **ShardMapManager**, pelo que estes pedidos não normalmente envolvem uma pesquisa de base de dados contra o **Global mapa de partições horizontais** base de dados. 

    // Syntax: 
    public SqlConnection OpenConnectionForKey<TKey>(
        TKey key,
        string connectionString,
        ConnectionOptions options
    )


* O **chave** parâmetro é utilizado como uma chave de pesquisa para o mapa de partições horizontais para determinar a base de dados adequada para o pedido. 
* O **connectionString** é utilizado para transferir as credenciais de utilizador para a ligação pretendida. Nenhum nome de base de dados ou o nome do servidor estão incluídos neste *connectionString* , uma vez que o método irá determinar a base de dados e o servidor a utilizar o **ShardMap**. 
* O  **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)**  deve ser definido como **ConnectionOptions.Validate** se um ambiente onde mapas de partições horizontais pode alterar e linhas podem mover a outras bases de dados devido a operações de intercalação ou divisão. Isto envolve a uma consulta breve para o mapa de partições horizontais local no destino da base de dados (não para o mapa de partições horizontais global) antes da ligação é entregue à aplicação. 

Se a falha de validação de contra o mapa de partições horizontais local (com a indicação de que a cache está incorreta), o Gestor de mapa de partições horizontais irá consultar o mapa de partições horizontais global para obter o novo valor correto para a pesquisa, atualizar a cache e obter e voltar a ligação de base de dados adequado. 

Utilize **ConnectionOptions.None** apenas quando as alterações de mapeamento de partições horizontais não são esperadas enquanto uma aplicação está online. Nesse caso, os valores em cache podem assumir-se sempre esteja correto, e a chamada de validação reportadas round-trip extra para a base de dados de destino pode ser ignorada com segurança. Que reduz o tráfego de base de dados. O **connectionOptions** também pode ser definido através de um valor de um ficheiro de configuração para indicar se as alterações de fragmentação esperadas ou não durante um período de tempo.  

Este exemplo utiliza o valor da chave de um número inteiro **CustomerID**, utilizando um **ShardMap** objeto com o nome **customerShardMap**.  

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
    // constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

O **OpenConnectionForKey** método devolve uma nova ligação já aberta na base de dados correto. Ligações utilizadas desta forma continuar a tirar partido do agrupamento de ligação do ADO.Net. Desde que os pedidos de transações e podem ser satisfeitos por um ID de partição horizontal num momento, esta deve ser a modificação apenas necessária numa aplicação já ADO.Net a utilizar. 

O  **[OpenConnectionForKeyAsync método](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)**  também está disponível se a aplicação faz com que utilize programação assíncrona com ADO.Net. O comportamento é os dados encaminhamento equivalentes ADO dependentes. Do NET  **[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688\(v=vs.110\).aspx)**  método.

## <a name="integrating-with-transient-fault-handling"></a>Integração com o processamento de falhas transitórias
É uma prática de segurança no desenvolvimento de aplicações de acesso de dados na nuvem garantir que as falhas transitórias são detetadas pela aplicação e que as operações são repetidas várias vezes antes de gerar um erro. Erro transitório de processamento para aplicações em nuvem é abordado em [processamento de erros transitórios](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx). 

Processamento de falhas transitórias pode coexistir naturalmente com o padrão de dados dependentes encaminhamento. É o requisito-chave para repetir a incluindo de pedido de acesso do dados todos o **utilizando** bloco que obteve a ligação de encaminhamento de dados dependentes. O exemplo acima pode ser foi reescrito, da seguinte forma (alterar nota realçada). 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo - dependentes de encaminhamento com o processamento de falhas transitórias de dados
<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connect to the shard for a customer ID. 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Pacotes necessários para implementar o processamento de falhas transitórias serão transferidas automaticamente quando criar a aplicação de exemplo de bases de dados elásticas. Pacotes também estão disponíveis em separado no [Enterprise biblioteca - transitório falhas de processamento Application Block](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Utilize a versão 6.0 ou posterior. 

## <a name="transactional-consistency"></a>Consistência transacional
Propriedades transacionais garantidas para todas as operações locais para um ID de partição horizontal. Por exemplo, as transações submetidas através de encaminhamento de dados dependentes executar no âmbito do ID de partição horizontal destino para a ligação. Neste momento, não existem nenhum capacidades fornecidas para inscrever várias ligações para uma transação e, por conseguinte, não existem nenhum garantias transacionais para operações executadas em partições horizontais.

## <a name="next-steps"></a>Passos seguintes
Anular a exposição de um ID de partição horizontal ou reattach um ID de partição horizontal, consulte [utilizando a classe de RecoveryManager para corrigir problemas de mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

