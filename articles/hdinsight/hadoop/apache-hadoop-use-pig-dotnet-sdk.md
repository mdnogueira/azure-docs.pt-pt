---
title: Executar tarefas do Apache Pig com o .NET SDK para o Hadoop - Azure HDInsight | Microsoft Docs
description: Saiba como utilizar o SDK .NET do Hadoop para submeter tarefas do Pig ao Hadoop no HDInsight.
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: fa11d49a-328c-47e7-b16d-e7ed2a453195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/08/2017
ms.author: larryfr
ms.openlocfilehash: c828a7b63e70669ed38ecea898442a3978e67ba7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Executar tarefas do Pig utilizando o SDK .NET para o Hadoop no HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Saiba como utilizar o SDK .NET do Hadoop para submeter tarefas de Apache Pig ao Hadoop no Azure HDInsight.

O SDK .NET do HDInsight fornece bibliotecas de cliente do .NET que torna mais fácil trabalhar com clusters do HDInsight a partir do .NET. PIg permite-lhe criar operações de MapReduce por uma série de transformações de dados de modelação. Neste documento, irá aprender a utilizar uma aplicação básica c# para submeter uma tarefa de Pig para um cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa do seguinte.

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (ou Windows ou baseado em Linux).

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Visual Studio 2012 2013, 2015 ou de 2017.

## <a name="create-the-application"></a>Criar a aplicação

O SDK .NET do HDInsight fornece bibliotecas de cliente .NET, que torna mais fácil trabalhar com clusters do HDInsight a partir do .NET.

1. Do **ficheiro** menu no Visual Studio, selecione **novo** e, em seguida, selecione **projeto**.

2. Para o novo projeto, escreva ou selecione os seguintes valores:

   | Propriedade | Valor |
   | ------ | ------ |
   | Categoria | Templates/Visual C#/Windows |
   | Modelo | Aplicação de Consola |
   | Nome | SubmitPigJob |

3. Clique em **OK** para criar o projeto.

4. Do **ferramentas** menu, selecione **Gestor de pacotes de biblioteca** ou **Gestor de pacotes NuGet**e, em seguida, selecione **consola do Gestor de pacotes**.

5. Para instalar os pacotes de SDK do .NET, utilize o seguinte comando:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. A partir do Explorador de soluções, faça duplo clique em **Program.cs** para abri-lo. Substitua o código existente com o seguinte.

    ```csharp
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

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Para iniciar a aplicação, prima **F5**.

8. Para sair da aplicação, prima **ENTER**.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o Pig no HDInsight, consulte [utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md).

Para obter mais informações sobre como utilizar o Hadoop no HDInsight, consulte os seguintes documentos:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
