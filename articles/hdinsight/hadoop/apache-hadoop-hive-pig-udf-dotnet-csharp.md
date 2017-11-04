---
title: Utilizar c# com o Hive e do Pig do Hadoop no HDInsight - Azure | Microsoft Docs
description: "Saiba como utilizar c# definido pelo utilizador funções (UDF) com o Hive e Pig de transmissão em fluxo no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: 38b6c153905d556bc9407a6cb5531a46ca17f5eb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Utilizar funções definidas pelo utilizador c# com o Hive e do Pig de transmissão em fluxo do Hadoop no HDInsight

Saiba como utilizar c# definido pelo utilizador funções (UDF) com Apache Hive e do Pig no HDInsight.

> [!IMPORTANT]
> Os passos neste documento trabalham com clusters do HDInsight baseados em Windows e Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controlo de versões do HDInsight componente](../hdinsight-component-versioning.md).

Tanto o Hive e Pig pode passar dados em aplicações externas para processamento. Este processo é conhecido como _de transmissão em fluxo_. Quando utilizar uma aplicação .NET, os dados são transmitidos para a aplicação STDIN e a aplicação devolve os resultados no STDOUT. Para ler e escrever de STDIN e STDOUT, pode utilizar `Console.ReadLine()` e `Console.WriteLine()` partir de uma aplicação de consola.

## <a name="prerequisites"></a>Pré-requisitos

