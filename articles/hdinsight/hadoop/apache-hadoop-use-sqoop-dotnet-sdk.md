---
title: Executar tarefas de Sqoop utilizando o .NET e o HDInsight - Azure | Microsoft Docs
description: Saiba como utilizar o SDK .NET do HDInsight para executar Sqoop importar e exportar entre um cluster do Hadoop e uma base de dados SQL do Azure.
keywords: tarefa de sqoop
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: 27bd93896718bf54ea3d483671b11070e8c274ec
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Executar tarefas de Sqoop utilizando o .NET SDK para o Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o SDK .NET da Azure HDInsight para executar tarefas de Sqoop no HDInsight para importar e exportar entre um cluster do HDInsight e uma base de dados SQL do Azure ou a base de dados do SQL Server.

> [!NOTE]
> Apesar de poder utilizar os procedimentos neste artigo com uma um cluster do HDInsight baseado em Windows ou Linux, que utilizam apenas a partir de um cliente Windows. Para optar por outros métodos, utilize o Seletor de separador na parte superior deste artigo.
> 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o item seguinte:

* Um cluster do Hadoop no HDInsight. Para obter mais informações, consulte [criar um cluster e uma base de dados do SQL Server](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Utilizar o Sqoop nos clusters do HDInsight com o SDK .NET
O SDK .NET do HDInsight fornece bibliotecas de cliente .NET, para que seja mais fácil trabalhar com clusters do HDInsight a partir do .NET. Nesta secção, vai criar uma aplicação de consola c# para exportar o hivesampletable para a tabela de SQL Database do Azure que criou anteriormente neste tutorial.

## <a name="submit-a-sqoop-job"></a>Submeter uma tarefa de Sqoop

1. Crie uma aplicação de consola c# no Visual Studio.

2. Na consola do Gestor de pacotes do Visual Studio, importe o pacote executando o seguinte comando do NuGet:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Utilize o seguinte código no ficheiro Program.cs:
   
        using System.Collections.Generic;
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Para executar o programa, selecione o **F5** chave. 

## <a name="limitations"></a>Limitações
HDInsight baseado em Linux apresenta as seguintes limitações:

* Em massa exportação: Sqoop o conector, que é utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.

* Criação de batches: utilizando o `-batch` mudar quando executa inserções, Sqoop efetua várias inserções em vez das operações de inserção de criação de batches.

## <a name="next-steps"></a>Passos seguintes
Agora tem aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Oozie com o HDInsight](../hdinsight-use-oozie.md): Utilize Sqoop ação um fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voo utilizando HDInsight](../hdinsight-analyze-flight-delay-data.md): utilizar o Hive para analisar voo atrasar dados e, em seguida, utilizar o Sqoop para exportar dados para uma base de dados SQL do Azure.
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md): localizar outros métodos para carregar dados para armazenamento HDInsight ou Blob do Azure.

