---
title: Utilizar o MapReduce e o PowerShell com o Hadoop - o Azure HDInsight | Microsoft Docs
description: Saiba como utilizar o PowerShell para executar remotamente as tarefas do MapReduce com o Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.openlocfilehash: cfed3617f20074f361629c65f14ce38c1012c702
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Executar tarefas de MapReduce com o Hadoop no HDInsight com o PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento fornece um exemplo de utilização do Azure PowerShell para executar uma tarefa de MapReduce num Hadoop num cluster do HDInsight.

## <a id="prereq"></a>Pré-requisitos

* **Um cluster do Azure HDInsight (Hadoop no HDInsight)**

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* **Uma estação de trabalho com o Azure PowerShell**.

## <a id="powershell"></a>Execute uma tarefa de MapReduce com o Azure PowerShell

O Azure PowerShell fornece *cmdlets* que permitem-lhe executar remotamente as tarefas do MapReduce no HDInsight. Internamente, PowerShell faz com que chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente denominadas Templeton) em execução no cluster do HDInsight.

Os cmdlets seguintes são utilizados quando executar tarefas de MapReduce num cluster de HDInsight remoto.

* **Login-AzureRmAccount**: autentica o Azure PowerShell para a sua subscrição do Azure.

* **Novo AzureRmHDInsightMapReduceJobDefinition**: cria um novo *definição de tarefa* utilizando as informações de MapReduce especificada.

* **Início AzureRmHDInsightJob**: envia a definição de tarefa no HDInsight e inicia a tarefa. A *tarefa* objeto é devolvido.

* **Espera-AzureRmHDInsightJob**: utiliza o objeto de trabalho para verificar o estado da tarefa. Deve aguardar até que a tarefa é concluída ou excedido o tempo de espera.

* **Get-AzureRmHDInsightJobOutput**: utilizado para obter o resultado da tarefa.

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa no cluster do HDInsight.

1. Com um editor, guarde o seguinte código como **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Abra uma nova **Azure PowerShell** linha de comandos. Altere os diretórios para a localização do **mapreducejob.ps1** de ficheiros, em seguida, utilize o seguinte comando para executar o script:

        .\mapreducejob.ps1

    Quando executar o script, serão apresentadas para o nome do cluster do HDInsight e o início de sessão do cluster. Também poderá ser solicitado para autenticar a sua subscrição do Azure.

3. Quando a tarefa estiver concluída, poderá recebe um resultado semelhante ao seguinte texto:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Este resultado indica que a tarefa foi concluída com êxito.

    > [!NOTE]
    > Se o **ExitCode** é um valor diferente de 0, consulte [resolução de problemas](#troubleshooting).

    Este exemplo também armazena os ficheiros transferidos para um **output.txt** ficheiro no diretório de executar o script.

### <a name="view-output"></a>Saída de vista

Para ver as palavras e contagens produzidas pela tarefa, abra o **output.txt** ficheiro num editor de texto.

> [!NOTE]
> Os ficheiros de saída de uma tarefa de MapReduce são imutáveis. Por isso, se voltar a executar este exemplo, terá de alterar o nome do ficheiro de saída.

## <a id="troubleshooting"></a>Resolução de Problemas

Se nenhuma informação é devolvida quando a tarefa é concluída, ver os erros para a tarefa. Para ver informações de erro para esta tarefa, adicione o seguinte comando ao fim do **mapreducejob.ps1** ficheiro, guarde-o e, em seguida, execute-o novamente.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve as informações que foi escritas em STDERR como a tarefa é executada.

## <a id="summary"></a>Resumo

Como pode ver, o Azure PowerShell fornece uma forma fácil de executar as tarefas do MapReduce num cluster do HDInsight, monitorizar o estado da tarefa e obter o resultado.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre as tarefas do MapReduce no HDInsight:

* [Utilizar o MapReduce do HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
