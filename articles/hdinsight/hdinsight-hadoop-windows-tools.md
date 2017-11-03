---
title: Utilizar um PC do Windows com o Hadoop no HDInsight - Azure | Microsoft Docs
description: "Trabalhar a partir de um PC do Windows no Hadoop no HDInsight. Gerir clusters e de consulta com ferramentas do PowerShell, o Visual Studio e Linux. Desenvolva soluções de macrodados com o .NET."
services: hdinsight
keywords: hadoop no windows, o hadoop para o windows
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: e4f231c1f9b903d6cc7f2b062b30d2a072be8493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabalhar do ecossistema do Hadoop no HDInsight a partir de um PC com Windows

Saiba mais sobre o desenvolvimento e as opções de gestão no PC Windows para trabalhar do ecossistema do Hadoop no HDInsight. 

HDInsight baseia-se no Apache Hadoop e componentes do Hadoop, tecnologias de open source desenvolvidos no Linux. HDInsight versão 3.4 e superior utiliza a distribuição de Ubuntu Linux como sistema operativo subjacente para o cluster. No entanto, pode trabalhar com o HDInsight partir de um cliente Windows ou o ambiente de desenvolvimento do Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Utilizar o PowerShell para implementação e gestão de tarefas
O Azure PowerShell é um ambiente de script que pode utilizar para controlar e automatizar a implementação e tarefas de gestão no HDInsight a partir do Windows.

Exemplos de tarefas que pode fazer com o PowerShell:

* [Criar clusters com o PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Executar consultas do Hive com o PowerShell](hdinsight-hadoop-use-hive-powershell.md)
* [Gerir clusters com o PowerShell](hdinsight-administer-use-powershell.md)

Siga os passos para [instalar e configurar o Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para obter a versão mais recente. Se tiver scripts que tenham de ser modificados para utilizar os novos cmdlets do Azure Resource Manager, consulte [migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Utilitários que pode ser executado num browser
Os seguintes utilitários tem uma IU da web do que é executado num browser:
* **[Shell de nuvem do Azure (pré-visualização)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  é uma shell interativa e da linha de comandos que é executado no seu browser e a partir do portal do Azure.
* **[IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md)**  é um gestão e monitorização utilitário disponível no portal do Azure que pode ser utilizado para gerir diferentes tipos de tarefas, tais como:
    * [Utilizar Ambari com a API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Vista Ambari Hive](hdinsight-hadoop-use-hive-ambari-view.md)
    * [Tez vista Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>N (Hadoop) do Data Lake Tools para Visual Studio
Utilize as ferramentas do Data Lake para Visual Studio para implementar e gerir topologias do Storm. Ferramentas do Data Lake também instala o SDK de SCP.NET, que permite-lhe desenvolver topologias de C# com o Visual Studio.

Antes de ir para os exemplos seguintes, [instale e tente ferramentas do Data Lake para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md). 

Exemplos de tarefas que pode fazer com o Visual Studio e as ferramentas do Data Lake para Visual Studio:
* [Implementar e gerir topologias Storm a partir do Visual Studio](hdinsight-storm-deploy-monitor-topology-linux.md)
* [Desenvolver topologias c# para Storm com o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md). O bits incluem modelos de exemplo para topologias do Storm que pode ligar às bases de dados, tais como a base de dados do Azure Cosmos e a SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio e o SDK .NET 

Pode utilizar o Visual Studio com o SDK .NET para gerir clusters e desenvolver aplicações de macrodados. Pode utilizar outros IDEs para as seguintes tarefas, mas os exemplos são mostrados no Visual Studio.

Exemplos de tarefas que pode fazer com o SDK do .NET no Visual Studio:
* [Criar clusters e trabalhar no HDInsight a partir de uma aplicação do .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Executar consultas do Hive com o SDK .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [Utilizar funções definidas pelo utilizador c# com o Hive e do Pig de transmissão em fluxo do Hadoop](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

> Sugestão Se estiver a executar as soluções de .NET com clusters do HDInsight baseados em Windows, é uma boa altura para planear uma migração para clusters baseados em Linux. Para obter mais informações, consulte [solução .NET migrar para o HDInsight baseado em Windows para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA e IDE Eclipse para clusters do Spark
Ambos [Intellij IDEA](https://www.jetbrains.com/idea/download) e [Eclipse IDE](https://www.eclipse.org/downloads/) pode ser utilizado para:
* Desenvolver e submeter uma aplicação do Scala Spark num cluster do HDInsight Spark.
* Aceder a recursos de cluster do Spark.
* Desenvolver e executar uma aplicação do Scala Spark localmente.

Estes artigos mostram como: 
* Intellij IDEA: [aplicações do Spark de criar utilizando o Toolkit do Azure para o plug-in Intellij e o SDK Scala.](hdinsight-apache-spark-intellij-tool-plugin.md)
* Eclipse IDE ou Scala IDE para o Eclipse: [aplicações criar Spark e o Toolkit de Azure do Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Blocos de notas no Spark cientistas de dados 
Clusters do Apache Spark no HDInsight incluem blocos de notas do Zeppelin e kernels que podem ser utilizados com blocos de notas do Jupyter. 

* [Saiba como utilizar kernels nos clusters do Spark com blocos de notas do Jupyter para testar aplicações do Spark](hdinsight-apache-spark-zeppelin-notebook.md)
* [Saiba como utilizar os blocos de notas do Zeppelin nos clusters do Spark para executar tarefas de Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Executar ferramentas baseadas em Linux e as tecnologias no Windows

Se encontrar uma situação em que tem de utilizar uma ferramenta ou tecnologia que só está disponível no Linux, considere as seguintes opções:

* **Bash (beta) no Windows 10** fornece um subsistema de Linux no Windows. Bash permite-lhe executar diretamente os utilitários de Linux sem necessidade de manter uma instalação de Linux dedicada. [Instalar e executar o beta de Bash no Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker para Windows** fornece acesso a várias ferramentas baseado em Linux e pode ser executada diretamente a partir do Windows. Por exemplo, pode utilizar o Docker para executar o cliente de Beeline para do Hive diretamente a partir do Windows. Pode também utilizar o Docker para executar um bloco de notas do Jupyter local e se ligam remotamente à Spark no HDInsight. [Introdução ao Docker para Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  permite-lhe navegar graficamente o sistema de ficheiros do cluster através de uma ligação SSH.

## <a name="next-steps"></a>Passos seguintes
Se estiver a trabalhar em clusters baseados em Linux, consulte os artigos de seguir:
* [Configurar Hadoop, Kafka, Spark ou outros clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Sugestões para clusters do HDInsight no Linux](hdinsight-hadoop-linux-information.md)