---
title: "Guia de programação SCP.NET | Microsoft Docs"
description: Saiba como utilizar SCP.NET para criar. Topologias do Storm baseado na rede para utilizam o Storm no HDInsight.
services: hdinsight
documentationcenter: 
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: a0ce92ba58fbcda812a3d4e5e275178b73400d6c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="scp-programming-guide"></a>Guia de programação de SCP
SCP é uma plataforma para criar em tempo real, fiável e consistente e a aplicação de processamento de dados de elevado desempenho. Baseia-se de [Apache Storm](http://storm.incubator.apache.org/) – pelo Comunidades de OSS do sistema de processamento de transmissão. Storm está concebido por Nathan Marz e foi aberto obtido pelo Twitter. Utiliza o [Apache ZooKeeper](http://zookeeper.apache.org/), outro projeto Apache para ativar a elevada disponibilidade fiável distribuídas coordenação e estado de gestão. 

Não só o projeto de SCP convertidos serem Storm no Windows, mas também o projeto adicionados extensões e personalização de ecossistema do Windows. As extensões de incluir a experiência de programação do .NET e bibliotecas, a personalização inclui a implementação baseados em Windows. 

A extensão e personalização é feita de forma a que não temos de bifurcar os projetos de OSS e iremos foi tirar partido de ecossistemas derivadas desenvolvidas Storm.

## <a name="processing-model"></a>Modelo de processamento
Os dados no SCP são modelados como sequências contínuas de cadeias de identificação. Normalmente, as cadeias de identificação circular para algumas fila pela primeira vez, em seguida, captado e transformados por lógica de negócio alojada no interior de uma topologia do Storm, por fim, o resultado foi direcionado como cadeias de identificação para outro sistema de SCP ou ser consolidada para lojas de como o sistema de ficheiros distribuído ou bases de dados como o SQL Server.

![Um diagrama de uma fila feeding dados para processamento, que feeds um arquivo de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

No Storm, uma topologia de aplicação define um gráfico de computação. Cada nó de numa topologia contém a lógica de processamento e ligações entre nós indicam o fluxo de dados. Os nós ao inserir dados de entrada na topologia são denominados _spouts_, que podem ser utilizadas para os dados de sequência. Os dados de entrada foi residir nos registos de ficheiros, a base de dados transacional contador de desempenho do sistema etc. Os nós com ambos os fluxos de dados de entrada e de saída são denominados _bolts_, que efetue a filtragem de dados real e seleções e a agregação.

SCP suporta esforços de melhor, em menos uma e exatamente-o processamento de dados uma vez. Uma aplicação de processamento de transmissão em fluxo distribuída, podem ocorrer vários erros durante o processamento de dados, tais como a indisponibilidade de rede, máquina falha ou erro de código de utilizador etc. Em menos uma processamento garante que todos os dados serão processados pelo menos uma vez por replaying automaticamente os mesmos dados, quando ocorre um erro. Em menos uma processamento é simples e fiável e se adapta às bem como muitas aplicações. No entanto, quando uma aplicação requer contando exata, processamento de em menos uma é insuficiente, uma vez que os mesmos dados foi potencialmente ser reproduzidos na topologia de aplicação. Nesse caso, exatamente-depois do processamento foi concebido para se certificar de que o resultado é correto, mesmo quando os dados podem ser reproduzidos e processados várias vezes.

SCP permite aos programadores de .NET desenvolver aplicações de processo de dados em tempo real, tirando partido em Java Máquina Virtual (JVM) com o Storm nos bastidores. O .NET e JVM comunicam através de sockets locais do TCP. Basicamente cada Spout/Bolt é um par de processo do .net/Java, onde a lógica de utilizador é executada no processo de .net como um plug-in.

Para criar uma aplicação de processamento de dados em cima SCP, são necessárias vários passos:

* Conceber e implementar os Spouts para obter dados a partir da fila.
* Conceber e implementar Bolts para processar os dados de entrada para guardar dados em arquivos de externos, como uma base de dados.
* Conceber a topologia, em seguida, submeter e execute a topologia. A topologia define vertexes e os dados fluxos entre os vertexes. SCP irá demorar a especificação de topologia e implementá-la num cluster Storm, onde cada vértice é executada num nó lógico. A ativação pós-falha e o dimensionamento irá ser tratado pelo programador de tarefas do Storm.

Este documento utiliza alguns exemplos simples para guiá-como criar aplicações de processamento de dados com o SCP.

## <a name="scp-plugin-interface"></a>Interface de plug-in de SCP
O plug-ins de SCP (ou aplicações) são EXEs autónomo que podem ambos são executados no interior do Visual Studio durante a fase de desenvolvimento e ser ligados no pipeline de Storm após a implementação em produção. Escrever o plug-in de SCP é apenas mesmas como escrever quaisquer outras padrão consola aplicações do Windows. Plataforma SCP.NET declara algumas interface de spout/bolt e o código de plug-in do utilizador deve implementar estas interfaces. O objetivo principal desta estrutura é que o utilizador pode concentrar-se os seus próprios logics de negócio e deixando outros aspetos a ser processado pela plataforma SCP.NET.

O código de plug-in do utilizador deve implementar uma das interfaces seguinte, depende se a topologia é transacional ou não transacional, e se o componente é um spout ou bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin é a interface comum para todos os tipos de plug-ins. Atualmente, é uma interface fictício.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout é a interface para spout não transacional.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Quando `NextTuple()` denomina-se, C\# código de utilizador pode emitir um ou mais cadeias de identificação. Se não há nada a emissão, este método deverá devolver sem emitir nada. Deve ser salientado que `NextTuple()`, `Ack()`, e `Fail()` são denominados num ciclo sólido num único thread em C\# processo. Quando não existem nenhum cadeias de identificação para emissão, é courteous tem NextTuple suspensão durante um curto período de tempo (por exemplo, 10 milissegundos), por isso, como não waste demasiada CPU.

`Ack()`e `Fail()` são denominados apenas quando o mecanismo de confirmação está ativado no ficheiro spec. O `seqId` é utilizado para identificar a cadeia de identificação que acked ou falhou. Por isso, se a confirmação estiver ativada numa topologia não transacional, a seguinte função emit deve ser utilizada em Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Se a confirmação não é suportada numa topologia não transacional, o `Ack()` e `Fail()` pode ser deixada como função vazia.

O `parms` estas funções do parâmetro de entrada é um dicionário vazio, está reservado para utilização futura.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt é a interface para bolt não transacional.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Se não estiver disponível, a nova cadeia de identificação de `Execute()` função é chamada processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout é a interface para spout transacional.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Tal como a respetiva counter-parte não transacional, `NextTx()`, `Ack()`, e `Fail()` são denominados num ciclo sólido num único thread em C\# processo. Quando não existem nenhum dado para emissão, é courteous ter `NextTx` suspensão de um curto período de tempo (10 milissegundos), por isso, não como para waste demasiada CPU.

`NextTx()`é chamado para iniciar uma nova transação, o parâmetro de saída `seqId` é utilizado para identificar a transação, o que também é utilizada no `Ack()` e `Fail()`. No `NextTx()`, utilizador, pode emitir dados lado de Java. Os dados são armazenados no ZooKeeper para suportar a reprodução. Porque a capacidade de ZooKeeper é limitada, o utilizador deve emitir apenas metadados, não em massa dados spout transacional.

Storm será reprodução de uma transação automaticamente se falhar, por isso `Fail()` não deve ser chamado no caso de normal. Mas se SCP pode verificar os metadados emitidos pelo spout transacional, pode chamar `Fail()` quando os metadados são inválido.

O `parms` estas funções do parâmetro de entrada é um dicionário vazio, está reservado para utilização futura.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt é a interface para bolt transacional.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()`é chamado quando há nova cadeia de identificação a chegar ao bolt. `FinishBatch()`é chamado quando esta transação seja terminada. O `parms` parâmetro de entrada está reservado para utilização futura.

Para topologia de transacional, é um conceito importante – `StormTxAttempt`. Tem dois campos, `TxId` e `AttemptId`. `TxId`é utilizado para identificar uma transação específica, e para uma transação, poderão existir várias tentativas se a transação falha e é reproduzido. SCP.NET cria um novo objeto de ISCPBatchBolt para processar cada `StormTxAttempt`, apenas como o que faz Storm em Java. O objetivo do design desta é suportar o processamento de transações paralelas. Utilizador deve manter-se em mente que, se a tentativa de transação estiver concluída, o objeto ISCPBatchBolt correspondente é destruído e libertados.

## <a name="object-model"></a>Modelo de objeto
SCP.NET também fornece um conjunto simple de objetos da chave para programadores programar com. São **contexto**, **StateStore**, e **SCPRuntime**. Estes são abordados na parte restante nesta secção.

### <a name="context"></a>Contexto
Contexto fornece um ambiente de execução para a aplicação. Cada instância de ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) tem uma instância de contexto correspondente. A funcionalidade fornecida pelo contexto pode ser dividida em duas partes: (1) a parte estática, que está disponível no C todo\# processar, (2) da parte dinâmica, o que só está disponível para a instância de contexto específica.

### <a name="static-part"></a>Parte estática
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger`é fornecido para fins de registo.

`pluginType`é utilizado para indicar o tipo de plug-in de C\# processo. Se o C\# processo é executado no modo de teste local (sem Java), o tipo de plug-in é `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config`é fornecido para obter os parâmetros de configuração do lado de Java. Os parâmetros são transmitidos de lado de Java quando C\# Plug-in é inicializada. O `Config` parâmetros são divididos em duas partes: `stormConf` e `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`é parâmetros definidos pelo Storm e `pluginConf` é parâmetros definidos pelo SCP. Por exemplo:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext`é fornecido para obter o contexto de topologia, é mais útil para componentes com vários paralelismo. Segue-se um exemplo:

    //demo how to get TopologyContext info
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)                      
    {
        Context.Logger.Info("TopologyContext info:");
        TopologyContext topologyContext = Context.TopologyContext;                    
        Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());          
        taskIndex = topologyContext.GetThisTaskIndex();
        Context.Logger.Info("taskIndex: {0}", taskIndex);
        string componentId = topologyContext.GetThisComponentId();                    
        Context.Logger.Info("componentId: {0}", componentId);
        List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
        Context.Logger.Info("taskNum: {0}", componentTasks.Count);                    
    }

