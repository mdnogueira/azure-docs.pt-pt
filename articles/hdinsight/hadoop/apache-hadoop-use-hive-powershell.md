---
title: Utilizar o Hive do Hadoop com o PowerShell no HDInsight - Azure | Microsoft Docs
description: Utilize o PowerShell para executar consultas do Hive no Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.openlocfilehash: 95bfab18a6a8a9ad9eb547179a3205ae4b186213
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="run-hive-queries-using-powershell"></a>Executar consultas do Hive com o PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de utilização do Azure PowerShell no modo de grupo de recursos do Azure para executar consultas do Hive num Hadoop num cluster do HDInsight.

> [!NOTE]
> Este documento não fornece uma descrição detalhada do que as declarações HiveQL que são utilizadas nos exemplos a fazem. Para obter informações sobre o HiveQL que é utilizada neste exemplo, consulte [utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md).

**Pré-requisitos**

* **Um cluster do Azure HDInsight**: é irrelevante se o cluster é Windows ou baseado em Linux.

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* **Uma estação de trabalho com o Azure PowerShell**.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>Executar consultas do Hive com o Azure PowerShell

O Azure PowerShell fornece *cmdlets* que permitem-lhe executar remotamente as consultas do Hive no HDInsight. Internamente, os cmdlets solicitar REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster do HDInsight.

Os cmdlets seguintes são utilizados quando executar consultas do Hive num cluster de HDInsight remoto:

* **Add-AzureRmAccount**: autentica o Azure PowerShell para a sua subscrição do Azure.
* **Novo AzureRmHDInsightHiveJobDefinition**: cria um *definição de tarefa* utilizando as declarações HiveQL especificadas.
* **Início AzureRmHDInsightJob**: envia a definição de tarefa no HDInsight e inicia a tarefa. A *tarefa* objeto é devolvido.
* **Espera-AzureRmHDInsightJob**: utiliza o objeto de trabalho para verificar o estado da tarefa. Deve aguardar até que a tarefa é concluída ou excedido o tempo de espera.
* **Get-AzureRmHDInsightJobOutput**: utilizado para obter o resultado da tarefa.
* **AzureRmHDInsightHiveJob invocar**: utilizada para executar declarações HiveQL. Este cmdlet blocos a consulta estiver concluída, em seguida, devolve os resultados.
* **Utilize AzureRmHDInsightCluster**: define o cluster atual para o **Invoke-AzureRmHDInsightHiveJob** comando.

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa no cluster do HDInsight:

1. Com um editor, guarde o seguinte código como **hivejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra uma nova **Azure PowerShell** linha de comandos. Altere os diretórios para a localização do **hivejob.ps1** de ficheiros, em seguida, utilize o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script é executado, lhe for pedido que introduza o nome do cluster e as credenciais da conta de administrador/HTTPS para o cluster. Também poderá ser pedido para iniciar sessão sua subscrição do Azure.

3. Quando a tarefa estiver concluída, devolve informações semelhante para o seguinte texto:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Conforme mencionado anteriormente, **Invoke-Hive** pode ser utilizado para executar uma consulta e a aguardar a resposta. Utilize o seguinte script para ver como funciona o Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída aparente ser o seguinte texto:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Para consultas de HiveQL maiores, pode utilizar o Azure PowerShell **aqui cadeias** cmdlet ou HiveQL ficheiros de script. O fragmento seguinte mostra como utilizar o **Invoke-Hive** cmdlet para executar um ficheiro de script de HiveQL. O ficheiro de script de HiveQL tem de ser carregado para wasb: / /.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre **aqui cadeias**, consulte <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">utilizando o Windows PowerShell aqui-cadeias</a>.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação é devolvida quando a tarefa é concluída, ver os registos de erros. Para ver informações de erro para esta tarefa, adicione o seguinte ao fim do **hivejob.ps1** ficheiro, guarde-o e, em seguida, execute-o novamente.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve as informações que são escritas em STDERR durante o processamento da tarefa.

## <a name="summary"></a>Resumo

Como pode ver, o Azure PowerShell fornece uma forma fácil de executar consultas do Hive num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

## <a name="next-steps"></a>Passos seguintes

Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)
