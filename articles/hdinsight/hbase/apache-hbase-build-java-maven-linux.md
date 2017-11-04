---
title: Cliente de Java HBase - Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar o Apache Maven para criar uma aplicação baseada em Java Apache HBase, em seguida, implementá-la para o HBase no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: 
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: larryfr
ms.openlocfilehash: 07f6d49b6d5c0a5b77ef5a7a7395a37e79419955
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="build-java-applications-for-apache-hbase"></a>Criar aplicações de Java para o Apache HBase

Saiba como criar um [Apache HBase](http://hbase.apache.org/) aplicação em Java. Em seguida, utilize a aplicação com o HBase no Azure HDInsight.

Os passos neste documento utilize [Maven](http://maven.apache.org/) para criar e compilar o projeto. Maven é uma ferramenta de compreensão que lhe permite criar software, documentação e relatórios para projetos de Java e gestão do projeto de software.

> [!NOTE]
> Os passos neste documento mais recentemente foram testados com 3.6 do HDInsight.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="requirements"></a>Requisitos

* [Plataforma de Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 8 ou posterior.

    > [!NOTE]
    > HDInsight 3.5 e maior requer Java 8. Versões anteriores do HDInsight requerem Java 7.

* [Maven](http://maven.apache.org/)

* [Um cluster baseado em Linux do Azure HDInsight com o HBase](apache-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

  > [!NOTE]
  > Os passos neste documento foi testados com as versões de cluster do HDInsight 3.4 e 3.5. Os valores predefinidos fornecidos nos exemplos são para um cluster de HDInsight 3.5.

## <a name="create-the-project"></a>Criar o projeto

1. Na linha de comandos no seu ambiente de desenvolvimento, altere os diretórios para a localização onde pretende criar o projeto, por exemplo, `cd code\hbase`.

2. Utilize o **mvn** comando, que é instalado com o Maven, para gerar o andaime para o projeto.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > Se estiver a utilizar o PowerShell, tem de incluir o `-D` parâmetros entre aspas duplas.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Este comando cria um diretório com o mesmo nome que o **artifactID** parâmetro (**hbaseapp** neste exemplo.) Este diretório contém os seguintes itens:

   * **pom.XML**: O modelo de objeto do projeto ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém detalhes de informações e configurações utilizados para compilar o projeto.
   * **SRC**: O diretório que contém o **main/java/com/microsoft/exemplos** directory, onde cria a aplicação.

3. Eliminar o `src/test/java/com/microsoft/examples/apptest.java` ficheiro. Não é possível utilizar este exemplo.

## <a name="update-the-project-object-model"></a>Atualizar o modelo de objeto do projeto

1. Editar o `pom.xml` de ficheiros e adicione o seguinte código dentro do `<dependencies>` secção:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    Esta secção indica que precisa do projeto **hbase-cliente** e **phoenix-core** componentes. No momento da compilação, estas dependências são transferidas do repositório de Maven predefinido. Pode utilizar o [pesquisa de repositório Central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre esta dependência.

   > [!IMPORTANT]
   > O número de versão do hbase-cliente tem de corresponder à versão do HBase que é fornecido com o cluster do HDInsight. Utilize a tabela seguinte para determinar o número de versão correta.

   | Clusters do HDInsight versão | Versão do HBase para utilizar |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 e 3.6 |1.1.2 |

    Para obter mais informações sobre as versões do HDInsight e componentes, consulte [quais são os diferentes componentes do Hadoop disponíveis com o HDInsight](../hdinsight-component-versioning.md).

3. Adicione o seguinte código para o **pom.xml** ficheiro. Este texto tem de estar dentro do `<project>...</project>` etiquetas no ficheiro, por exemplo, entre `</dependencies>` e `</project>`.

   ```xml
    <build>
        <sourceDirectory>src</sourceDirectory>
        <resources>
        <resource>
            <directory>${basedir}/conf</directory>
            <filtering>false</filtering>
            <includes>
            <include>hbase-site.xml</include>
            </includes>
        </resource>
        </resources>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
            </plugin>
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
        </plugins>
    </build>
   ```

    Esta secção configura um recurso (`conf/hbase-site.xml`) que contém informações de configuração para o HBase.

   > [!NOTE]
   > Também pode definir valores de configuração através de código. Veja os comentários no `CreateTable` exemplo.

    Esta secção também configura a [Plug-in do Maven compilador](http://maven.apache.org/plugins/maven-compiler-plugin/) e [Plug-in do Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/). O compilador de plug-in é utilizado para compilar a topologia. Shade Plug-in é utilizado para evitar a duplicação de licença no pacote JAR que é composta por Maven. Este plug-in é utilizado para evitar um erro de "ficheiros de licença duplicado" em tempo de execução no cluster do HDInsight. Utilizar maven-shade-Plug-in com o `ApacheLicenseResourceTransformer` implementação impede o erro.

    O maven-shade-Plug-in também produz um jar uber que contém todas as dependências necessárias para a aplicação.

4. Guardar o `pom.xml` ficheiro.

5. Criar um diretório com o nome `conf` no `hbaseapp` diretório. Este diretório é utilizado para conter as informações de configuração para ligar ao HBase.

6. Utilize o seguinte comando para copiar a configuração do HBase do cluster HBase para o `conf` diretório. Substitua `USERNAME` com o nome de início de sessão SSH. Substitua `CLUSTERNAME` com o nome de cluster do HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   Para obter mais informações sobre como utilizar `ssh` e `scp`, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Criar a aplicação

1. Vá para o `hbaseapp/src/main/java/com/microsoft/examples` diretório e o app.java de mudar o nome do ficheiro para `CreateTable.java`.

2. Abra o `CreateTable.java` de ficheiros e substitua os conteúdos existentes com o seguinte texto:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;
    import org.apache.hadoop.hbase.HTableDescriptor;
    import org.apache.hadoop.hbase.TableName;
    import org.apache.hadoop.hbase.HColumnDescriptor;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Put;
    import org.apache.hadoop.hbase.util.Bytes;

    public class CreateTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Example of setting zookeeper values for HDInsight
        // in code instead of an hbase-site.xml file
        //
        // config.set("hbase.zookeeper.quorum",
        //            "zookeepernode0,zookeepernode1,zookeepernode2");
        //config.set("hbase.zookeeper.property.clientPort", "2181");
        //config.set("hbase.cluster.distributed", "true");
        //
        //NOTE: Actual zookeeper host names can be found using Ambari:
        //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

        //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
        config.set("zookeeper.znode.parent","/hbase-unsecure");

        // create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // create the table...
        HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
        // ... with two column families
        tableDescriptor.addFamily(new HColumnDescriptor("name"));
        tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
        admin.createTable(tableDescriptor);

        // define some people
        String[][] people = {
            { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
            { "2", "Franklin", "Holtz", "franklin@contoso.com" },
            { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
            { "4", "Rae", "Schroeder", "rae@contoso.com" },
            { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
            { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

        HTable table = new HTable(config, "people");

        // Add each person to the table
        //   Use the `name` column family for the name
        //   Use the `contactinfo` column family for the email
        for (int i = 0; i< people.length; i++) {
            Put person = new Put(Bytes.toBytes(people[i][0]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
            person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
            table.put(person);
        }
        // flush commits and close the table
        table.flushCommits();
        table.close();
        }
    }
   ```

    Este código é o **CreateTable** classe, que cria uma tabela com o nome **pessoas** e preenchê-lo com alguns utilizadores predefinidas.

3. Guardar o `CreateTable.java` ficheiro.

4. No `hbaseapp/src/main/java/com/microsoft/examples` directory, crie um ficheiro denominado `SearchByEmail.java`. Utilize o seguinte texto como o conteúdo deste ficheiro:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Scan;
    import org.apache.hadoop.hbase.client.ResultScanner;
    import org.apache.hadoop.hbase.client.Result;
    import org.apache.hadoop.hbase.filter.RegexStringComparator;
    import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
    import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
    import org.apache.hadoop.hbase.util.Bytes;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class SearchByEmail {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Use GenericOptionsParser to get only the parameters to the class
        // and not all the parameters passed (when using WebHCat for example)
        String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
        if (otherArgs.length != 1) {
            System.out.println("usage: [regular expression]");
            System.exit(-1);
        }

        // Open the table
        HTable table = new HTable(config, "people");

        // Define the family and qualifiers to be used
        byte[] contactFamily = Bytes.toBytes("contactinfo");
        byte[] emailQualifier = Bytes.toBytes("email");
        byte[] nameFamily = Bytes.toBytes("name");
        byte[] firstNameQualifier = Bytes.toBytes("first");
        byte[] lastNameQualifier = Bytes.toBytes("last");

        // Create a regex filter
        RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
        // Attach the regex filter to a filter
        //   for the email column
        SingleColumnValueFilter filter = new SingleColumnValueFilter(
            contactFamily,
            emailQualifier,
            CompareOp.EQUAL,
            emailFilter
        );

        // Create a scan and set the filter
        Scan scan = new Scan();
        scan.setFilter(filter);

        // Get the results
        ResultScanner results = table.getScanner(scan);
        // Iterate over results and print  values
        for (Result result : results ) {
            String id = new String(result.getRow());
            byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
            String firstName = new String(firstNameObj);
            byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
            String lastName = new String(lastNameObj);
            System.out.println(firstName + " " + lastName + " - ID: " + id);
            byte[] emailObj = result.getValue(contactFamily, emailQualifier);
            String email = new String(emailObj);
            System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
        }
        results.close();
        table.close();
        }
    }
   ```

    O **SearchByEmail** classe pode ser utilizada para consultar linhas por endereço de correio eletrónico. Porque utiliza um filtro de expressão regular, pode fornecer uma cadeia ou uma expressão regular quando utilizar a classe.

5. Guardar o `SearchByEmail.java` ficheiro.

6. No `hbaseapp/src/main/hava/com/microsoft/examples` directory, crie um ficheiro denominado `DeleteTable.java`. Utilize o seguinte texto como o conteúdo deste ficheiro:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;

    public class DeleteTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // Disable, and then delete the table
        admin.disableTable("people");
        admin.deleteTable("people");
        }
    }
   ```

    Esta classe limpa as tabelas de HBase criadas neste exemplo, desativando e remover a tabela criada pelo `CreateTable` classe.

7. Guardar o `DeleteTable.java` ficheiro.

## <a name="build-and-package-the-application"></a>Criar e a aplicação do pacote

1. Do `hbaseapp` diretório, utilize o seguinte comando para criar um ficheiro JAR que contém a aplicação:

    ```bash
    mvn clean package
    ```

    Este comando cria e pacotes de aplicação para um ficheiro. JAR.

2. Quando o comando for concluído, o `hbaseapp/target` diretório contém um ficheiro denominado `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]
   > O `hbaseapp-1.0-SNAPSHOT.jar` ficheiro é um jar uber. Contém todas as dependências necessárias para executar a aplicação.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Carregar o JAR e executar tarefas (SSH)

Os passos seguintes utilize `scp` para copiar o JAR ao nó principal do principal do HBase no cluster do HDInsight. O `ssh` comando, em seguida, é utilizado para ligar ao cluster e executar o exemplo diretamente no nó principal.

1. Para carregar o jar para o cluster, utilize o seguinte comando:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Substitua `USERNAME` com o nome de início de sessão SSH. Substitua `CLUSTERNAME` com o nome de cluster do HDInsight.

2. Para ligar ao HBase cluster, utilize o seguinte comando:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua `USERNAME` o nome de início de sessão SSH. Substitua `CLUSTERNAME` com o nome de cluster do HDInsight.

3. Para criar uma tabela de HBase utilizando a aplicação de Java, utilize o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Este comando cria uma tabela de HBase com o nome **pessoas**e preenche-lo com dados.

4. Para procurar armazenados na tabela de endereços de correio eletrónico, utilize o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Receber os seguintes resultados:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Para eliminar a tabela, utilize o seguinte comando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Carregar o JAR e executar tarefas (PowerShell)

Os seguintes passos utilizam o Azure PowerShell para carregar o JAR no armazenamento de predefinido para o cluster de HBase. Cmdlets do HDInsight, em seguida, são utilizados para executar os exemplos remotamente.

1. Depois de instalar e configurar o Azure PowerShell, crie um ficheiro denominado `hbase-runner.psm1`. Utilize o seguinte texto como o conteúdo deste ficheiro:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzureStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Este ficheiro contém dois módulos:

   * **HDInsightFile adicionar** - utilizado para carregar ficheiros para o cluster
   * **Início HBaseExample** - utilizada para executar as classes que criou anteriormente

2. Guardar o `hbase-runner.psm1` ficheiro.

3. Abra uma nova janela do PowerShell do Azure, altere os diretórios para o `hbaseapp` diretório e, em seguida, execute o seguinte comando:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Altere o caminho para a localização do `hbase-runner.psm1` ficheiro criado anteriormente. Este comando regista o módulo com o Azure PowerShell.

4. Utilize o seguinte comando para carregar o `hbaseapp-1.0-SNAPSHOT.jar` ao cluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Substitua `hdinsightclustername` pelo nome do cluster. O comando carrega o `hbaseapp-1.0-SNAPSHOT.jar` para o `example/jars` localização no armazenamento do primário para o cluster.

5. Para criar uma tabela utilizando o `hbaseapp`, utilize o seguinte comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Substitua `hdinsightclustername` pelo nome do cluster.

    Este comando cria uma tabela com o nome **pessoas** no HBase no cluster do HDInsight. Este comando não mostra resultados na janela da consola.

6. Para procurar entradas na tabela, utilize o seguinte comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Substitua `hdinsightclustername` pelo nome do cluster.

    Este comando utiliza o `SearchByEmail` classe para pesquisar as linhas em que o `contactinformation` família de colunas e o `email` coluna, contém a cadeia `contoso.com`. Deverá receber os seguintes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Utilizar **fabrikam.com** para o `-emailRegex` valor devolve os utilizadores que tenham **fabrikam.com** no campo do e-mail. Pode também utilizar expressões regulares como o termo de pesquisa. Por exemplo, **^ r** devolve e-mail endereços que começam com a letra "r".

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Não existem resultados ou resultados inesperados quando utilizar HBaseExample de início

Utilize o `-showErr` parâmetro para ver o erro padrão (STDERR) que é produzido ao executar a tarefa.

## <a name="delete-the-table"></a>Eliminar a tabela

Quando tiver terminado com o exemplo, utilize o seguinte para eliminar o **pessoas** tabela utilizada neste exemplo:

__De um `ssh` sessão__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__A partir do Azure PowerShell__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>Passos seguintes

[Saiba como utilizar o SQuirreL SQL com HBase](apache-hbase-phoenix-squirrel-linux.md)