### <a name="dynamic-part"></a>Parte dinâmica
As interfaces seguintes são relevantes para uma determinada instância de contexto. A instância de contexto é criada pela plataforma SCP.NET e transmitida para o código de utilizador:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Para não transacional spout ack de suporte, é fornecido o método seguinte:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Para o bolt não transacional ack de suporte, este deve explicitamente `Ack()` ou `Fail()` a cadeia de identificação que recebeu. E quando emitir nova cadeia de identificação, deve também especificar as âncoras de cadeia de identificação de novo. São fornecidos os seguintes métodos.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore de
`StateStore`Fornece serviços de metadados, geração de sequência monotonic e coordenação livre de espera. Abstrações de nível simultaneidade distribuída superior podem ser incorporadas `StateStore`, incluindo as bloqueios distribuídos distribuídas filas, barreiras as eficazes e os serviços de transação.

Podem utilizar aplicações de SCP do `State` objetos para manter algumas informações no ZooKeeper, especialmente para topologia transacional. Se o fizer, se spout transacional falhas e reiniciar, pode obter as informações necessárias do ZooKeeper e reinicie o pipeline.

O `StateStore` objeto tem principalmente estes métodos:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommited States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Commited();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

O `State` objeto tem principalmente estes métodos:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Para o `Commit()` método, quando simpleMode está definido como VERDADEIRO, elimina o ZNode correspondente no ZooKeeper. Caso contrário, elimina o ZNode atual e adicionar um novo nó no CONSOLIDADO\_caminho.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime fornece dois métodos seguintes:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()`é utilizada para inicializar o ambiente de tempo de execução de SCP. Neste método, o C\# processo liga ao lado de Java e obtém os parâmetros de configuração e o contexto de topologia.

`LaunchPlugin()`é utilizado para iniciar o ciclo de processamento de mensagens. Neste ciclo, C\# Plug-in recebe mensagens formulário lado de Java (incluindo sinais de cadeias de identificação e o controlo) e as mensagens, talvez ao chamar o método de interface de processos, em seguida, fornecem pelo código do utilizador. O parâmetro de entrada para o método `LaunchPlugin()` é um delegado que pode devolver um objeto que implementa a interface ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Para ISCPBatchBolt, pode obter `StormTxAttempt` de `parms`e utilizá-la para avaliar se se trata de uma tentativa de reproduzido. A verificação de uma tentativa de repetição, muitas vezes, é realizada ao bolt consolidação e é demonstrado a `HelloWorldTx` exemplo.

Um modo geral, o plug-ins de SCP poderão ser executadas em dois modos aqui:

1. Modo de teste local: Neste modo, o plug-ins de SCP (C\# código de utilizador) execute no interior do Visual Studio durante a fase de desenvolvimento. `LocalContext`pode ser utilizado neste modo, o que fornece o método para serializar as cadeias de identificação emitted para ficheiros locais e lê-los a memória.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Modo normal: Neste modo, os plug-ins de SCP são iniciadas pelo processo de java de storm.
   
    Eis um exemplo de iniciar o plug-in do SCP:
   
        namespace Scp.App.HelloWorld
        {
        public class Generator : ISCPSpout
        {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
            return new Generator(ctx);
            }
        }
   
        class HelloWorld
        {
            static void Main(string[] args)
            {
            /* Setting the environment variable here can change the log file name */
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");
   
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
            }
        }
        }

## <a name="topology-specification-language"></a>Idioma de especificação de topologia
Especificação de topologia de SCP é uma linguagem de específicas do domínio para descrever e configurar as topologias de SCP. Se baseia no Clojure DSL do Storm (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) e for prolongada até o SCP.

Especificações da topologia podem ser submetidas diretamente para o cluster do storm para execução através de ***runspec*** comando.

SCP.NET adicionou as seguintes funções para definir transacional topologias:

| **Novas funções** | **Parâmetros** | **Descrição** |
| --- | --- | --- |
| **Tx topolopy** |topologia de nome<br />mapa de spout<br />mapa de bolt |Definir uma topologia de transacional com o nome de topologia, &nbsp;spouts mapa de definição e o mapa de definição de bolts |
| **SCP-tx-spout** |Exec-name<br />args<br />Campos |Defina um spout transacional. É executada a aplicação com ***exec-name*** utilizando ***args***.<br /><br />O ***campos*** é os campos de saída para spout |
| **SCP-tx-batch-bolt** |Exec-name<br />args<br />Campos |Defina um Bolt Batch transacional. É executada a aplicação com ***exec-name*** utilizando ***args.***<br /><br />Os campos é os campos de saída para bolt. |
| **SCP-tx-commit-bolt** |Exec-name<br />args<br />Campos |Defina um bolt consolidação transacional. É executada a aplicação com ***exec-name*** utilizando ***args***.<br /><br />O ***campos*** é os campos de saída para bolt |
| **nontx topolopy** |topologia de nome<br />mapa de spout<br />mapa de bolt |Definir uma topologia de nontransactional com o nome de topologia,&nbsp; spouts mapa de definição e o mapa de definição de bolts |
| **SCP spout** |Exec-name<br />args<br />Campos<br />parâmetros |Defina um spout nontransactional. É executada a aplicação com ***exec-name*** utilizando ***args***.<br /><br />O ***campos*** é os campos de saída para spout<br /><br />O ***parâmetros*** são opcionais, utilizá-la para especificar alguns parâmetros, tais como "nontransactional.ack.enabled". |
| **SCP bolt** |Exec-name<br />args<br />Campos<br />parâmetros |Defina um nontransactional Bolt. É executada a aplicação com ***exec-name*** utilizando ***args***.<br /><br />O ***campos*** é os campos de saída para bolt<br /><br />O ***parâmetros*** são opcionais, utilizá-la para especificar alguns parâmetros, tais como "nontransactional.ack.enabled". |

SCP.NET tem as seguintes palavras-chave definidas:

| **Palavras-chave** | **Descrição** |
| --- | --- |
| **: nome** |Definir o nome de topologia |
| **: topologia** |Definir a topologia com as funções anteriores e criar naqueles. |
| **: p** |Defina a sugestão de paralelismo para cada spout ou bolt. |
| **: configuração** |Definir configurar parâmetro ou atualizar os existentes |
| **: esquema** |Defina o esquema de fluxo. |

E parâmetros utilizados com frequência:

| **Parâmetro** | **Descrição** |
| --- | --- |
| **"plugin.name"** |nome de ficheiro. exe do plug-in c# |
| **"plugin.args"** |argumentos de plug-in |
| **"output.schema"** |Esquema de saída |
| **"nontransactional.ack.enabled"** |Se a confirmação está ativada para topologia de nontransactional |

O comando runspec é implementado juntamente com o bits, a utilização é como:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

O ***recursos dir*** parâmetro é opcional, tem de especificar quando pretende fixação um C\# aplicação e este diretório contém a aplicação, dependências e configurações.

O ***classpath*** parâmetro também é opcional. É utilizado para especificar o classpath Java se o ficheiro na especificação contiver Java Spout ou Bolt.

## <a name="miscellaneous-features"></a>Diversas funcionalidades
### <a name="input-and-output-schema-declaration"></a>Entrada e saída esquema declaração
Os utilizadores podem emitir cadeias de identificação no C\# processos, a plataforma necessita de serializar a cadeia de identificação em byte [], a transferência de lado de Java e Storm irá transferir esta cadeia de identificação para os destinos. Entretanto nos componentes a jusante, C\# processos irão receber cadeias de identificação novamente a partir do lado de java e convertê-lo para os tipos de originais pela plataforma, todas as operações destas estão ocultadas por plataforma.

Para suportar a serialização e a anulação da serialização, o código de utilizador tem de declarar o esquema de entradas e saídas.

O esquema de fluxo de entrada/saída está definido como um dicionário. A chave é a StreamId. O valor é os tipos de colunas. O componente pode ter vários fluxos declarados.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }


Objeto de contexto, temos a seguinte API adicionada:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Os programadores tem de garantir que as cadeias de identificação emitidas obedecer o esquema definido para esse fluxo, caso contrário, o sistema irá gerar uma exceção de tempo de execução.

### <a name="multi-stream-support"></a>Suporte de fluxo multi
SCP suporta o código de utilizador para emitir ou receber vários fluxos distintos ao mesmo tempo. O suporte reflete no objeto de contexto, como o método de Emit assume um parâmetro de ID do fluxo opcional.

Foram adicionados dois métodos no objeto de contexto SCP.NET. São utilizados para emitir a cadeia de identificação ou cadeias de identificação para especificar StreamId. O StreamId é uma cadeia e tem de ser consistente em ambos os C\# e a especificação de definição de topologia.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Emitir um fluxo não existente faz com que as exceções de tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de campos
O agrupamento de campos incorporados em Strom não está a funcionar corretamente no SCP.NET. No lado do Proxy de Java, todos os tipos de dados de campos são, na verdade, de byte [] e os campos de agrupamento utiliza o código de hash de objeto do byte [] para executar o agrupamento. O código de hash de objeto do byte [] é o endereço deste objeto na memória. Por isso, o agrupamento será errado para objetos do dois byte [] que partilham os mesmos conteúdos, mas não o mesmo endereço.

SCP.NET adiciona um método de agrupamento personalizados e utiliza o conteúdo do byte [] para fazer o agrupamento. No **SPEC** a sintaxe de ficheiro, é como:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Aqui,

1. "grupo de campo de scp" significa "Agrupamento de campo personalizado implementado por SCP".
2. ": tx"ou": não tx" significa que se trata de topologia transacional. Esta informação é necessário, uma vez que o índice de início é diferente em tx vs não tx topologias.
3. meio [0,1] um conjunto de hash de Ids de campo, a partir de 0.

### <a name="hybrid-topology"></a>Topologia de híbrida
O Storm nativo é escrito em Java. E SCP.Net foi melhorada para permitir C\# aos programadores escrever C\# código para processar a respetiva lógica de negócio. Mas também suporta topologias híbridas, que contém não só C\# spouts/bolts, mas também Java Spout/Bolts.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Especifique o Java Spout/Bolt no ficheiro spec
No ficheiro spec, "scp spout" e "scp bolt" também podem ser utilizados para especificar Java Spouts e Bolts, eis um exemplo:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Aqui `microsoft.scp.example.HybridTopology.Generator` é o nome da classe Java Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Especifique o Java Classpath no runSpec comando
Se quer submeter a topologia com Java Spouts ou Bolts, tem primeiro de compilação de Java Spouts ou Bolts e obter os ficheiros Jar. Em seguida, deve especificar o classpath de java que contém os ficheiros Jar ao submeter a topologia. Segue-se um exemplo:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Aqui **exemplos\\HybridTopology\\java\\destino\\**  é a pasta que contém o ficheiro Jar de Spout/Bolt de Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e a anulação da serialização entre Java e C\#
Componente de SCP inclui lado de Java e C\# lado. Para interagir com nativos Spouts de Java/Bolts, deve ser realizada serialização/desserialização entre lado de Java e C\# lado, conforme ilustrado no gráfico seguinte.

![Diagrama do componente de java enviar para o componente de SCP enviar para o componente de Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serialização no lado de Java e anulação da serialização no C\# lado**
   
   Primeiro, fornecemos implementação predefinida para a serialização do lado de Java e anulação da serialização no C\# lado. O método de serialização do lado do Java pode ser especificado no ficheiro SPEC:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   O método de anulação da serialização C\# lado deve ser especificado no C\# código de utilizador:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Esta implementação predefinida deve processar a maioria dos casos fornecido o tipo de dados não é demasiado complexo. Alguns casos, porque o tipo de dados do utilizador é demasiado complexo ou porque o desempenho da nossa implementação predefinida não cumpre os requisitos do utilizador, os utilizadores podem Plug-in sua própria implementação.
   
   A interface de serializar no lado de java está definida como:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   A interface de deserialize C\# lado está definido como:
   
   interface pública ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serialização no C\# lado e anulação da serialização do lado do Java**
   
   O método de serialização C\# lado deve ser especificado no C\# código de utilizador:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   O método de anulação da serialização do lado de Java deve ser especificado no ficheiro SPEC:
   
     (o scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Aqui "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" é o nome do desserializador, não sendo "microsoft.scp.example.HybridTopology.Person" os dados da classe de destino é anular a serialização para.
   
   Utilizador também pode plug-in as seus próprios implementação de C\# serializador e desserializador de Java. Este código é a interface para C\# serializador:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Este código é a interface para o desserializador Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Modo de anfitrião de SCP
Neste modo, utilizador pode compilar os seus códigos de dll e utilizar SCPHost.exe fornecida pelo SCP para submeter a topologia. O ficheiro na especificação aspeto este código:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Aqui, `plugin.name` está especificado como `SCPHost.exe` fornecida pelo SCP SDK. SCPHost.exe aceita três parâmetros:

1. A primeira é o nome DLL, que é `"HelloWorld.dll"` neste exemplo.
2. Segunda é o nome de classe, que é `"Scp.App.HelloWorld.Generator"` neste exemplo.
3. O nome de um método estático público, que pode ser invocado para obter uma instância de ISCPPlugin é o terceiro.

No modo de anfitrião, o código de utilizador é compilado como DLL e é invocado pela plataforma de SCP. Por isso, plataforma de SCP pode obter o controlo total da lógica de processamento de todo. Por isso, recomendamos que os nossos clientes ao submeter a topologia no modo de anfitrião de SCP, uma vez que pode simplificar a experiência de desenvolvimento e coloque-nos para mais flexibilidade e melhor compatibilidade com versões anteriores, bem como a versão posterior.

## <a name="scp-programming-examples"></a>Exemplos de programação de SCP
### <a name="helloworld"></a>HelloWorld
**Olámundo** é um exemplo simples para mostrar um taste de SCP.Net. Utiliza uma topologia não transacional, com um spout chamado **gerador**e duas bolts chamados **divisor** e **contador**. O spout **gerador** aleatoriamente gera frases e emitir estes frases para **divisor**. Bolt **divisor** divide frases para palavras e emitir estes palavras a **contador** bolt. Bolt "contador" utiliza um dicionário para registar o número de ocorrência de cada palavra.

Existem dois ficheiros spec, **HelloWorld.spec** e **Olámundo\_EnableAck.spec** para este exemplo. No C\# código, pode descobrir se a confirmação está ativada por obter o pluginConf do lado de Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Spout, se a confirmação estiver ativada, um dicionário é utilizado para colocar em cache as cadeias de identificação que não foram acked. Se denomina-se Fail(), é reproduzida pela cadeia de identificação com falhas:

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### <a name="helloworldtx"></a>HelloWorldTx
O **HelloWorldTx** exemplo demonstra como implementar a topologia transacional. Tem um spout chamado **gerador**, um bolt batch chamado **parcial contagem**, e um bolt consolidação denominada **contagem soma**. Existem três ficheiros txt previamente criada: **DataSource0.txt**, **DataSource1.txt**, e **DataSource2.txt**.

Em cada transação, o spout **gerador** aleatoriamente seleciona dois ficheiros dos ficheiros de três previamente criados e emitir os nomes de dois ficheiros para o **parcial contagem** bolt. Bolt **parcial contagem** obtém o ficheiro de nome da cadeia de identificação recebida, em seguida, abra o ficheiro contabilizar o número de palavras neste ficheiro e, finalmente, emitir o número de palavras para o **contagem soma** bolt. O **contagem soma** bolt resume a contagem total.

Para alcançar **exatamente uma vez** semântica, bolt consolidação **contagem soma** tem de avaliar se se trata de uma transação reproduzida. Neste exemplo, tem uma variável de membro estático:

    public static long lastCommittedTxId = -1; 

Quando é criada uma instância de ISCPBatchBolt, obtém o `txAttempt` de parâmetros de entrada:

    public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
    {
        /* for transactional topology, we can get txAttempt from the input parms */
        if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
        {
            StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
            return new CountSum(ctx, txAttempt);
        }
        else
        {
            throw new Exception("null txAttempt");
        }
    }

Quando `FinishBatch()` denomina-se, o `lastCommittedTxId` serão atualizados se não se trata de uma transação reproduzida.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
Esta topologia contém um Spout Java e um C\# Bolt. Utiliza a implementação de serialização e a anulação da serialização de predefinida fornecida pela plataforma de SCP. Consulte o **HybridTopology.spec** no **exemplos\\HybridTopology** pasta para os detalhes na especificação do ficheiro, e **SubmitTopology.bat** como especificar Java ClassPath.

### <a name="scphostdemo"></a>SCPHostDemo
Neste exemplo é o mesmo que Olámundo essencialmente. A única diferença é que o código de utilizador é compilado como DLL e a topologia é submetida utilizando SCPHost.exe. Consulte a secção "Modo de anfitrião de SCP" para obter explicações mais detalhadas.

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos de topologias do Storm criadas utilizando o SCP, consulte os seguintes documentos:

* [Desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Processar eventos provenientes dos Hubs de eventos do Azure com o Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processar os dados de sensor vehicle provenientes dos Hubs de eventos a utilizar o Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrair, transformar e carregar (ETL) a partir do Event Hubs do Azure para HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
