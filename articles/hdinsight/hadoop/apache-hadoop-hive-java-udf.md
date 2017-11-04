---
title: "Java definido pelo utilizador função (UDF com o Hive no HDInsight - Azure) | Microsoft Docs"
description: "Saiba como criar uma Java função baseada em definido pelo utilizador (UDF) que funciona com o Hive. Neste exemplo UDF converte uma tabela de cadeias de texto para minúsculas."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: larryfr
ms.openlocfilehash: 2228e9c2d48c994471a2a84161d7bff27f6a7f18
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilize um Java UDF com o Hive no HDInsight

Saiba como criar uma Java função baseada em definido pelo utilizador (UDF) que funciona com o Hive. UDF Java neste exemplo converte uma tabela de cadeias de texto em minúsculas de todos os carateres.

## <a name="requirements"></a>Requisitos

* Um cluster do HDInsight 

    > [!IMPORTANT]
    > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

    A maioria dos passos neste documento funcionam em ambos os clusters baseados em Windows e Linux. No entanto, os passos utilizados para carregar UDF compilada para o cluster e executá-la são específicos para clusters baseados em Linux. São fornecidas hiperligações para informações que podem ser utilizadas com clusters baseados em Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 ou posterior (ou um equivalente, tais como OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Um editor de texto ou IDE de Java

    > [!IMPORTANT]
    > Se criar os ficheiros de Python num cliente Windows, tem de utilizar um editor que utiliza LF como um fim de linha. Se não tem a certeza se o seu editor utiliza LF ou CRLF, consulte o [resolução de problemas](#troubleshooting) secção para obter passos sobre como remover o caráter de CR.

## <a name="create-an-example-java-udf"></a>Criar um exemplo de Java UDF 

1. A partir de uma linha de comandos, utilize o seguinte para criar um novo projeto Maven:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Se estiver a utilizar o PowerShell, tem de colocar secção os parâmetros entre aspas. Por exemplo, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Este comando cria um diretório com o nome **exampleudf**, que contém o projeto Maven.

2. Depois de criar o projeto, elimine o **exampleudf/src/teste** diretório foi criado como parte do projeto.

3. Abra o **exampleudf/pom.xml**e substituir a existente `<dependencies>` entrada com o XML dos seguinte:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Estas entradas especifiquem a versão do Hadoop e ao Hive incluídos com o HDInsight 3.5. Pode encontrar informações sobre as versões do Hadoop e ao Hive fornecido com o HDInsight a partir do [controlo de versões do HDInsight componente](../hdinsight-component-versioning.md) documento.

    Adicionar um `<build>` secção antes do `</project>` linha no final do ficheiro. Esta secção deve conter o seguinte XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.5  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
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
        </plugins>
    </build>
    ```

    Estas entradas definem como compilar o projeto. Especificamente, a versão do Java que utiliza o projeto e como criar um uberjar para a implementação para o cluster.

    Guarde o ficheiro depois das alterações foram efetuadas.

4. Mudar o nome **exampleudf/src/main/java/com/microsoft/examples/App.java** para **ExampleUDF.java**e, em seguida, abra o ficheiro no seu editor.

5. Substitua os conteúdos do **ExampleUDF.java** ficheiros com o seguinte, em seguida, guarde o ficheiro.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Este código implementa um UDF que aceita um valor de cadeia e devolve uma versão em minúsculas da cadeia.

## <a name="build-and-install-the-udf"></a>Criar e instalar UDF

1. Utilize o seguinte comando para compilar e UDF do pacote:

    ```bash
    mvn compile package
    ```

    Este comando cria e pacotes UDF para o `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` ficheiro.

2. Utilize o `scp` comandos para copiar o ficheiro para o cluster do HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Substitua `myuser` com a conta de utilizador do SSH para o cluster. Substitua `mycluster` com o nome do cluster. Se utilizou uma palavra-passe para proteger a conta do SSH, lhe for pedido que introduza a palavra-passe. Se utilizou um certificado, poderá ter de utilizar o `-i` parâmetro para especificar o ficheiro de chave privada.

3. Ligar ao cluster através de SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. A partir de sessão SSH, copie o ficheiro jar no armazenamento do HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Utilizar UDF do ramo de registo

1. Utilize o seguinte para iniciar o cliente Beeline da sessão SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Este comando assume que utilizou a predefinição de **admin** para a conta de início de sessão para o cluster.

2. Assim que chegam a `jdbc:hive2://localhost:10001/>` solicitar, introduza o seguinte para adicionar UDF ao ramo de registo e expô-la como uma função.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Este exemplo assume que o armazenamento do Azure é armazenamento predefinido para o cluster. Se o cluster utilizar o Data Lake Store em vez disso, altere o `wasb:///` valor `adl:///`.

3. Utilize UDF ao converter valores obtidos a partir de uma tabela para cadeias de minúsculas.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Esta consulta seleciona a plataforma do dispositivo (Android, Windows, iOS, etc.) da tabela, converter a cadeia de carateres inferior maiúsculas e minúsculas e, em seguida, as pode apresentar. A saída apresentado de forma semelhante para o seguinte texto:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Passos seguintes

Para outras formas de trabalhar com o Hive, consulte [utilizar o Hive com o HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre Hive User-Defined funções, consulte [operadores do ramo de registo e funções definidas pelo utilizador](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) secção da wiki do ramo de registo em apache.org.
