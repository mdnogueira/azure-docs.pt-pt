---
title: Executar consultas do Hive com o SDK .NET do HDInsight - Azure | Microsoft Docs
description: Saiba como submeter tarefas do Hadoop para o Azure HDInsight Hadoop utilizando o SDK .NET do HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 4e291890-f8b4-426c-b5e8-d4fd512ff042
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jgao
ms.openlocfilehash: e9ac97aa31e9909584bfc5debdfba927d4884114
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Executar consultas do Hive com o SDK .NET do HDInsight
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como submeter consultas do Hive com o SDK .NET do HDInsight. Escreva um programa c# para submeter uma consulta do Hive para listar as tabelas do Hive e apresentar os resultados.

> [!NOTE]
> Os passos neste artigo devem ser executados a partir de um cliente Windows. Para informações sobre como utilizar um Linux, OS X ou cliente Unix para trabalhar com o Hive, utilize o Seletor de separador apresentado na parte superior do artigo.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter os seguintes itens:

* **Um cluster do Hadoop no HDInsight**. Consulte [começar a utilizar o Hadoop baseado em Linux no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > A partir do dia 15 de Setembro de 2017, o SDK .NET do HDInsight suporta apenas os resultados devolvidos da consulta do Hive das contas do Storage do Azure. Se utilizar este exemplo com um cluster do HDInsight que utiliza o Azure Data Lake Store como armazenamento primário, não é possível obter os resultados da pesquisa utilizando o SDK .NET.

* **Visual Studio 2013/2015/2017**.

## <a name="submit-hive-queries-using-hdinsight-net-sdk"></a>Submeter consultas do Hive com o SDK .NET do HDInsight
O SDK .NET do HDInsight fornece bibliotecas de cliente .NET, que torna mais fácil trabalhar com clusters do HDInsight a partir do .NET. 

**Para submeter tarefas**

1. Crie uma aplicação de consola c# no Visual Studio.
2. A partir da consola do Gestor de pacotes Nuget, execute o seguinte comando:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Utilize o seguinte código:

    ```csharp
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
    ```
4. Prima **F5** para executar a aplicação.

O resultado da aplicação deverá ser semelhante a:

![Resultado da tarefa do Hive do Hadoop HDInsight](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a várias formas de criar um cluster do HDInsight. Para obter mais informações, consulte os artigos seguintes:

* [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Criar clusters do Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Gerir clusters do Hadoop no HDInsight ao utilizar o portal do Azure](../hdinsight-administer-use-management-portal.md)
* [Referência do SDK .NET do HDInsight](https://msdn.microsoft.com/library/mt271028.aspx)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar o Sqoop com o HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Criar aplicações do HDInsight de .NET de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


