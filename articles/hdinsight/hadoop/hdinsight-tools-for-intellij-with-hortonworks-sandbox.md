---
title: Utilize o Toolkit do Azure para o IntelliJ com Hortonworks Sandbox | Microsoft Docs
description: Saiba como utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ com Hortonworks Sandbox.
keywords: ferramentas hadoop hive consulta, intellij, hortonworks sandbox, toolkit do azure para o intellij
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: 8e63499258da7cfbdd288419d262ce41d9de4559
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox

Saiba como utilizar as ferramentas do HDInsight para o IntelliJ para desenvolver aplicações Apache Scala e, em seguida, teste as aplicações no [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) em execução no seu computador. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) é um ambiente de desenvolvimento integrado (IDE) de Java para o desenvolvimento de software do computador. Depois de desenvolver e testar as aplicações no Hortonworks Sandbox, pode mover as aplicações [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

- Hortonworks Data Platform (HDP) 2.4 da Hortonworks Sandbox em execução no computador local. Para configurar HDP, consulte [introdução ao ecossistema do Hadoop com um sandbox de Hadoop numa máquina virtual](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > As ferramentas do HDInsight para o IntelliJ foi testada apenas com HDP 2.4. Para obter HDP 2.4, expanda **Hortonworks Sandbox arquivo** no [Hortonworks Sandbox transfere site](http://hortonworks.com/downloads/#sandbox).

- [Kit de programação Java (JDK) versão 1.8 ou posterior](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Toolkit do Azure para o IntelliJ requer JDK.

- [Edição de Comunidade IntelliJ IDEA](https://www.jetbrains.com/idea/download) com o [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) Plug-in e [Toolkit do Azure para o IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) Plug-in. As ferramentas do HDInsight para o IntelliJ está disponível como parte do Toolkit do Azure para o IntelliJ. 

Para instalar os plug-ins:

  1. Abra o IntelliJ IDEA.
  2. No **boas-vindas** página, selecione **configurar**e, em seguida, selecione **plug-ins**.
  3. No canto inferior esquerdo, selecione **Plug-in de instalar JetBrains**.
  4. Utilize a função de procura para procurar **Scala**e, em seguida, selecione **instalar**.
  5. Para concluir a instalação, selecione **reiniciar o IntelliJ IDEA**.
  6. Repita os passos 4 e 5 para instalar **Toolkit do Azure para o IntelliJ**. Para obter mais informações, consulte [instalar o Toolkit do Azure para o IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-a-spark-scala-application"></a>Criar uma aplicação do Spark Scala

Nesta secção, crie um projeto de Scala exemplo utilizando o IntelliJ IDEA. Na secção seguinte, ligação IntelliJ IDEA para a Hortonworks Sandbox (emulador) antes de submeter o projeto.

1. Abra o IntelliJ IDEA no seu computador. No **novo projeto** diálogo caixa, conclua estes passos:

   1. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.
   2. No **ferramenta de compilação** lista, selecione uma das seguintes ações, com base no seu cenário:

    * **Maven**: suporte de Assistente de criação do projeto para Scala.
    * **SBT**: para gerir as dependências e a criação do projeto Scala.

   ![A caixa de diálogo novo projeto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Selecione **seguinte**.
3. As próximas **novo projeto** diálogo caixa, conclua os seguintes passos:

    1. No **nome do projeto** box, introduza um nome de projeto.
    2. No **localização do projeto** box, introduza uma localização de projeto.
    3. Junto a **projeto SDK** na lista pendente, selecione **novo**, selecione **JDK**, e, em seguida, especifique a pasta para Java JDK versão 1.7 ou posterior. Selecione **1.8 do Java** para o cluster do Spark 2. x. Selecione **Java 1.7** para o cluster do Spark 1. x. A localização predefinida é c:\Programas\Microsoft Files\Java\jdk1.8.x_xxx.
    4. No **versão Spark** na lista pendente, o Assistente de criação de projeto Scala integra-se a versão correta para o SDK de Spark e Scala SDK. Se a versão de cluster do Spark é anterior ao 2.0, selecione **Spark 1. x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza Spark 1.6.2 (Scala 2.10.5). Certifique-se de que está a utilizar o repositório marcado **Scala 2.10.x**. Não utilize o repositório marcado Scala 2.11.x.
    
    ![Criar IntelliJ Scala propriedades do projeto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Selecione **Concluir**.
5. Se o **projeto** vista ainda não estiver aberta, prima **Alt + 1** para abri-lo.
6. No **Explorador de projeto**, expanda o projeto e, em seguida, selecione **src**.
7. Clique com botão direito **src**, aponte para **novo**e, em seguida, selecione **Scala classe**.
8. No **nome** caixa, introduza um nome. No **tipo** caixa, selecione **objeto**. Em seguida, selecione **OK**.

    ![A caixa de diálogo Criar nova classe de Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. No ficheiro .scala, cole o seguinte código:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. No **criar** menu, selecione **compilação do projeto**. Certifique-se de que a compilação é concluída com êxito.


## <a name="link-to-the-hortonworks-sandbox"></a>Ligação para a Sandbox Hortonworks

Pode ligar a uma Hortonworks Sandbox (emulador), tem de ter uma aplicação IntelliJ existente.

Para ligar a um emulador:

1. Abra o projeto in IntelliJ.
2. No **vista** menu, selecione **ferramentas Windows**e, em seguida, selecione **Explorador do Azure**.
3. Expanda **Azure**, faça duplo clique **HDInsight**e, em seguida, selecione **associar um emulador**.
4. No **ligação um novo emulador** caixa de diálogo, introduza a palavra-passe que definiu para a conta de raiz do Hortonworks Sandbox. Em seguida, introduza valores semelhantes às utilizadas na captura de ecrã seguinte. Em seguida, selecione **OK**. 

   ![A ligação de uma caixa de diálogo novo emulador](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Para configurar o emulador, selecione **Sim**.

Quando o emulador está ligado com êxito, o emulador (Hortonworks Sandbox) está listado no nó de HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>A aplicação do Spark Scala para a Hortonworks Sandbox de envio

Depois de ter associado o IntelliJ IDEA para o emulador, pode submeter o seu projeto.

Para submeter um projeto para um emulador:

1. No **Explorador de projeto**, com o botão direito no projeto e, em seguida, selecione **aplicação submeter Spark no HDInsight**.
2. Conclua os seguintes passos:

    1. No **cluster do Spark (apenas Linux)** pendente lista, selecione o Hortonworks Sandbox local.
    2. No **nome da classe principal** caixa, selecione ou introduza o nome da classe principal. Para este tutorial, o nome é **GroupByTest**.

3. Selecione **submeter**. Os registos de submissão da tarefa são apresentados na janela de ferramenta de submissão de Spark.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ para criar Spark aplicações para um cluster do HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Para obter um vídeo sobre as ferramentas do HDInsight para o IntelliJ, consulte [introduz as ferramentas do HDInsight para o IntelliJ para o desenvolvimento de Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Saiba como [remotamente depurar aplicações do Spark num cluster do HDInsight com o Toolkit do Azure para o IntelliJ através de SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Saiba como [utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ para depurar aplicações do Spark remotamente num cluster do HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Saiba como [utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse criar Spark aplicações](../spark/apache-spark-eclipse-tool-plugin.md).

- Para obter um vídeo sobre as ferramentas do HDInsight para o Eclipse, consulte [utilizar as ferramentas do HDInsight para o Eclipse criar Spark aplicações](https://mix.office.com/watch/1rau2mopb6fha).
