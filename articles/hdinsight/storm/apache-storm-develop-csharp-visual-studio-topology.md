---
title: Topologias do Apache Storm com o Visual Studio e c# - Azure HDInsight | Microsoft Docs
description: Saiba como criar topologias Storm em c#. Cria uma topologia de contagem do word simples no Visual Studio, utilizando as ferramentas Hadoop para o Visual Studio.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2017
ms.author: larryfr
ms.openlocfilehash: d972def582ff8fee74c2eae59f4756eb1dcd0a70
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Desenvolver topologias c# para Apache Storm, utilizando as ferramentas do Data Lake para Visual Studio

Saiba como criar uma topologia do Storm c# utilizando as ferramentas do Azure Data Lake (Hadoop) para o Visual Studio. Este documento explica o processo de criação de um projeto de Storm no Visual Studio, testá-lo localmente e implementar a política para um Apache Storm no cluster do Azure HDInsight.

Também irá aprender a criar topologias híbridas que utilizam c# e componentes de Java.

> [!NOTE]
> Enquanto os passos neste documento dependem de um ambiente de desenvolvimento do Windows com o Visual Studio, o projeto compilado pode ser submetido para um Linux ou o HDInsight baseado em Windows cluster. Apenas os clusters baseados em Linux criados após 28 de Outubro de 2016, suportam SCP.NET topologias.

Para utilizar uma topologia de c# com um cluster baseado em Linux, tem de atualizar o pacote Microsoft.SCP.Net.SDK NuGet utilizado pelo seu projeto para a versão 0.10.0.6 ou posterior. A versão do pacote também tem de corresponder à versão principal do Storm instalada no HDInsight.

| Versão do HDInsight | Versão do Storm | Versão SCP.NET | Versão Mono predefinida |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(apenas no HDInsight baseado em Windows) | ND |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> As topologias C# em clusters baseados em Linux têm de utilizar o .NET 4.5 e utilizar o Mono para serem executadas no cluster do HDInsight. Verifique [compatibilidade Mono](http://www.mono-project.com/docs/about-mono/compatibility/) para incompatibilidades potenciais.

## <a name="install-visual-studio"></a>Instalar o Visual Studio

Pode desenvolver topologias c# com SCP.NET utilizando uma das seguintes versões do Visual Studio:

* Visual Studio 2012 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=39305)

