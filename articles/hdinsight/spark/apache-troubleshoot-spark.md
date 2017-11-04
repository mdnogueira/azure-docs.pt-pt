---
title: Resolver problemas do Spark utilizando o Azure HDInsight | Microsoft Docs
description: "Obtenha respostas a questões recorrentes sobre como trabalhar com Apache Spark e o Azure HDInsight."
keywords: "O Azure HDInsight, Spark, FAQ, manual, problemas comuns, configuração da aplicação, Ambari de resolução de problemas"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: b75aca7d9f78cb425099cc33034b44a80d7b6b81
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Resolver problemas do Spark utilizando o Azure HDInsight

Saiba mais sobre os principais problemas e as resoluções ao trabalhar com payloads do Apache Spark no Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>Como configurar a uma aplicação de Spark utilizando Ambari em clusters

### <a name="resolution-steps"></a>Passos de resolução

Os valores de configuração para este procedimento anteriormente foram definidos no HDInsight. Para determinar qual o Spark configurações tem de ser definidas e para os quais os valores, consulte o artigo [o que faz com que um Spark exceção de aplicação de OutofMemoryError](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. Na lista de clusters, selecionar **Spark2**.

    ![Selecione o cluster a partir da lista](./media/apache-troubleshoot-spark/update-config-1.png)

2. Selecione o **folhas** separador.

    ![Selecione o separador de documento](./media/apache-troubleshoot-spark/update-config-2.png)

3. Na lista de configurações, selecione **predefinições de spark2 personalizada**.

    ![Selecione as predefinições do spark personalizada](./media/apache-troubleshoot-spark/update-config-3.png)

4. Procure a definição do valor que precisa para ajustar, tais como **spark.executor.memory**. Neste caso, o valor de **4608m** é demasiado elevada.

    ![Selecione o campo de spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Defina o valor para a definição recomendada. O valor **2048m** é recomendada para esta definição.

    ![Altere o valor a 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Guarde o valor e, em seguida, guarde a configuração. Na barra de ferramentas, selecione **guardar**.

    ![Guardar a definição e configuração](./media/apache-troubleshoot-spark/update-config-6a.png)

    Será notificado se quaisquer configurações necessitem de atenção. Tenha em atenção os itens e, em seguida, selecione **continuar mesmo assim**. 

    ![Selecione continuar mesmo assim](./media/apache-troubleshoot-spark/update-config-6b.png)

    Escrever uma nota sobre as alterações de configuração e, em seguida, selecione **guardar**.

    ![Introduza uma nota sobre as alterações que efetuou](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Sempre que uma configuração é guardada, lhe for pedido para reiniciar o serviço. Selecione **reiniciar**.

    ![Selecione o reinício](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirme o reinício.

    ![Selecione confirmar todas reinicie](./media/apache-troubleshoot-spark/update-config-7b.png)

    Pode rever os processos que estão em execução.

    ![Rever os processos em execução](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Pode adicionar configurações. Na lista de configurações, selecione **predefinições de spark2 personalizada**e, em seguida, selecione **Adicionar propriedade**.

    ![Selecione Adicionar propriedade](./media/apache-troubleshoot-spark/update-config-8.png)

9. Defina uma nova propriedade. Pode definir uma propriedade de único ao utilizar uma caixa de diálogo para as definições específicas, tais como o tipo de dados. Em alternativa, pode definir várias propriedades utilizando uma definição por linha. 

    Neste exemplo, o **spark.driver.memory** propriedade estiver definida com um valor de **4g**.

    ![Definir a propriedade novo](./media/apache-troubleshoot-spark/update-config-9.png)

10. Guardar a configuração e, em seguida, reinicie o serviço, conforme descrito nos passos 6 e 7.

Estas alterações são em todo o cluster, mas podem ser substituídas ao submeter a tarefa de Spark.

### <a name="additional-reading"></a>Leitura adicional

[Submissão da tarefa de Spark nos clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como configurar a uma aplicação de Spark através da utilização de um bloco de notas do Jupyter em clusters

### <a name="resolution-steps"></a>Passos de resolução

1. Para determinar qual o Spark configurações tem de ser definidas e para os quais os valores, consulte o artigo [o que faz com que um Spark exceção de aplicação de OutofMemoryError](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Na primeira célula do bloco de notas do Jupyter, depois do **% % configurar** diretiva, especifique as configurações do Spark no formato JSON válido. Altere os valores reais, conforme necessário:

    ![Adicionar uma configuração](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Leitura adicional

[Submissão da tarefa de Spark nos clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>Como configurar uma aplicação de Spark a utilizando o Livy em clusters?

### <a name="resolution-steps"></a>Passos de resolução

1. Para determinar qual o Spark configurações tem de ser definidas e para os quais os valores, consulte o artigo [o que faz com que um Spark exceção de aplicação de OutofMemoryError](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Submeta a aplicação de Spark para o Livy utilizando um cliente REST como cURL. Utilize um comando semelhante ao seguinte. Altere os valores reais, conforme necessário:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Leitura adicional

[Submissão da tarefa de Spark nos clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>Como configurar a um aplicação através da utilização do spark-submeter o Spark nos clusters

### <a name="resolution-steps"></a>Passos de resolução

1. Para determinar qual o Spark configurações tem de ser definidas e para os quais os valores, consulte o artigo [o que faz com que um Spark exceção de aplicação de OutofMemoryError](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Inicie o shell de spark utilizando um comando semelhante ao seguinte. Altere o valor real das configurações conforme necessário: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Leitura adicional

[Submissão da tarefa de Spark nos clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>O que faz com que um Spark exceção de aplicação de OutofMemoryError?

### <a name="detailed-description"></a>Descrição detalhada

A aplicação de Spark falha, com os seguintes tipos de exceções não identificadas:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Causa provável

A causa mais provável desta exceção é que a memória insuficiente área dinâmica para dados está alocada para as máquinas virtuais de Java (JVMs). Estes JVMs são iniciadas como executores ou controladores como parte da aplicação de Spark. 

### <a name="resolution-steps"></a>Passos de resolução

1. Determinar o tamanho máximo dos dados de Spark identificadores de aplicação. Pode efetuar um estimado com base no tamanho máximo de dados de entrada, os dados intermédios que são produzidos por transformar os dados de entrada e os dados de saída são produzidos quando a aplicação é ainda mais transformar os dados intermédios. Este processo pode ser um iterativo se não é possível efetuar um estimado formal inicial. 

2. Certifique-se de que o cluster do HDInsight que vai utilizar tem recursos suficientes em termos de memória e de núcleos para acomodar a aplicação de Spark. Pode realizar esta visualizando a secção de métricas de cluster da IU do YARN para os valores de **memória utilizada** vs. **Total de memória**, e **VCores utilizado** vs. **Total de VCores**.

3. Defina as seguintes configurações de Spark para os valores apropriados, não devem ter mais de 90% de memória disponível e núcleos. Os valores devem ser bem dentro os requisitos de memória da aplicação Spark: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Para a memória total utilizada por todos os executores calcuate: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Para o total de memória utilizado pelo controlador calcuate:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Leitura adicional

- [Descrição geral da gestão de memória do Spark](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Depurar uma aplicação de Spark num cluster do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Veja Também
[Resolver problemas utilizando o Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)

