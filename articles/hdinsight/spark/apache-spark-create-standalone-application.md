---
title: "Criar aplicação Scala para executar em clusters do Spark - Azure HDInsight | Microsoft Docs"
description: "Crie uma aplicação de Spark escrita no Scala com Apache Maven como o sistema de compilação e um archetype Maven existente para Scala fornecida o IntelliJ IDEA."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 24becbf5f539ac031d6f5b217a4c5a6b59f572f8
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Criar uma aplicação de Scala Maven para executar no cluster do Apache Spark no HDInsight

Saiba como criar uma aplicação de Spark escrita no Scala com o Maven com o IntelliJ IDEA. O artigo utiliza o Apache Maven como o sistema de compilação e começa com um archetype Maven existente para Scala fornecida o IntelliJ IDEA.  Criar uma aplicação Scala no IntelliJ IDEA envolve os seguintes passos:

* Utilize Maven como o sistema de compilação.
* Atualize o ficheiro de modelo de objeto do projeto (POM) para resolver dependências do módulo de Spark.
* Escreva a sua aplicação no Scala.
* Gere um ficheiro jar que pode ser submetido para clusters do HDInsight Spark.
* Execute a aplicação no cluster do Spark com o Livy.

> [!NOTE]
> O HDInsight também fornece uma ferramenta de plug-in IntelliJ IDEA para facilitar o processo de criar e submeter aplicações para um cluster do HDInsight Spark no Linux. Para obter mais informações, consulte [Plug-in ferramentas de HDInsight de utilização para o IntelliJ IDEA criar e submeter aplicações do Spark](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Kit de desenvolvimento Java Oracle. Pode instalar a [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Um Java IDE. Este artigo utiliza o IntelliJ IDEA 15.0.1. Pode instalar a [aqui](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA
Se a instalação IntelliJ IDEA não foi possível pedir para ativar o plug-in do Scala, inicie o IntelliJ IDEA e efetuar os passos seguintes para instalar o plug-in:

1. Inicie o IntelliJ IDEA e a partir do ecrã de boas-vindas, clique em **configurar** e, em seguida, clique em **plug-ins**.
   
    ![Ativar o plug-in do scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. No ecrã seguinte, clique em **Plug-in de instalar JetBrains** do canto inferior esquerdo. No **Procurar plug-ins de JetBrains** caixa de diálogo que se abre, procure Scala e, em seguida, clique em **instalar**.
   
    ![Instalar o plug-in do scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. Depois do plug-in instala com êxito, clique em de **botão de reiniciar o IntelliJ IDEA** para reiniciar o IDE.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autónomo
1. Inicie o IntelliJ IDEA e criar um novo projeto. Na caixa de diálogo de novo projeto, selecione as seguintes opções e, em seguida, clique em **seguinte**.
   
    ![Criar projeto Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Selecione **Maven** como tipo de projeto.
   * Especifique um **projeto SDK**. Clique em novo e navegue para o diretório de instalação de Java, normalmente `C:\Program Files\Java\jdk1.8.0_66`.
   * Selecione o **criar do archetype** opção.
   * Na lista de archetypes, selecione **org.scala tools.archetypes:scala-archetype simples**. Isto irá criar a estrutura de diretórios à direita e transferir as dependências de predefinido necessário escrever Scala programa.
2. Fornecer valores relevantes para **GroupId**, **ArtifactId**, e **versão**. Clique em **Seguinte**.
3. Na caixa de diálogo seguinte, onde pode Especifica diretório raiz do Maven e outras definições de utilizador, aceite as predefinições e clique em **seguinte**.
4. Na caixa de diálogo última, especifique um nome de projeto e a localização e, em seguida, clique em **concluir**.
5. Eliminar o **MySpec.Scala** de ficheiros no **src\test\scala\com\microsoft\spark\example**. Não é necessário isto para a aplicação.
6. Se necessário, mude o nome de ficheiros de origem e de teste predefinidos. No painel esquerdo no IntelliJ IDEA, navegue para **src\main\scala\com.microsoft.spark.example**. Clique com botão direito **App.scala**, clique em **Refatorar**, clique em mudança de nome de ficheiro, na caixa de diálogo, forneça o novo nome para a aplicação e, em seguida, clique em **Refatorar**.
   
    ![Mudar o nome de ficheiros](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. Nos passos subsequentes, atualizará o pom.xml para definir as dependências para a aplicação do Spark Scala. Para essas dependências ser transferidas e resolvido automaticamente, tem de configurar Maven em conformidade.
   
    ![Configurar o Maven para transferências automáticas](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Do **ficheiro** menu, clique em **definições**.
   2. No **definições** diálogo caixa, navegue para **de compilação, execução, implementação** > **ferramentas de criação** > **Maven**  >  **Importar**.
   3. Selecione a opção para **automaticamente projetos do Maven importação**.
   4. Clique em **aplicar**e, em seguida, clique em **OK**.
8. Atualize o ficheiro de origem Scala para incluir o código da aplicação. Abra e substitua o código de exemplo existente pelo código seguinte e guardar as alterações. Este código lê os dados de HVAC.csv (disponível em todos os clusters do HDInsight Spark), obtém as linhas que têm apenas um dígito na coluna sexto e escreve a saída para **/HVACOut** sob o contentor de armazenamento predefinido para o cluster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Atualize o pom.xml.
   
   1. Dentro do `<project>\<properties>` adicione o seguinte:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Dentro do `<project>\<dependencies>` adicione o seguinte:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Guarde as alterações pom.xml.
10. Crie o ficheiro. JAR. IntelliJ IDEA permite a criação de JAR como um artefacto de um projeto. Execute os seguintes passos.
    
    1. Do **ficheiro** menu, clique em **estrutura do projeto**.
    2. No **estrutura do projeto** caixa de diálogo, clique em **artefactos** e, em seguida, clique no símbolo de adição. Na caixa de diálogo de pop-up, clique em **JAR**e, em seguida, clique em **de módulos com dependências**.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. No **criar JAR de módulos** diálogo caixa, clique nas reticências (![reticências](./media/apache-spark-create-standalone-application/ellipsis.png) ) contra o **classe principal**.
    4. No **selecione classe de Main** diálogo caixa, seleccione a classe que é apresentado por predefinição e, em seguida, clique em **OK**.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. No **criar JAR de módulos** diálogo caixa, certifique-se de que a opção de **extrair para o destino JAR** está selecionada e, em seguida, clique em **OK**. Esta ação cria um único JAR com todas as dependências.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. O separador de esquema de saída apresenta uma lista de todos os v7 que está incluídos como parte do projeto Maven. Pode selecionar e eliminar as no qual a aplicação de Scala não tem nenhum dependência direta. Para a aplicação que estamos a criar aqui, pode remover todos os mas último (**SparkSimpleApp compilação saída**). Selecione o v7 para eliminar e, em seguida, clique em de **eliminar** ícone.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Certifique-se **tirar partido da marca** caixa está selecionada, que garante que o jar é criada sempre que o projeto é criado ou atualizado. Clique em **aplicar** e, em seguida, **OK**.
    7. Na barra de menus, clique em **criar**e, em seguida, clique em **tornar projeto**. Também pode clicar em **criar artefactos** para criar o jar. O jar de saída é criado no **\out\artifacts**.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Executar a aplicação no cluster do Spark
Para executar a aplicação no cluster, terá de efetuar o seguinte:

* **Copie o jar de aplicação para o blob storage do Azure** associados com o cluster. Pode utilizar [ **AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comandos, para o fazer. Existem muitos outros clientes, bem como que pode utilizar para carregar dados. Pode encontrar mais informações sobre-las em [carregar dados para tarefas do Hadoop no HDInsight](../hdinsight-upload-data.md).
* **Utilize o Livy para submeter uma tarefa de aplicação remotamente** para o cluster do Spark. Clusters do Spark no HDInsight inclui o Livy que expõe os pontos finais REST para submeter remotamente tarefas de Spark. Para obter mais informações, consulte [tarefas do Spark submeter clusters do remotamente com o Livy com o Spark no HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Passo seguinte

Neste artigo, aprendeu como criar uma aplicação do Spark scala. Avançar para o artigo seguinte para saber como executar esta aplicação num cluster do Spark do HDInsight com o Livy.

> [!div class="nextstepaction"]
>[Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