* Visual Studio 2013 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 ou [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (qualquer edição)

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Data Lake tools para Visual Studio

Para instalar as ferramentas do Data Lake para Visual Studio, siga os passos no [começar a utilizar as ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Instalar o Java

Quando submete uma topologia do Storm do Visual Studio, SCP.NET gera um ficheiro zip que contém a topologia e as dependências. Java é utilizado para criar estes ficheiros zip, porque utiliza um formato que é mais compatível com clusters baseados em Linux.

1. Instale o Java Developer Kit (JDK) 7 ou posterior no seu ambiente de desenvolvimento. Pode obter o JDK Oracle de [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Também pode utilizar [outras distribuições de Java](http://openjdk.java.net/).

2. O `JAVA_HOME` variável de ambiente tem de apontar para o diretório que contém o Java.

3. O `PATH` variável de ambiente tem de incluir o `%JAVA_HOME%\bin` diretório.

Pode utilizar a aplicação de consola c# seguinte para verificar que Java e o JDK estão corretamente instalados:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable(“JAVA_HOME”);
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @”\bin”, “jar.exe”);
               if (File.Exists(jarExe))
               {
                   Console.WriteLine(“JAVA Is Installed properly”);
                    return;
               }
               else
               {
                   Console.WriteLine(“A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.”);
               }
           }
           else
           {
             Console.WriteLine(“A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.”);
           }
       }  
   }
}
```

## <a name="storm-templates"></a>Modelos de Storm

As ferramentas do Data Lake para Visual Studio fornecem os seguintes modelos:

| Tipo de projeto | Demonstra |
| --- | --- |
| Aplicação Storm |Um projeto de topologia do Storm vazio. |
| Exemplo de escritor do Storm SQL do Azure |Como escrever a SQL Database do Azure. |
| Exemplo do Storm Azure Cosmos DB leitor |Como a leitura da base de dados do Azure Cosmos. |
| Exemplo de escritor do Storm Cosmos BD do Azure |Como escrever BD do Cosmos do Azure. |
| Exemplo de leitor de EventHub Storm |Como ler a partir do Event Hubs do Azure. |
| Exemplo de escritor de EventHub Storm |Como escrever Event Hubs do Azure. |
| Exemplo de leitor de HBase Storm |Como ler a partir do HBase nos clusters do HDInsight. |
| Exemplo de escritor do HBase do Storm |Como escrever HBase nos clusters do HDInsight. |
| Exemplo do Storm híbrida |Como utilizar um componente de Java. |
| Exemplo de Storm |Uma topologia de contagem do word básico. |

> [!WARNING]
> Nem todos os modelos irão funcionar com o HDInsight baseado em Linux. Não podem ser compatíveis com Mono utilizados pelos modelos de pacotes de Nuget. Verifique o [compatibilidade Mono](http://www.mono-project.com/docs/about-mono/compatibility/) documentos e utilizar o [.NET portabilidade analisador](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) para identificar potenciais problemas.

Nos passos neste documento, utilize o tipo de projeto de aplicação Storm básico para criar uma topologia.

### <a name="hbase-templates-notes"></a>Notas de modelos de HBase

Os modelos de leitor e escritor do HBase utilizam a API de REST de HBase, não a API de Java HBase para comunicar com um HBase no cluster do HDInsight.

### <a name="eventhub-templates-notes"></a>Notas de modelos de EventHub

> [!IMPORTANT]
> O componente de spout baseada em Java EventHub incluído com o modelo de leitor de EventHub não pode funcionar com o Storm no HDInsight versão 3.5 ou posterior. Uma versão atualizada deste componente está disponível em [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Para uma topologia de exemplo que utiliza este componente e funciona com o Storm no HDInsight 3.5, consulte o artigo [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma topologia de c#

1. Abra o Visual Studio, selecione **ficheiro** > **novo**e, em seguida, selecione **projeto**.

2. Do **novo projeto** janela, expanda **instalada** > **modelos**e selecione **do Azure Data Lake**. Na lista de modelos, selecione **aplicação Storm**. Na parte inferior do ecrã, introduza **WordCount** como o nome da aplicação.

    ![Janela de captura de ecrã do novo projeto](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Depois de criar o projeto, deve ter os seguintes ficheiros:

   * **Program.cs**: este ficheiro define a topologia para o seu projeto. Uma topologia de predefinido que é composta por um spout e um bolt é criada por predefinição.

   * **Spout.cs**: um spout de exemplo que emite números aleatórios.

   * **Bolt.cs**: um bolt de exemplo que mantém uma contagem dos números emitidos pelo spout.

     Quando criar o projeto, NuGet transfere a versão mais recente [SCP.NET pacote](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementar o spout

1. Abra **Spout.cs**. Spouts são utilizados para ler dados numa topologia de uma origem externa. Os componentes principais de um spout são:

   * **NextTuple**: chamado pelo Storm quando não é permitida a spout para emitir cadeias de identificação de novo.

   * **Confirmação** (apenas na topologia transacional): processa confirmações iniciadas por outros componentes na topologia para cadeias de identificação enviadas a partir de spout. Confirmar uma cadeia de identificação permite spout saber que foi processado com êxito por componentes a jusante.

   * **Falhar** (apenas na topologia transacional): processa cadeias de identificação que são falhar processamento de outros componentes na topologia. Implementar um método de falhas permite-lhe emitir novamente a cadeia de identificação, de modo a que pode ser processado novamente.

2. Substitua os conteúdos do **Spout** classe com o seguinte texto. Este spout aleatoriamente emite uma frase para a topologia.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementar os bolts

1. Elimine o existente **Bolt.cs** ficheiros do projeto.

2. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **adicionar** > **novo item**. Na lista, selecione **Storm Bolt**e introduza **Splitter.cs** como o nome. Repita este processo para criar um segundo bolt denominado **Counter.cs**.

   * **Splitter.cs**: implementa um bolt que divide frases numa palavras individuais e emite um novo fluxo de palavras.

   * **Counter.cs**: implementa um bolt que contagens de cada palavra e emite um novo fluxo de palavras e a contagem de cada palavra.

     > [!NOTE]
     > Estes bolts para ler e escrever fluxos, mas também pode utilizar um bolt para comunicar com origens, como uma base de dados ou serviço.

3. Abra **Splitter.cs**. Tem apenas um método por predefinição: **executar**. O método executar é chamado quando o bolt recebe uma cadeia de identificação para o processamento. Aqui, pode ler e processar cadeias de identificação de entrada e saídas cadeias de identificação de emissão.

4. Substitua os conteúdos do **divisor** classe com o seguinte código:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Abra **Counter.cs**e substitua o conteúdo de classe com o seguinte:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Definir a topologia

Spouts e bolts são dispostas num gráfico que define como os dados fluem entre os componentes. Para esta topologia, o gráfico é o seguinte:

![Diagrama de como os componentes são dispostos](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Frases são emitidos a partir de spout e são distribuídos para instâncias de bolt divisor. Bolt divisor quebras de frases no palavras, o que são distribuídas para o bolt de contador.

Porque a contagem de word é mantida localmente na instância do contador, queremos certificar-se de que as palavras específicas circular para a mesma instância de bolt do contador. Cada instância mantém um registo dos palavras específicas. Uma vez que o bolt divisor mantém sem estado, realmente irrelevante que instância do divisor recebe o frase.

Abra **Program.cs**. O método de importante **GetTopologyBuilder**, que é utilizado para definir a topologia que foi submetida para Storm. Substitua os conteúdos de **GetTopologyBuilder** com o seguinte código para implementar a topologia descrita anteriormente:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Submeter a topologia

1. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **envio para Storm no HDInsight**.

   > [!NOTE]
   > Se lhe for solicitado, introduza as credenciais para a sua subscrição do Azure. Se tiver mais do que uma subscrição, inicie sessão no que contém o seu cluster Storm no HDInsight.

2. Selecione o seu cluster Storm no HDInsight do **Cluster do Storm** na lista pendente e, em seguida, selecione **submeter**. Pode monitorizar se a submissão for bem sucedida, utilizando o **saída** janela.

3. Quando a topologia foi submetida com êxito, o **topologias Storm** para o cluster deve aparecer. Selecione o **WordCount** topologia da lista para ver informações sobre a topologia em execução.

   > [!NOTE]
   > Também pode ver **topologias Storm** de **Explorador de servidores**. Expanda **Azure** > **HDInsight**, um cluster Storm no HDInsight com o botão direito e, em seguida, selecione **topologias do Storm vista**.

    Para ver informações sobre os componentes na topologia, faça duplo clique o componente no diagrama.

4. Do **resumo da topologia** ver, clique em **Kill** para parar a topologia.

   > [!NOTE]
   > Topologias do Storm continuam a ser executado até que são desativadas ou o cluster é eliminado.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior é não transacional. Os componentes na topologia implementa a funcionalidade para replaying mensagens. Para obter um exemplo de uma topologia transacional, crie um projeto e selecione **exemplo do Storm** como tipo de projeto.

Topologias transacionais implementam o seguinte para suportar a reprodução de dados:

* **Colocação em cache de metadados**: O spout tem de armazenar metadados sobre dados emitidos, para que os dados podem ser obtidos e novamente emitidos se ocorrer uma falha. Porque os dados emitidos pela amostra são pequenos, os dados não processados para cada cadeia de identificação são armazenados um dicionário para repetição.

* **Confirmação**: cada bolt na topologia pode chamar `this.ctx.Ack(tuple)` a confirmar que foi processado com êxito uma cadeia de identificação. Quando todos os bolts têm acked a cadeia de identificação, o `Ack` é invocar o método do spout. O `Ack` método permite spout remover os dados que foi colocado em cache para repetição.

* **Falhar**: cada bolt pode chamar `this.ctx.Fail(tuple)` para indicar que tem falha no processamento para uma cadeia de identificação. A falha propaga para o `Fail` método de spout, onde a cadeia de identificação pode ser reproduzida utilizando colocados em cache de metadados.

* **ID de sequência**: Quando emitir uma cadeia de identificação, pode ser especificado um ID exclusivo de sequência. Este valor identifica a cadeia de identificação para o processamento de repetição (Ack e falhar). Por exemplo, o spout no **exemplo do Storm** projeto utiliza o seguinte quando emitir dados:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Este código emite uma cadeia de identificação que contém uma frase no fluxo predefinido, com o valor de ID de sequência contido no **lastSeqId**. Neste exemplo, **lastSeqId** é aumentada para todas as cadeias de identificação emitidas.

Como é demonstrado no **exemplo do Storm** do projeto, se um componente transacional pode ser definido no tempo de execução, com base na configuração.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia de híbrida com c# e Java

Também pode utilizar as ferramentas do Data Lake para Visual Studio para criar topologias híbridas, em que alguns componentes são c# e outros estão Java.

Para obter um exemplo de uma topologia de híbrida, crie um projeto e selecione **exemplo do Storm híbrida**. Este tipo de exemplo demonstra os seguintes conceitos:

* **Java spout** e **c# bolt**: definidos no **HybridTopology_javaSpout_csharpBolt**.

    * Uma versão transacional está definida no **HybridTopologyTx_javaSpout_csharpBolt**.

* **C# spout** e **Java bolt**: definidos no **HybridTopology_csharpSpout_javaBolt**.

    * Uma versão transacional está definida no **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > Esta versão também demonstra como utilizar Clojure código a partir de um ficheiro de texto como um componente de Java.


Para mudar a topologia que é utilizada quando o projeto é submetido, basta mover o `[Active(true)]` declaração à topologia que pretende utilizar, antes de submetê-lo para o cluster.

> [!NOTE]
> Todos os ficheiros de Java que são necessários são fornecidos como parte deste projeto no **JavaDependency** pasta.

Quando estiver a criar e submeter uma topologia de híbrida, considere o seguinte:

* Tem de utilizar **JavaComponentConstructor** para criar uma instância da classe Java para um spout ou bolt.

* Deve utilizar **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** para serializar os dados ou a sair componentes de Java de objetos de Java para JSON.

* Ao submeter a topologia para o servidor, tem de utilizar o **configurações adicionais** opção para especificar o **caminhos de ficheiro de Java**. O caminho especificado deve ser o diretório que contém os ficheiros JAR que contêm as classes de Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Versão SCP.NET 0.9.4.203 introduz uma nova classe e o método especificamente para funcionar com o spout de Hub de eventos (um spout de Java que lê a partir do Event Hubs). Quando cria uma topologia que utiliza um spout de Hub de eventos, utilize os seguintes métodos:

* **EventHubSpoutConfig** classe: cria um objeto que contém a configuração para o componente de spout.

* **TopologyBuilder.SetEventHubSpout** método: Adiciona o componente do Event Hub spout à topologia.

> [!NOTE]
> Ainda tem de utilizar o **CustomizedInteropJSONSerializer** para serializar os dados produzidos pelo spout.

## <a id="configurationmanager"></a>Utilizar ConfigurationManager

Não utilize **ConfigurationManager** para obter valores de configuração de bolt e spout componentes. Se o fizer, pode causar uma exceção de apontador nulo. Em vez disso, a configuração para o seu projeto é transmitida a topologia do Storm como um par chave e valor no contexto de topologia. Cada componente que se baseie nos valores de configuração tem de obtê-los do contexto durante a inicialização.

O código seguinte demonstra como obter estes valores:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Se utilizar um `Get` método para devolver uma instância do seu componente, tem de se certificar que tenha sido enviado ambos o `Context` e `Dictionary<string, Object>` parâmetros do construtor. O exemplo seguinte é num nível básico `Get` método transmite corretamente estes valores:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Como atualizar SCP.NET

Versões recentes do SCP.NET suportam a atualização do pacote através do NuGet. Quando uma nova atualização está disponível, recebe uma notificação de atualização. Para procurar uma atualização manualmente, siga estes passos:

1. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **gerir pacotes NuGet**.

2. A partir do Gestor de pacote, selecione **atualizações**. Se estiver disponível uma atualização, este está listado. Clique em **atualização** para o pacote para o instalar.

> [!IMPORTANT]
> Se o projeto foi criado com uma versão anterior do SCP.NET que não foi possível utilizar o NuGet, tem de efetuar os seguintes passos para atualizar para uma versão mais recente:
>
> 1. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **gerir pacotes NuGet**.
> 2. Utilizar o **pesquisa** campo, procure e, em seguida, adicionar, **Microsoft.SCP.Net.SDK** ao projeto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Resolver problemas comuns com as topologias

### <a name="null-pointer-exceptions"></a>Exceções de apontador nulo

Quando estiver a utilizar uma topologia de c# com um cluster do HDInsight baseado em Linux, bolt e spout componentes que utilizam **ConfigurationManager** ler definições de configuração em tempo de execução podem devolver exceções de apontador nulo.

A configuração para o seu projeto é transmitida a topologia do Storm como um par chave e valor no contexto de topologia. Pode ser obtida o objeto de dicionário transmitido para os componentes quando estes estão a ser inicializados.

Para obter mais informações, consulte o [ConfigurationManager](#configurationmanager) secção deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Quando estiver a utilizar uma topologia de c# com um cluster do HDInsight baseado em Linux, pode encontrar o seguinte erro:

    System.TypeLoadException: Failure has occurred while loading a type.

Este erro ocorre quando utiliza um binário que não é compatível com a versão do .NET que Mono suporta.

Para clusters do HDInsight baseado em Linux, certifique-se de que o seu projeto utiliza binários compilados para o .NET 4.5.

### <a name="test-a-topology-locally"></a>Testar uma topologia localmente

Embora seja fácil de implementar uma topologia para um cluster, em alguns casos, poderá ter de testar uma topologia localmente. Utilize os seguintes passos para executar e testar a topologia de exemplo neste tutorial localmente no seu ambiente de desenvolvimento.

> [!WARNING]
> Testar local só funciona para básico, c#-apenas topologias. Não é possível utilizar a testar local topologias híbridas ou as topologias que utilizar vários fluxos.

1. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **propriedades**. Nas propriedades do projeto, altere o **de saída do tipo** para **aplicação de consola**.

    ![Captura de ecrã das propriedades do projeto, com o tipo de saída realçado](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Não se esqueça de alterar o **de saída do tipo** para **biblioteca de classes** antes de implementar a topologia para um cluster.

2. No **Explorador de soluções**, com o botão direito no projeto e, em seguida, selecione **adicionar** > **Novo Item**. Selecione **classe**e introduza **LocalTest.cs** como o nome de classe. Por fim, clique em **adicionar**.

3. Abra **LocalTest.cs**e adicione o seguinte **utilizando** declaração na parte superior:

    ```csharp
    using Microsoft.SCP;
    ```

4. Utilize o seguinte código como o conteúdo a **LocalTest** classe:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Demorar um pouco para ler os comentários do código. Este código utiliza **LocalContext** executar os componentes no ambiente de desenvolvimento e continuar o fluxo de dados entre os componentes para ficheiros de texto no disco local.

1. Abra **Program.cs**e adicione o seguinte para o **Main** método:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Guardar as alterações e, em seguida, clique em **F5** ou selecione **depurar** > **iniciar depuração** para iniciar o projeto. Uma janela da consola deve aparecer e registo de estado como o progresso de testes. Quando **testes concluída** for apresentada, prima qualquer tecla para fechar a janela.

3. Utilize **Explorador do Windows** para localizar o diretório que contém o projeto. Por exemplo: **C:\Users\<your_user_name > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Neste diretório, abra **Bin**e, em seguida, clique em **depurar**. Deverá ver os ficheiros de texto que foram produzidos quando executaram os testes: sentences.txt, counter.txt e splitter.txt. Abra cada ficheiro de texto e inspecionar os dados.

   > [!NOTE]
   > Mantém os dados de cadeia como uma matriz de valores decimais destes ficheiros. Por exemplo, \[[97,103,111]] no **splitter.txt** ficheiro é a palavra *e*.

> [!NOTE]
> Certifique-se definir o **o tipo de projeto** para **biblioteca de classes** antes de implementar um cluster Storm no HDInsight.

### <a name="log-information"></a>Informações de registo

Pode facilmente registar informações de componentes da topologia utilizando `Context.Logger`. Por exemplo, o seguinte cria uma entrada de registo informativa:

```csharp
Context.Logger.Info("Component started");
```

Informações com sessão iniciada podem ser visualizadas a **registo do serviço de Hadoop**, que se encontra no **Explorador de servidores**. Expanda a entrada para o cluster Storm no HDInsight e, em seguida, expanda **registo do serviço de Hadoop**. Por fim, selecione o ficheiro de registo para ver.

> [!NOTE]
> Os registos são armazenados na conta do storage do Azure que é utilizada pelo cluster. Para ver os registos no Visual Studio, tem de iniciar sessão subscrição do Azure que é proprietário da conta de armazenamento.

### <a name="view-error-information"></a>Ver informações de erro

Para ver os erros ocorridos numa topologia em execução, utilize os seguintes passos:

1. De **Explorador de servidores**, o cluster Storm no HDInsight com o botão direito e selecione **topologias Storm vista**.

2. Para o **Spout** e **Bolts**, a **último erro** coluna contém informações sobre o erro da última.

3. Selecione o **Spout Id** ou **Bolt Id** para o componente que tem um erro listado. Na página de detalhes que é apresentado, adicional erro informações estão listadas no **erros** secção na parte inferior da página.

4. Para obter mais informações, selecione um **porta** do **executores** secção da página, para ver o registo de trabalho do Storm para os último alguns minutos.

### <a name="errors-submitting-topologies"></a>Submeter topologias de erros

Se encontrar erros submeter uma topologia para o HDInsight, pode encontrar registos para os componentes do lado do servidor que processar a submissão de topologia no cluster do HDInsight. Para obter estes registos, utilize o seguinte comando numa linha de comandos:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Substitua __sshuser__ com a conta de utilizador do SSH para o cluster. Substitua __clustername__ com o nome do cluster do HDInsight. Para obter mais informações sobre como utilizar `scp` e `ssh` com o HDInsight, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Submissões podem falhar por vários motivos:

* JDK não está instalado ou não está no caminho.
* Dependências de Java necessárias não estão incluídas na submissão.
* Dependências de incompatíveis.
* Duplicar nomes de topologia.

Se o `hdinsight-scpwebapi.out` registo contém um `FileNotFoundException`, poderá dever-se as seguintes condições:

* O JDK não está a ser o caminho no ambiente de desenvolvimento. Certifique-se de que o JDK está instalado no ambiente de desenvolvimento e de que `%JAVA_HOME%/bin` está no caminho.
* Estão em falta uma dependência de Java. Certifique-se de que é incluindo quaisquer ficheiros necessários. JAR como parte da submissão.

## <a name="next-steps"></a>Passos seguintes

Para obter um exemplo de dados de processamento de Event Hubs, consulte [processar eventos provenientes dos Hubs de eventos do Azure com o Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Para obter um exemplo de uma topologia de c# que divide dados de fluxo em vários fluxos, consulte [exemplo do Storm c#](https://github.com/Blackmist/csharp-storm-example).

Para descobrir mais informações sobre como criar topologias c#, consulte o artigo [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obter mais formas de trabalhar com o HDInsight e mais Storm no HDInsight exemplos, consulte os seguintes documentos:

**Microsoft SCP.NET**

* [Guia de programação de SCP](apache-storm-scp-programming-guide.md)

**Apache Storm no HDInsight**

* [Implementar e monitorizar as topologias Apache Storm no HDInsight](apache-storm-deploy-monitor-topology.md)
* [Topologias de exemplo para Storm no HDInsight](apache-storm-example-topology.md)

**Apache Hadoop no HDInsight**

* [Utilizar o Hive com o Hadoop no HDInsight](../hadoop/hdinsight-use-hive.md)
* [Utilizar o Pig com o Hadoop no HDInsight](../hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

* [Introdução ao HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
