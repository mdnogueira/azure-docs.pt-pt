---
title: Utilizar o Pig de Hadoop com o PowerShell no HDInsight - Azure | Microsoft Docs
description: Saiba como submeter as tarefas do Pig a um cluster do Hadoop no HDInsight com o Azure PowerShell.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: b883a3d9559c2f11742cd54716d8220b2034470d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Utilizar o Azure PowerShell para executar tarefas do Pig com o HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de utilização do Azure PowerShell para submeter tarefas do Pig para um Hadoop no cluster do HDInsight. PIg permite-lhe escrever as tarefas do MapReduce através da utilização de um idioma (Pig Latin) que transformações de dados de modelos, em vez de mapear e reduzir a funções.

> [!NOTE]
> Este documento não fornece uma descrição detalhada do que as declarações de Pig Latin utilizadas nos exemplos fazem. Para obter informações sobre o Pig Latin utilizado neste exemplo, consulte [utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Pré-requisitos

* **Um cluster do Azure HDInsight**

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* **Uma estação de trabalho com o Azure PowerShell**.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a id="powershell"></a>Executar tarefas do Pig com o PowerShell

O Azure PowerShell fornece *cmdlets* que permitem-lhe executar remotamente as tarefas do Pig no HDInsight. Internamente, PowerShell utiliza chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) em execução no cluster do HDInsight.

Os cmdlets seguintes são utilizados durante a execução de tarefas do Pig num cluster do HDInsight remoto:

* **Login-AzureRmAccount**: autentica o Azure PowerShell para a sua subscrição do Azure.
* **Novo AzureRmHDInsightPigJobDefinition**: cria um *definição de tarefa* utilizando as instruções de Pig Latin especificadas.
* **Início AzureRmHDInsightJob**: envia a definição de tarefa no HDInsight e inicia a tarefa. A *tarefa* objeto é devolvido.
* **Espera-AzureRmHDInsightJob**: utiliza o objeto de trabalho para verificar o estado da tarefa. Deve aguardar até que a tarefa foi concluída ou o tempo de espera foi excedido.
* **Get-AzureRmHDInsightJobOutput**: utilizado para obter o resultado da tarefa.

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa em cluster do HDInsight.

1. Com um editor, guarde o seguinte código como **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Abra uma nova linha de comandos do Windows PowerShell. Altere os diretórios para a localização do **pigjob.ps1** de ficheiros, em seguida, utilize o seguinte comando para executar o script:

        .\pigjob.ps1

    Lhe for pedido para iniciar sessão na sua subscrição do Azure. Em seguida, é-lhe pedido para o nome de conta de HTTPs/Admin e a palavra-passe para o cluster do HDInsight.

2. Quando a tarefa estiver concluída, este deverá devolver informações semelhantes para o seguinte texto:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Resolução de Problemas

Se nenhuma informação é devolvida quando a tarefa é concluída, ver os registos de erros. Para ver informações de erro para esta tarefa, adicione o seguinte comando ao fim do **pigjob.ps1** ficheiro, guarde-o e, em seguida, execute-o novamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Este cmdlet devolve as informações que foi escritas em STDERR durante o processamento da tarefa.

## <a id="summary"></a>Resumo
Como pode ver, o Azure PowerShell fornece uma forma fácil de executar tarefas do Pig num cluster do HDInsight, monitorizar o estado da tarefa e obter o resultado.

## <a id="nextsteps"></a>Passos seguintes
Para obter informações gerais sobre o Pig no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)
