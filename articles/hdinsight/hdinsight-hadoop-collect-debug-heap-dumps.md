---
title: "Depurar e analisar os serviços de Hadoop com capturas de área dinâmica para dados - Azure | Microsoft Docs"
description: "Recolher informações de área dinâmica para dados de serviços do Hadoop e coloque dentro da conta de armazenamento de Blobs do Azure para depuração e de análise automaticamente."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 6d1d4d47d279eb7a1f0bf1f587445683f0ace7a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Área dinâmica para dados recolher informações de estado no armazenamento de BLOBs para depurar e analisar os serviços do Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Área dinâmica para dados capturas contém um instantâneo de memória da aplicação, incluindo os valores das variáveis no momento que a captura foi criada. Por isso, são úteis para diagnosticar problemas que ocorram em tempo de execução. Capturas de área dinâmica para dados podem ser automaticamente recolhidas para serviços do Hadoop e colocadas dentro da conta de armazenamento de Blobs do Azure de um utilizador em HDInsightHeapDumps /.

A coleção de área dinâmica para dados de Estados para vários serviços tem de estar ativada para os serviços em clusters individuais. A predefinição para esta funcionalidade é a ser desativado para um cluster. Estas capturas de área dinâmica para dados podem ser elevadas, pelo que é recomendado para monitorizar a conta do Blob storage em que estes são guardadas depois da coleção foi ativada.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). As informações neste artigo aplica-se apenas ao HDInsight baseado em Windows. Para obter informações sobre o HDInsight baseado em Linux, consulte [informações de estado da área dinâmica para dados de ativação para os serviços do Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Serviços elegíveis para capturas de área dinâmica para dados
Pode ativar a área dinâmica para dados de informações para os seguintes serviços:

* **hcatalog** -tempelton
* **ramo de registo** -hiveserver2, metastore, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager, nodemanager, timelineserver
* **hdfs** -datanode secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementos de configuração que permitem capturas de área dinâmica para dados
Para ativar as capturas de área dinâmica para dados para um serviço, tem de definir os elementos de configuração adequado na secção para esse serviço, o que é especificado por **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

O valor de **service_name** pode ser qualquer um dos serviços listados aqui: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, ou namenode.

## <a name="enable-using-azure-powershell"></a>Ativar a utilizar o Azure PowerShell
Por exemplo, para ativar as capturas de área dinâmica para dados utilizando o Azure PowerShell para jobhistoryserver, pode utilizar o script seguinte:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Ativar a utilizar o SDK do .NET
Por exemplo, para ativar a área dinâmica para dados capturas utilizando o SDK .NET da Azure HDInsight para jobhistoryserver, pode utilizar o seguinte código:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