* Uma familiaridade com escrever e a criação de código c# que vise o .NET Framework 4.5.

    * Utilize qualquer IDE quiser. Recomendamos [Visual Studio](https://www.visualstudio.com/vs) versão 2015, 2017, ou [Visual Studio Code](https://code.visualstudio.com/). Os passos neste documento utilizam o Visual Studio 2017.

* Uma forma para carregar ficheiros de .exe para o cluster e executadas tarefas de Pig e do Hive. Recomendamos que as ferramentas do Data Lake para Visual Studio, o Azure PowerShell e o CLI do Azure. Os passos em utilização deste documento as ferramentas do Data Lake para Visual Studio carregar os ficheiros e executar o exemplo do Hive consulta.

    Para obter informações sobre outras formas de executar consultas e tarefas do Pig de ramo de registo, consulte os seguintes documentos:

    * [Utilizar o Apache Hive com o HDInsight](hdinsight-use-hive.md)

    * [Utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md)

* Um Hadoop no cluster do HDInsight. Para obter mais informações sobre como criar um cluster, consulte [criar um cluster do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET no HDInsight

* __HDInsight baseado em Linux__ clusters com [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicações de .NET. Versão mono 4.2.1 está incluído com o HDInsight versão 3.5.

    Para obter mais informações sobre Mono compatibilidade com versões do .NET Framework, consulte [compatibilidade Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

    Para utilizar uma versão específica do Mono, consulte o [instalação ou atualização Mono](../hdinsight-hadoop-install-mono.md) documento.

* __HDInsight baseado em Windows__ clusters utilizam o Microsoft .NET CLR para executar aplicações de .NET.

Para obter mais informações sobre a versão do .NET framework e Mono incluído com as versões do HDInsight, consulte [as versões de componentes do HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar o C\# projetos

### <a name="hive-udf"></a>Ramo de registo UDF

1. Abra o Visual Studio e criar uma solução. Para o tipo de projeto, selecione **aplicação de consola (.NET Framework)**e atribua o nome do novo projeto **HiveCSharp**.

    > [!IMPORTANT]
    > Selecione __.NET Framework 4.5__ se estiver a utilizar um cluster do HDInsight baseado em Linux. Para obter mais informações sobre Mono compatibilidade com versões do .NET Framework, consulte [compatibilidade Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

2. Substitua os conteúdos de **Program.cs** com o seguinte código:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Crie o projeto.

### <a name="pig-udf"></a>PIg UDF

1. Abra o Visual Studio e criar uma solução. Para o tipo de projeto, selecione **aplicação de consola**e atribua o nome do novo projeto **PigUDF**.

2. Substitua os conteúdos do **Program.cs** ficheiro com o seguinte código:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Este código analisa as linhas enviadas do Pig e reformata linhas que começam com `java.lang.Exception`.

3. Guardar **Program.cs**e, em seguida, criar o projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

1. No Visual Studio, abra **Explorador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Se lhe for solicitado, introduza as credenciais da sua subscrição do Azure e, em seguida, clique em **sessão**.

4. Expanda o cluster do HDInsight que pretende implementar esta aplicação. Uma entrada com o texto __(conta de armazenamento predefinida)__ está listado.

    ![Explorador de servidores que mostra a conta de armazenamento para o cluster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Se esta entrada pode ser expandida, estiver a utilizar um __conta do Storage do Azure__ como armazenamento de predefinido para o cluster. Para ver os ficheiros de armazenamento de predefinido para o cluster, expanda a entrada e, em seguida, faça duplo clique o __(contentor predefinido)__.

    * Se esta entrada não pode ser expandida, estiver a utilizar __Azure Data Lake Store__ como armazenamento de predefinido para o cluster. Para ver os ficheiros de armazenamento de predefinido para o cluster, faça duplo clique o __(conta de armazenamento predefinida)__ entrada.

6. Para carregar os ficheiros de .exe, utilize um dos seguintes métodos:

    * Se utilizar um __conta do Storage do Azure__, clique no ícone de carregamento e, em seguida, procure o **bin\debug** pasta para o **HiveCSharp** projeto. Por fim, selecione o **HiveCSharp.exe** do ficheiro e clique em **Ok**.

        ![Carregue ícone](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Se utilizar __Azure Data Lake Store__, faça duplo clique numa área vazia na listagem de ficheiros e, em seguida, selecione __carregar__. Por fim, selecione o **HiveCSharp.exe** do ficheiro e clique em **abra**.

    Uma vez a __HiveCSharp.exe__ carregamento foi concluída, repita o processo de carregamento para o __PigUDF.exe__ ficheiro.

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. No Visual Studio, abra **Explorador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Clique no cluster que tenha implementado o **HiveCSharp** aplicação para e, em seguida, selecione **escrever uma consulta do Hive**.

4. Utilize o seguinte texto para a consulta do Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Anule o comentário de `add file` instrução que corresponde ao tipo de armazenamento predefinido utilizado para o cluster.

    Esta consulta seleciona o `clientid`, `devicemake`, e `devicemodel` campos do `hivesampletable`e transmite os campos para a aplicação de HiveCSharp.exe. A consulta espera que a aplicação para devolver os três campos que são armazenados como `clientid`, `phoneLabel`, e `phoneHash`. A consulta também espera encontrar HiveCSharp.exe na raiz do contentor de armazenamento predefinido.

5. Clique em **submeter** para submeter a tarefa ao cluster do HDInsight. O **resumo da tarefa do Hive** é aberta a janela.

6. Clique em **atualizar** para atualizar o resumo até **estado da tarefa** alterações **concluído**. Para ver o resultado da tarefa, clique em **resultado da tarefa**.

## <a name="run-a-pig-job"></a>Execute uma tarefa Pig

1. Utilize um dos seguintes métodos para ligar ao cluster do HDInsight:

    * Se estiver a utilizar um __baseado em Linux__ HDInsight cluster, utilizar o SSH. Por exemplo, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Para obter mais informações, consulte [withHDInsight utilizar o SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Se estiver a utilizar um __baseados em Windows__ cluster do HDInsight, [ligar ao cluster através de ambiente de trabalho remoto](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Utilize um o seguinte comando para iniciar a linha de comandos do Pig:

        pig

    > [!IMPORTANT]
    > Se estiver a utilizar um cluster baseado no Windows, utilize os seguintes comandos em vez disso:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` linha é apresentada.

3. Introduza o seguinte para executar uma tarefa Pig que utiliza a aplicação do .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    O `DEFINE` instrução cria um alias de `streamer` para as aplicações de pigudf.exe e `CACHE` carrega-o de armazenamento predefinido para o cluster. Mais tarde, `streamer` é utilizado com o `STREAM` operador para processar as linhas único contidas no registo e devolver os dados como uma série de colunas.

    > [!NOTE]
    > O nome da aplicação que é utilizado para transmissão em fluxo tem de ser rodeado pelo \` (backtick) caráter quando um alias, e ' (aspa simples) quando utilizado com `SHIP`.

4. Depois de introduzir a última linha, deve iniciar a tarefa. Devolve resultados semelhantes para o seguinte texto:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a utilizar uma aplicação .NET Framework do Hive e do Pig no HDInsight. Se gostaria de aprender a utilizar o Python com o Hive e Pig, consulte [utilizar Python com o Hive e Pig no HDInsight](python-udf-hdinsight.md).

Para outras formas de utilizar o Pig e ramo de registo e para saber mais sobre como utilizar o MapReduce, consulte os seguintes documentos:

* [Utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
