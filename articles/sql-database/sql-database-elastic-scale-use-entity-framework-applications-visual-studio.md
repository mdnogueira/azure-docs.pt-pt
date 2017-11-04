---
title: "Com a biblioteca de cliente de bases de dados elásticas com o Entity Framework | Microsoft Docs"
description: "Utilizar a biblioteca de clientes de base de dados elástica e do Entity Framework para codificação bases de dados"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: b9c3065b-cb92-41be-aa7f-deba23e7e159
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: torsteng
ms.openlocfilehash: 1fc61657419f1f4581c5c67639d7bc2e4b0d509f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteca de clientes de base de dados elástica com o Entity Framework
Este documento mostra as alterações de uma aplicação do Entity Framework, que são necessários para integrar com o [ferramentas de base de dados elástica](sql-database-elastic-scale-introduction.md). O foco incide na composição [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md) e [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md) com o Entity Framework **Code First** abordagem. O [Code primeiro - nova base de dados](http://msdn.microsoft.com/data/jj193542.aspx) tutorial para EF funciona como o exemplo em execução ao longo deste documento. O código de exemplo que acompanha este documento faz parte das ferramentas de base de dados elástica conjunto de exemplos exemplos de código do Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Transferir e executar o código de exemplo
Para transferir o código para este artigo:

* É necessário o Visual Studio 2012 ou posterior. 
* Transferir o [ferramentas de base de dados elástica para o Azure SQL - exemplo de integração do Entity Framework](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) da MSDN. Deszipe o exemplo para uma localização da sua escolha.
* Inicie o Visual Studio. 
* No Visual Studio, selecione o ficheiro -> projeto/solução aberta. 
* No **abrir projeto** caixa de diálogo, navegue para o exemplo que transferiu e selecione **EntityFrameworkCodeFirst.sln** para abrir o exemplo. 

Para executar o exemplo, terá de criar três bases de dados vazios na SQL Database do Azure:

* Base de dados de Gestor de mapa de partições horizontais
* Base de dados de partições horizontais 1
* Base de dados de partição horizontal 2

Assim que tiver criado estas bases de dados, preencha os proprietários local no **Program.cs** com o nome do servidor de BD SQL do Azure, os nomes de base de dados e as suas credenciais para ligar às bases de dados. Compilar a solução no Visual Studio. Visual Studio transfere os NuGet pacotes necessários para a biblioteca de clientes de base de dados elástica do Entity Framework e o erro transitório processamento como parte do processo de compilação. Certifique-se de que o restauro de pacotes NuGet está ativado para a sua solução. Pode ativar esta definição ao clicar no ficheiro de solução no Explorador de soluções do Visual Studio. 

## <a name="entity-framework-workflows"></a>Fluxos de trabalho do Entity Framework
Os programadores do Entity Framework baseiam-se dos seguintes quatro fluxos de trabalho para criar aplicações e garantir a persistência para objectos da aplicação: 

* **Code First (nova base de dados)**: programador a EF cria o modelo no código da aplicação e, em seguida, EF gera a base de dados do mesmo. 
* **Code First (base de dados existente)**: O programador permite EF gerar código da aplicação para o modelo a partir de uma base de dados existente.
* **Modelo primeiro**: O programador cria o modelo no EF designer e, em seguida, EF cria a base de dados do modelo.
* **Base de dados primeiro**: O programador utiliza EF ferramentas para inferir o modelo de base de dados existente. 

Todas estas abordagens baseiam-se na classe DbContext para gerir de forma transparente ligações de base de dados e o esquema de base de dados para uma aplicação. Permitir que diferentes construtores na classe DbContext base para diferentes níveis de controlo sobre a criação da ligação, base de dados bootstrapping e criação de esquema. Desafios surgem principalmente do facto de que a gestão de ligação de base de dados fornecida pelo EF intersetar-se com as capacidades de gestão de ligação das interfaces de encaminhamento de dados dependentes fornecidas pela biblioteca de clientes de base de dados elásticas. 

## <a name="elastic-database-tools-assumptions"></a>Pressupostos de ferramentas de base de dados elástica
Para definições de prazo, consulte [Glossário de ferramentas de base de dados elástica](sql-database-elastic-scale-glossary.md).

Com biblioteca de clientes de base de dados elástica, defina partições dos seus dados de aplicação chamados shardlets. Shardlets são identificados por uma chave de fragmentação e estão mapeadas para as bases de dados específicas. Uma aplicação poderá ter como muitas bases de dados conforme necessário e distribuir shardlets para fornecer suficiente capacidade ou de desempenho fornecidos requisitos de negócio atuais. O mapeamento dos valores de chave de fragmentação para as bases de dados é armazenado por um mapa de partições horizontais fornecido pelo cliente de base de dados elástica APIs. Esta capacidade é denominada **gestão de mapa de partições horizontais**, ou SMM para abreviar. O mapa de partições horizontais também serve como o Mediador de ligações de base de dados para pedidos que tem uma chave de fragmentação. Esta capacidade é conhecida como **encaminhamento de dados dependentes**. 

O Gestor de mapa de partições horizontais protege os utilizadores de vistas inconsistentes em shardlet dados que podem ocorrer quando shardlet simultâneas operações de gestão (tais como a alteração da localização da dados de um ID de partição horizontal para outro) estão a ocorrer. Para tal, os maps de partições horizontais geridos pelo Mediador de biblioteca de cliente, as ligações de base de dados para uma aplicação. Isto permite que a funcionalidade de mapa de partições horizontais eliminar automaticamente uma ligação de base de dados quando as operações de gestão de partições horizontais pode afetar o shardlet que foi criada para a ligação. Esta abordagem tem de integrar com algumas das funcionalidades do EF, tais como criar novas ligações a partir de um existente para verificar a existência de base de dados. Em geral, a nossa observação foi que os construtores DbContext padrão só funcionam fiável para ligações de base de dados fechada que podem ser Clonadas para EF funcionam. O princípio da estrutura de base de dados elástica em vez disso, é apenas Mediador de ligações foi abertas. Um pensa que fechar uma ligação mediada pela biblioteca de clientes antes de oelo-lo a DbContext EF poderá resolver este problema. No entanto, ao fechar a ligação e depender EF para abri-lo novamente, uma foregoes as verificações de validação e consistência executadas pela biblioteca. No entanto, a funcionalidade de migrações do EF, utiliza estas ligações para gerir o esquema de base de dados subjacente, de forma que é transparente para a aplicação. Idealmente, irá manter e combine todas estas capacidades da biblioteca de clientes de base de dados elástica e EF na mesma aplicação. A secção seguinte explica estas propriedades e os requisitos mais detalhadamente. 

## <a name="requirements"></a>Requisitos
Ao trabalhar com a biblioteca de clientes de base de dados elástica e a APIs do Entity Framework, que pretenda manter as seguintes propriedades: 

* **Escalamento horizontal**: para adicionar ou remover bases de dados da camada de dados da aplicação em partição horizontal conforme necessário para os pedidos de capacidade da aplicação. Isto significa que o controlo sobre a criação e eliminação de bases de dados e utilizar o Gestor de mapa de partições horizontais de base de dados elástica APIs para gerir bases de dados e mapeamentos de shardlets. 
* **Consistência**: A aplicação emprega fragmentação e utiliza as capacidades de encaminhamento de dados dependentes da biblioteca de clientes. Para evitar danos ou os resultados da consulta errado, as ligações são mediadas através do Gestor de mapa de partições horizontais. Isto mantém também validação e consistência.
* **Code First**: para manter a conveniência de paradigma primeiro de código do EF. No Code First classes na aplicação estão mapeadas transparente para as estruturas de base de dados subjacente. O código de aplicação interage com DbSets mascarar a maioria dos aspetos envolvidos no processamento da base de dados subjacente.
* **Esquema**: processa a criação de esquema de base de dados inicial e a evolução de esquema subsequentes através de migrações do Entity Framework. Ao manter estas capacidades, a adaptação da aplicação é fácil à medida que evolui de dados. 

As seguintes orientações dá instruções ao como satisfazer os requisitos seguintes para aplicações Code First utilizando ferramentas de base de dados elásticas. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Encaminhamento com EF DbContext dados dependentes
Ligações de base de dados com o Entity Framework, normalmente, são geridas através das subclasses de **DbContext**. Crie estas subclasses ao efetuar a derivação de **DbContext**. Este é onde definir o **DbSets** que implementem as coleções de segurança da base de dados de objetos CLR para a sua aplicação. No contexto de dados dependentes encaminhamento, pode identificar várias propriedades útil que não contêm necessariamente para obter outros cenários de aplicação primeiro do EF código: 

* A base de dados já existe e foi registado no mapa de partições horizontais bases de dados elásticas. 
* O esquema da aplicação já foi implementado para a base de dados (explicado abaixo). 
* Ligações de encaminhamento de dados dependentes para a base de dados são mediadas pelo mapa de partições horizontais. 

Para integrar **DbContexts** com o encaminhamento de dados dependentes para Escalamento horizontal:

1. Criar ligações de base de dados física através de interfaces de cliente da base de dados elástica do Gestor de mapa de partições horizontais 
2. Encapsular a ligação com o **DbContext** subclasse
3. Transmita a ligação para baixo para a **DbContext** base classes para garantir que todos os o processamento do lado do EF acontece bem. 

Exemplo de código seguinte ilustra esta abordagem. (Este código é também no projeto de Visual Studio associado)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data-dependent routing. This call opens a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call fails for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Pontos de principais
* Um novo construtor substitui o construtor predefinido na subclasse DbContext 
* Este novo construtor assume os argumentos que sejam necessários para o encaminhamento de dados dependentes através da biblioteca de clientes de base de dados elástica:
  
  * o mapa de partições horizontais para as interfaces de encaminhamento de dados dependentes, de acesso
  * a chave de fragmentação para identificar o shardlet
  * uma cadeia de ligação com as credenciais para a ligação de encaminhamento de dados dependentes para as partições horizontais. 
* A chamada para o construtor de classe base tem um detour num método estático que executa todos os passos necessários para o encaminhamento de dados dependentes. 
  
  * Utiliza a chamada de OpenConnectionForKey das interfaces de cliente de base de dados elástica no mapa de partições horizontais para estabelecer uma ligação aberta.
  * O mapa de partições horizontais cria a ligação aberta para o ID de partição horizontal que contém o shardlet para a chave de fragmentação especificado.
  * Esta ligação aberta é reenviada para o construtor de classe base do DbContext para indicar que esta ligação está a ser utilizado pelo EF em vez de permitir que o EF criar automaticamente uma nova ligação. Desta forma, a ligação tem sido etiquetada pelo cliente de base de dados elástica API, de modo a que pode garantir consistência em operações de gestão de mapa de partições horizontais.

Utilize o construtor de novo para a subclasse DbContext em vez do construtor predefinido no seu código. Segue-se um exemplo: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Este novo construtor abre-se a ligação para o ID de partição horizontal que contém os dados para o shardlet identificada pelo valor **tenantid1**. O código no **utilizando** bloco permanece inalterado para acesso a **DbSet** para os blogues utilizando EF nas partições horizontais para **tenantid1**. Isto altera a semântica para o código em utilizando bloquear como todas as operações de base de dados estão agora confinadas para o ID de partição um horizontal onde **tenantid1** é mantida. Por exemplo, uma consulta LINQ através de blogues **DbSet** apenas devolvam blogues armazenados no ID de partição horizontal atual, mas não as armazenados nos outros shards.  

#### <a name="transient-faults-handling"></a>Processamento de falhas transitórias
A equipa do Microsoft Patterns & práticas publicado a [o transitório falhas de processamento Application Block](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é utilizada com a biblioteca de clientes do dimensionamento elástico em combinação com EF. No entanto, certifique-se de que qualquer excepção transitória devolve para um local onde pode garantir que está a ser utilizado o construtor de novo após um erro transitório para que qualquer nova tentativa de ligação é efetuada utilizando os construtores que tweaked. Caso contrário, uma ligação para o ID de partição horizontal correta não é garantida, e existem não oferece garantias ao rendimento que a ligação é mantida tal como as alterações para o mapa de partições horizontais. 

O exemplo de código seguinte ilustra como uma política de repetição do SQL Server pode ser utilizada em torno da nova **DbContext** construtores subclasse: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10 e 5 segundos de tempo entre tentativas de espera. Esta abordagem é semelhante para a documentação de orientação para EF e transações iniciada pelo utilizador (consulte [limitações com repetir estratégias de execução (EF6 em diante)](http://msdn.microsoft.com/data/dn307226). Ambas as situações necessitam que o programa de aplicação controla o âmbito para o qual a exceção transitória devolve: volte a abrir a transação ou (conforme mostrado) recriar o contexto do construtor adequado que utiliza a biblioteca de clientes de base de dados elásticas.

A necessidade para controlar onde exceções transitórias demorar-no âmbito também sequência exclui a utilização do incorporado **SqlAzureExecutionStrategy** que vem com EF. **SqlAzureExecutionStrategy** seria volte a abrir uma ligação, mas utiliza **OpenConnectionForKey** e, por conseguinte, ignorar a validação que seja efetuada como parte do **OpenConnectionForKey**chamada. Em vez disso, o código de exemplo utiliza o incorporado **DefaultExecutionStrategy** que também inclui EF. Como opposed para **SqlAzureExecutionStrategy**, que funciona corretamente em combinação com a política de repetição de processamento de erros transitórios. A política de execução está definida **ElasticScaleDbConfiguration** classe. Tenha em atenção que decidimos para não utilizar **DefaultSqlExecutionStrategy** desde que sugere utilizando **SqlAzureExecutionStrategy** se ocorrerem exceções transitórias - que iria causar comportamento errado, tal como descrito. Para obter mais informações sobre as políticas de repetição diferentes e EF, consulte [resiliência de ligação no EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Construtor reescritas
Os exemplos de código acima mostram a predefinição construtor reescreve necessário para a sua aplicação para utilizar o encaminhamento de dados dependentes com o Entity Framework. A tabela seguinte generaliza esta abordagem para outros construtores. 

| Construtor atual | Construtor de conversão de dados | Construtor base | Notas |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A ligação tem de ser uma função de mapa de partições horizontais e a chave de encaminhamento de dados dependentes. Tem de criação de ligação automática de ignorar por EF e em vez disso, utilize o mapa de partições horizontais para mediador a ligação. |
| MyContext(string) |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A ligação é uma função do mapa de partições horizontais e a chave de encaminhamento de dados dependentes. Uma cadeia de ligação ou nome de base de dados fixa não funcionar à medida que a validação de ignorar pelo mapa de partições horizontais. |
| MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A ligação é criada para a chave de mapa e a fragmentação de determinada partição horizontal com o modelo fornecido. O modelo compilado é transmitido para o c'tor base. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |A ligação tem de ser inferidos a partir do mapa de partições horizontais e a chave. Não pode ser fornecido como entrada (exceto se essa entrada já estava a utilizar o mapa de partições horizontais e a chave). O valor boleano é transmitido. |
| MyContext (cadeia, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A ligação tem de ser inferidos a partir do mapa de partições horizontais e a chave. Não pode ser fornecido como entrada (exceto se essa entrada estava a utilizar o mapa de partições horizontais e a chave). O modelo compilado é transmitido. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Este novo construtor tem Certifique-se de que todas as ligações no ObjectContext transmitido como uma entrada novamente encaminhada para uma ligação gerida pelo horizontal elástico. Ver um debate detalhado dos ObjectContexts está fora do âmbito deste documento. |
| MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |A ligação tem de ser inferidos a partir do mapa de partições horizontais e a chave. A ligação não pode ser fornecida como entrada (exceto se essa entrada já estava a utilizar o mapa de partições horizontais e a chave). Modelo e o valor boleano são transmitido a este construtor de classe base. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implementação de esquema de partições horizontais através de migrações do EF
Gestão automática de esquema está para efeitos práticos fornecido o Entity Framework. No contexto das aplicações a utilizar as ferramentas de base de dados elástica, que pretenda manter esta capacidade para aprovisionar automaticamente o esquema para shards recentemente criados quando são adicionadas as bases de dados para a aplicação em partição horizontal. O caso de utilização principal é aumentar a capacidade na camada de dados para aplicações em partição horizontal com EF. Entidade confiadora nas capacidades do EF para a gestão de esquema reduz o esforço de administração de base de dados com uma aplicação em partição horizontal incorporada no EF. 

Implementação de esquema através de migrações do EF funciona melhor no **não aberta ligações**. Este é contrariamente cenário para o encaminhamento de dados dependentes que se baseia na ligação aberta fornecida pelo cliente de base de dados elástica API. Diferença outra é o requisito de consistência: ao desejável para garantir consistência para todas as ligações de encaminhamento de dados dependentes proteger contra manipulação de mapa de partições horizontais em simultâneo, não é uma preocupação com inicial base de dados de implementação de esquema para um novo tem ainda não foi registado no mapa de partições horizontais e ainda não foi alocada ao reter shardlets. Pode, por conseguinte, dependem ligações regular da base de dados para este cenário, em vez de encaminhamento de dados dependentes.  

Isto leva a uma abordagem em que implementação de esquema através de migrações do EF totalmente está integrada com o registo da nova base de dados como um ID de partição horizontal no mapa de partições horizontais da aplicação. Isto baseia-se nos seguintes pré-requisitos: 

* A base de dados já foi criado. 
* A base de dados está vazia - realiza nenhum esquema de utilizador e não existem dados de utilizador.
* A base de dados ainda não pode ser acedido através do cliente de base de dados elástica APIs para o encaminhamento de dados dependentes. 

Com estas pré-requisitos no local, pode criar um regular anular aberta **SqlConnection** para iniciar migrações do EF para a implementação de esquema. O exemplo de código seguinte ilustra esta abordagem. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 


Este exemplo mostra o método **RegisterNewShard** que regista o ID de partição horizontal no mapa de partições horizontais, implementa o esquema através de migrações do EF e armazena um mapeamento de uma chave de fragmentação para as partições horizontais. Depende de um construtor do **DbContext** subclasse (**ElasticScaleContext** no exemplo) que assume uma cadeia de ligação do SQL Server como entrada. O código deste construtor é simples, como o seguinte exemplo mostra: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // You want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

Um poderá utilizou a versão do construtor herdada da classe base. Mas tem do código para se certificar de que o inicializador de predefinido para EF é utilizado ao estabelecer ligação. Por conseguinte, o curto detour para o método estático antes de chamar para o construtor de classe base com a cadeia de ligação. Tenha em atenção que o registo de partições horizontais deve ser executado um domínio aplicacional diferente ou o processo para garantir que as definições de inicializador de EF não entram em conflito. 

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento entail algumas limitações: 

* As aplicações do EF que utilizam **LocalDb** em primeiro lugar migrar uma base de dados do SQL Server regular antes de utilizar a biblioteca de clientes de base de dados elásticas. Não é possível com aumentar horizontalmente uma aplicação através de fragmentação com a escala elástica **LocalDb**. Tenha em atenção que o desenvolvimento ainda pode utilizar **LocalDb**. 
* Quaisquer alterações à aplicação, o que implica alterações do esquema de base de dados tem de passar por migrações do EF em todas as partições horizontais. O código de exemplo para este documento não demonstram como fazê-lo. Considere a utilização de atualização de base de dados com um parâmetro de ConnectionString para iterar shards todos os; ou extrair o script T-SQL para a migração pendente utilizando a base de dados de atualização com Script de-opção e aplicar o script T-SQL para a sua shards.  
* Tendo em conta um pedido, presume-se que todos os respetivo processamento da base de dados está contido dentro de um ID de partição horizontal único conforme identificado pelo fragmentação chave fornecida pelo pedido. No entanto, este pressuposto não sempre manter verdadeiro. Por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isto, a biblioteca de cliente fornece o **MultiShardQuery** classe que implementa uma abstração de ligação para consultas através de várias partições horizontais. Aprender a utilizar o **MultiShardQuery** em combinação com EF está fora do âmbito deste documento

## <a name="conclusion"></a>Conclusão
Os passos descritos neste documento, as aplicações de EF podem utilizar a capacidade da biblioteca de clientes de base de dados elástica para o encaminhamento de dados dependentes por refactoring construtores do **DbContext** subclasses utilizadas na aplicação EF. Isto limita as alterações necessárias para os locais onde **DbContext** classes já existem. Além disso, as aplicações de EF podem continuar a tirar partido da implementação automática de esquema ao combinar os passos que invocam as migrações do EF necessárias com o registo do novo shards e mapeamentos no mapa de partições horizontais. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
