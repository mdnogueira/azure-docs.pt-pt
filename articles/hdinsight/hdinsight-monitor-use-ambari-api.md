---
title: Monitorizar clusters do Hadoop no HDInsight utilizando a API do Ambari - Azure | Microsoft Docs
description: Utilize as APIs do Apache Ambari para criar, gerir e monitorizar clusters do Hadoop. APIs e ferramentas de operador intuitiva ocultam a complexidade do Hadoop.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: b6fc2098027690eb76b69b1427f0e9541b8a7a69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Monitorize clusters Hadoop no HDInsight usando a API do Ambari
Saiba como monitorizar clusters do HDInsight ao utilizar as APIs do Ambari.

> [!NOTE]
> As informações neste artigo são principalmente para os clusters do HDInsight baseado em Windows, que fornecem uma versão só de leitura da API de REST do Ambari. Para clusters baseados em Linux, consulte [clusters do Hadoop de gerir com o Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>O que é o Ambari?
[Apache Ambari] [ ambari-home] é utilizado para o aprovisionamento, gestão e monitorização de clusters do Apache Hadoop. Inclui uma coleção intuitiva de ferramentas de operador intuitiva e um conjunto robusto de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Para obter mais informações sobre as APIs, consulte [referência da API do Ambari][ambari-api-reference]. 

Atualmente, o HDInsight suporta apenas a funcionalidade de monitorização do Ambari. Ambari API 1.0 é suportado pelo clusters do HDInsight versão 3.0 e 2.1. Este artigo abrange ao aceder aos APIs Ambari em clusters do HDInsight versão 3.1 e 2.1. A principal diferença entre os dois é que alguns dos componentes foram alteradas com a introdução das novas capacidades (por exemplo, o servidor de histórico da tarefa). 

**Pré-requisitos**

Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma estação de trabalho com o Azure PowerShell**.
* (Opcional) [cURL][curl]. Para instalá-lo, consulte o artigo [cURL versões e transferências][curl-download].
  
  > [!NOTE]
  > Quando utilizar o comando cURL no Windows, utilize double-aspas em vez de aspas dos único para os valores de opção.
  > 
  > 
* **Um cluster do Azure HDInsight**. Para obter instruções sobre o aprovisionamento de cluster, consulte [começar a utilizar o HDInsight] [ hdinsight-get-started] ou [aprovisionar clusters do HDInsight][hdinsight-provision]. Terá dos dados para seguir o tutorial:
  
  | Propriedade do cluster | Nome da variável do PowerShell do Azure | Valor | Descrição |
  | --- | --- | --- | --- |
  |   Nome de cluster do HDInsight |$clusterName | |O nome do cluster do HDInsight. |
  |   Nome de utilizador do cluster |$clusterUsername | |Especificar o nome de utilizador do cluster quando o cluster foi criado. |
  |   Palavra-passe do cluster |$clusterPassword | |Palavra-passe de utilizador de cluster. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Iniciar
Existem várias formas de utilizar Ambari para monitorizar clusters do HDInsight.

**Utilizar o Azure PowerShell**

O seguinte script do PowerShell do Azure obtém as informações de controlador de tarefa de MapReduce *num cluster HDInsight 3.5.*  A principal diferença é que podemos solicitar estes detalhes do serviço YARN (em vez de MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

O script do PowerShell seguinte obtém as informações de controlador de tarefa de MapReduce *num cluster HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

O resultado é:

![Saída de Jobtracker][img-jobtracker-output]

**Utilizar cURL**

O exemplo seguinte obtém informações do cluster utilizando cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

O resultado é:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Para a versão 8/10/2014**:

Ao utilizar o ponto final do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o *host_name* campo devolve o nome de domínio completamente qualificado (FQDN) do nó em vez do nome de anfitrião. Antes do lançamento 8/10/2014, neste exemplo devolvido simplesmente "**headnode0**". Depois da versão 8/10/2014, obterá o FQDN "**headnode0. { . Azurehdinsight.NET de ClusterDNS} .net**", conforme mostrado no exemplo anterior. Esta alteração é exigida para facilitar os cenários em que os vários tipos de cluster (por exemplo, o HBase e Hadoop) podem ser implementados numa rede virtual (VNET). Isto acontece, por exemplo, quando utilizar o HBase como uma plataforma de back-end para o Hadoop.

## <a name="ambari-monitoring-apis"></a>APIs de monitorização do Ambari
A tabela seguinte lista algumas do Ambari mais comuns monitorizar chamadas de API. Para obter mais informações sobre a API, consulte [referência da API do Ambari][ambari-api-reference].

| Chamada de API do monitor | URI | Descrição |
| --- | --- | --- |
| Obter clusters |`/api/v1/clusters` | |
| Obter as informações de cluster. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |clusters, serviços, anfitriões |
| Obter os serviços |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Os serviços incluem: hdfs, mapreduce |
| Obter as informações de serviços. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Obter os componentes de serviço |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Obter as informações de componente. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, os componentes do anfitrião, as métricas |
| Obter anfitriões |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Obter as informações de anfitrião. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Obter os componentes de anfitrião |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Obter informações de componente de anfitrião. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, componente, anfitrião, métricas |
| Obter configurações |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Tipos de configuração: site principal, hdfs local, mapred site, site do hive |
| Obter as informações de configuração. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Tipos de configuração: site principal, hdfs local, mapred site, site do hive |

## <a name="next-steps"></a>Passos Seguintes
Agora tem aprendeu a utilizar Ambari monitorizar chamadas de API. Para saber mais, consulte:

* [Gerir clusters do HDInsight através do portal do Azure][hdinsight-admin-portal]
* [Gerir clusters do HDInsight com o Azure PowerShell][hdinsight-admin-powershell]
* [Gerir clusters do HDInsight através da interface de linha de comandos][hdinsight-admin-cli]
* [Documentação do HDInsight][hdinsight-documentation]
* [Introdução ao HDInsight][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
