---
title: Criar Java MapReduce para o Hadoop - o Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar o Apache Maven para criar uma aplicação baseada em Java MapReduce, em seguida, execute-o com o Hadoop no HDInsight do Azure."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/03/2017
ms.author: larryfr
ms.openlocfilehash: 75f52e09d141cd009127d46acf93cbab7fc6a4f6
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Desenvolver programas de Java MapReduce para o Hadoop no HDInsight

Saiba como utilizar o Apache Maven para criar uma aplicação baseada em Java MapReduce, em seguida, execute-o com o Hadoop no HDInsight do Azure.

> [!NOTE]
> Neste exemplo mais recentemente foi testado em 3.6 do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 ou posterior (ou um equivalente, tais como OpenJDK).
    
    > [!NOTE]
    > As versões do HDInsight 3.4 e anteriores utilizam Java 7. HDInsight 3.5 e maior utiliza Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

As seguintes variáveis de ambiente podem ser definidas quando instalar o Java e o JDK. No entanto, deve verificar que existe e que contêm os valores corretos para o seu sistema.

* `JAVA_HOME`-devem apontar para o diretório onde o ambiente de tempo de execução Java (JRE) está instalado. Por exemplo, num sistema OS X, Unix ou Linux, deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, terá um valor semelhante a`c:\Program Files (x86)\Java\jre1.7`

* `PATH`-deve conter os seguintes caminhos:
  
  * `JAVA_HOME`(ou o caminho equivalente)

  * `JAVA_HOME\bin`(ou o caminho equivalente)

  * O diretório onde está instalado o Maven

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Partir de uma sessão de terminal, ou uma linha de comandos no seu ambiente de desenvolvimento, altere os diretórios para a localização que pretende armazenar este projeto.

2. Utilize o `mvn` comando, que é instalado com o Maven, para gerar o andaime para o projeto.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Se estiver a utilizar o PowerShell, tem de incluir o `-D` parâmetros entre aspas duplas.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Este comando cria um diretório com o nome especificado pelo `artifactID` parâmetro (**wordcountjava** neste exemplo.) Este diretório contém os seguintes itens:

   * `pom.xml`-O [modelo de objeto do projeto (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém detalhes de configuração e informações utilizados para compilar o projeto.

   * `src`-O diretório que contém a aplicação.

3. Eliminar o `src/test/java/org/apache/hadoop/examples/apptest.java` ficheiro. Não é utilizada neste exemplo.

## <a name="add-dependencies"></a>Adicione as dependências

1. Editar o `pom.xml` de ficheiros e adicione o seguinte texto no interior do `<dependencies>` secção:
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    Isto define bibliotecas necessárias (listados na &lt;artifactId\>) com uma versão específica (listados na &lt;versão\>). No momento da compilação, estas dependências são transferidas do repositório de Maven predefinido. Pode utilizar o [pesquisa de repositório do Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver mais informações.
   
    O `<scope>provided</scope>` indica Maven que estas dependências não devem ser compactadas com a aplicação, como forem fornecidos pelo cluster do HDInsight em tempo de execução.

    > [!IMPORTANT]
    > A versão utilizada deve corresponder à versão do Hadoop presente no seu cluster. Para obter mais informações sobre as versões, consulte o [controlo de versões do HDInsight componente](../hdinsight-component-versioning.md) documento.

2. Adicione o seguinte para o `pom.xml` ficheiro. Este texto tem de estar dentro do `<project>...</project>` etiquetas no ficheiro; por exemplo, entre `</dependencies>` e `</project>`.

   ```xml
    <build>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
            </configuration>
            <executions>
            <execution>
                <phase>package</phase>
                    <goals>
                    <goal>shade</goal>
                    </goals>
            </execution>
            </executions>
            </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    O plug-in primeiro configura a [Plug-in do Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), que é utilizada para criar um uberjar (por vezes denominada uma fatjar), que contém as dependências necessárias para a aplicação. Também impede que duplicação de licenças no pacote jar, que pode causar problemas em alguns sistemas.

    O segundo Plug-in configura a versão de Java de destino.

    > [!NOTE]
    > HDInsight 3.4 e utilização anterior Java 7. HDInsight 3.5 e maior utiliza Java 8.

3. Guardar o `pom.xml` ficheiro.

## <a name="create-the-mapreduce-application"></a>Criar a aplicação de MapReduce

1. Vá para o `wordcountjava/src/main/java/org/apache/hadoop/examples` diretório e mude o nome a `App.java` do ficheiro para `WordCount.java`.

2. Abra o `WordCount.java` ficheiro num editor de texto e substitua o conteúdo com o seguinte texto:
   
    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```
   
    Tenha em atenção é o nome do pacote `org.apache.hadoop.examples` e o nome de classe é `WordCount`. Utilize estes nomes de ao submeter a tarefa de MapReduce.

3. Guarde o ficheiro.

## <a name="build-the-application"></a>Criar a aplicação

1. Mude para o `wordcountjava` diretório, se não estiver já existe.

2. Utilize o seguinte comando para criar um ficheiro JAR que contém a aplicação:

   ```
   mvn clean package
   ```

    Este comando cleans quaisquer artefactos de compilação anterior, transfere quaisquer dependências que já não tenham sido instaladas, e, em seguida, compilações e pacote da aplicação.

3. Depois do comando for concluído, o `wordcountjava/target` diretório contém um ficheiro denominado `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > O `wordcountjava-1.0-SNAPSHOT.jar` ficheiro é um uberjar, que contém não apenas a tarefa de WordCount, mas também dependências que requer que a tarefa no tempo de execução.

## <a id="upload"></a>Carregar o jar

Utilize o seguinte comando para carregar o ficheiro jar para o HDInsight headnode:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Este comando copia os ficheiros do sistema local para o nó principal. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Executar a tarefa de MapReduce no Hadoop

1. Ligar ao HDInsight através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A partir de sessão SSH, utilize o seguinte comando para executar a aplicação de MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Este comando inicia a aplicação de WordCount MapReduce. O ficheiro de entrada é `/example/data/gutenberg/davinci.txt`, e o diretório de saída é `/example/data/wordcountout`. O ficheiro de entrada e de saída são armazenadas no armazenamento de predefinido para o cluster.

3. Uma vez concluída a tarefa, utilize o seguinte comando para ver os resultados:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Deverá receber uma lista de palavras e contagens, com valores semelhantes ao seguinte texto:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Passos seguintes

Neste documento, aprendeu como desenvolver uma tarefa de MapReduce de Java. Consulte os seguintes documentos para outras formas de trabalhar com o HDInsight.

* [Utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações, consulte também o [Centro de programadores Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

