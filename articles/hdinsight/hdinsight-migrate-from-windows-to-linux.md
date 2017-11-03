---
title: Migrar do HDInsight baseados em Windows para o HDInsight baseado em Linux - Azure | Microsoft Docs
description: Saiba como migrar a partir de um cluster do HDInsight baseado em Windows para um cluster do HDInsight baseado em Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: f2695d4f15fe984cd02cba9ff66033b90d0a4dc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrar a partir de um cluster do HDInsight baseados em Windows para um cluster baseado em Linux

Este documento fornece detalhes sobre as diferenças entre o HDInsight no Windows e Linux. Também fornece orientações sobre como migrar cargas de trabalho existentes para um cluster baseado em Linux.

Enquanto HDInsight baseado em Windows fornece uma forma fácil de utilizar o Hadoop na nuvem, poderá ter de migrar para um cluster baseado em Linux. Por exemplo, para tirar partido das tecnologias que são necessárias para a sua solução e ferramentas baseadas em Linux. Muitos aspetos do ecossistema do Hadoop são desenvolvidos em sistemas baseados em Linux e poderão não estar disponíveis para utilização com o HDInsight baseado em Windows. Muitos documentação, vídeos e outros materiais de formação partem do princípio de que está a utilizar um sistema Linux ao trabalhar com o Hadoop.

> [!NOTE]
> Clusters do HDInsight utilizam o suporte de longa duração Ubuntu (LTS) como o sistema operativo para os nós do cluster. Para obter informações sobre a versão do Ubuntu disponível com o HDInsight, juntamente com outras informações de controlo de versões do componente, consulte [as versões de componentes do HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tarefas de migração

Segue-se o fluxo de trabalho geral para a migração.

![Diagrama de fluxo de trabalho de migração](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Cada secção deste documento para compreender as alterações que poderão ser necessárias quando migrar de leitura.

2. Crie um cluster baseado em Linux como um ambiente de garantia de teste/qualidade. Para obter mais informações sobre como criar um cluster baseado em Linux, consulte [baseado em Linux criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Copie as tarefas existentes, origens de dados e sinks para o novo ambiente.

4. Execute testes de validação para se certificar de que as tarefas funcionam como esperado no novo cluster.

Depois de verificar que tudo funciona conforme esperado, agende o período de indisponibilidade para a migração. Durante este período de indisponibilidade, efetue as seguintes ações:

1. Cópia de segurança de todos os dados transitórios armazenados localmente em nós de cluster. Por exemplo, se tiver dados armazenados diretamente num nó principal.

2. Elimine o cluster baseado no Windows.

3. Crie um cluster baseado em Linux utilizando o mesmo arquivo de dados predefinida que o cluster baseado em Windows utilizado. O cluster baseado em Linux pode continuar a trabalhar contra os dados de produção existentes.

4. Importe dados transitórios que uma cópia de segurança.

5. Iniciar tarefas/continuar a processar utilizando o novo cluster.

### <a name="copy-data-to-the-test-environment"></a>Copiar dados para o ambiente de teste

Existem vários métodos para copiar os dados e tarefas, no entanto as duas abordadas nesta secção são os métodos mais simples para diretamente move ficheiros para um cluster de teste.

#### <a name="hdfs-copy"></a>HDFS cópia

Utilize os seguintes passos para copiar dados do cluster de produção para o cluster de teste. Estes passos utilizam o `hdfs dfs` utilitário que está incluído com o HDInsight.

1. Localize as informações de contentor de armazenamento conta e o predefinido, para o seu cluster existente. O exemplo seguinte utiliza o PowerShell para obter estas informações:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Para criar um ambiente de teste, siga os passos em clusters baseados em Linux criar HDInsight documento. Parar antes de criar o cluster e, em vez disso, selecione **configuração opcional**.

3. Na secção de configuração opcionais, selecione **contas de armazenamento ligadas**.

4. Selecione **adicionar uma chave de armazenamento**e quando lhe for pedido, selecione a conta de armazenamento que foi devolvida pelo script do PowerShell no passo 1. Clique em **selecione** em cada secção. Por fim, crie o cluster.

5. Depois de criar o cluster, ligar a ela com **SSH.** Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. A partir de sessão SSH, utilize o seguinte comando para copiar ficheiros da conta do storage ligadas para a nova conta do storage predefinida. Substitua o contentor com as informações de contentor devolvidas pelo PowerShell. Substitua __conta__ com o nome da conta. Substitua o caminho para os dados com o caminho para um ficheiro de dados.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Se a estrutura do diretório que contém os dados não existe no ambiente de teste, pode criá-la utilizando o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    O `-p` comutador permite a criação de todos os diretórios no caminho.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Cópia direta entre os blobs no armazenamento do Azure

Em alternativa, pode pretender utilizar o `Start-AzureStorageBlobCopy` cmdlet PowerShell do Azure para copiar os blobs entre contas de armazenamento fora do HDInsight. Para obter mais informações, consulte a forma como para gerir a secção de Blobs do Azure de utilizar o Azure PowerShell com o Storage do Azure.

## <a name="client-side-technologies"></a>Tecnologias do lado do cliente

Tecnologias do lado do cliente, tais como [cmdlets Azure PowerShell](/powershell/azureps-cmdlets-docs), [CLI do Azure](../cli-install-nodejs.md), ou o [SDK .NET para o Hadoop](https://hadoopsdk.codeplex.com/) continuem a funcionar clusters baseados em Linux. Estas tecnologias baseiam-se nas APIs REST, que são os mesmos em ambos os tipos de SO do cluster.

## <a name="server-side-technologies"></a>Tecnologias do lado do servidor

A tabela seguinte fornece orientações sobre migrar componentes do lado do servidor que são específicas do Windows.

| Se estiver a utilizar esta tecnologia... | Esta ação... |
| --- | --- |
| **PowerShell** (scripts do lado do servidor, incluindo as ações de Script utilizada durante a criação do cluster) |Reescrever como scripts de Bash. Para ações de Script, consulte [HDInsight baseado em Linux personalizar com ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Script desenvolvimento de ações para o HDInsight baseado em Linux](hdinsight-hadoop-script-actions-linux.md). |
| **CLI do Azure** (scripts do lado do servidor) |Enquanto a CLI do Azure está disponível no Linux, não vêm pré-instaladas sobre os nós principais do cluster de HDInsight. Para obter mais informações sobre como instalar a CLI do Azure, consulte [introdução ao Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Componentes de .NET** |.NET é suportada no HDInsight baseado em Linux através do [Mono](https://mono-project.com). Para obter mais informações, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Componentes de Win32 ou outra tecnologia de só de Windows** |Documentação de orientação depende o componente ou tecnologia. É possível localizar uma versão compatível com o Linux. Caso contrário, tem de encontrar uma solução alternativa ou reescrever este componente. |

> [!IMPORTANT]
> A gestão de HDInsight não é totalmente compatível com Mono SDK. Não utilizá-lo como parte das soluções que são implementadas para o cluster do HDInsight.

## <a name="cluster-creation"></a>Criação do cluster

Esta secção fornece informações sobre as diferenças na criação do cluster.

### <a name="ssh-user"></a>SSH utilizador

Utilização de clusters do HDInsight baseado em Linux a **Secure Shell (SSH)** protocolo para fornecer acesso remoto a nós de cluster. Ao contrário dos clusters baseados no ambiente de trabalho remoto para Windows, a maioria dos clientes SSH não fornecem uma experiência de utilizador gráfica. Em vez disso, os clientes SSH fornecem uma linha de comandos que lhe permite executar comandos no cluster. Alguns clientes (tais como [MobaXterm](http://mobaxterm.mobatek.net/)) fornecem um browser de sistema de ficheiros gráfica para além de uma linha de comando remoto.

Durante a criação do cluster, tem de fornecer um utilizador SSH e um um **palavra-passe** ou **certificado de chave pública** para autenticação.

Recomendamos a utilização de certificado de chave pública, como é mais seguro que utilizar uma palavra-passe. Autenticação de certificado funciona ao gerar um par de chaves públicas/privadas assinado, em seguida, fornecer a chave pública, quando criar o cluster. Ao ligar ao servidor utilizando SSH, a chave privada no cliente fornece autenticação para a ligação.

Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Personalização de cluster

**Ações de script** utilizada com clusters baseados em Linux têm de ser escritos em scripts de Bash. Clusters baseados em Linux podem utilizar as ações de script durante ou após a criação do cluster. Para obter mais informações, consulte [HDInsight baseado em Linux personalizar com ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Script desenvolvimento de ações para o HDInsight baseado em Linux](hdinsight-hadoop-script-actions-linux.md).

Outra funcionalidade de personalização é **arranque**. Para os clusters do Windows, esta funcionalidade permite-lhe especificar a localização das bibliotecas adicionais para utilização com o Hive. Após a criação do cluster, estas bibliotecas estão automaticamente disponíveis para utilização com consultas do Hive, sem a necessidade de utilizar `ADD JAR`.

A funcionalidade de arranque de configuração para clusters baseados em Linux não disponibilizar esta funcionalidade. Em alternativa, utilize a ação de script documentada [bibliotecas adicionar ramo de registo durante a criação do cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Redes Virtuais

Baseado em Windows clusters do HDInsight só funciona com redes virtuais clássicas, enquanto que os clusters do HDInsight baseado em Linux requerem redes virtuais de Gestor de recursos. Se tiver recursos numa rede Virtual clássica que tem de ligar ao cluster do HDInsight do Linux, consulte [ligar uma rede Virtual clássica a uma rede Virtual do Gestor de recursos](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Para obter mais informações sobre requisitos de configuração, consulte o [capacidades de expandir HDInsight utilizando uma rede Virtual](hdinsight-extend-hadoop-virtual-network.md) documento.

## <a name="management-and-monitoring"></a>Gestão e monitorização

Muitas das web UIs pode ter sido utilizada com o HDInsight baseado em Windows, tais como o histórico de tarefas ou IU do Yarn, estão disponíveis através do Ambari. Além disso, a vista do Ambari Hive fornece uma forma para executar consultas do Hive com o seu browser. IU da Web do Ambari está disponível em clusters baseados em Linux no https://CLUSTERNAME.azurehdinsight.net.

Para obter mais informações sobre como trabalhar com o Ambari, consulte os seguintes documentos:

* [Web do Ambari](hdinsight-hadoop-manage-ambari.md)
* [API de REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertas do Ambari

Ambari tem um sistema de alerta que pode indicar-lhe potenciais problemas com o cluster. Alertas são apresentados como entradas de limiar de amarela ou vermelhos na IU da Web do Ambari, no entanto, também pode obtê-los através da API REST.

> [!IMPORTANT]
> Alertas do Ambari indicam que existe *poderá* ser um problema, não que existem *é* um problema. Por exemplo, poderá receber um alerta que não pode ser acedido HiveServer2, apesar de poder aceder a normalmente.
>
> Muitos alertas são implementadas como baseada no intervalo de consultas em relação a um serviço e esperam uma resposta dentro de um intervalo de tempo específico. Para que o alerta não significa necessariamente que o serviço está inativo, mas não devolvem resultados. o intervalo de tempo esperado.

## <a name="file-system-locations"></a>Localizações do sistema de ficheiros

O sistema de ficheiros de cluster do Linux é disposto diferente clusters do HDInsight baseados em Windows. Utilize a seguinte tabela para localizar utilizada frequentemente ficheiros.

| Tem de encontrar... | Está localizado... |
| --- | --- |
| Configuração |`/etc`. Por exemplo, `/etc/hadoop/conf/core-site.xml` |
| Ficheiros de registo |`/var/logs` |
| Plataforma de dados do Hortonworks (HDP) |`/usr/hdp`. Existem dois diretórios localizado aqui, que é a versão atual do HDP e `current`. O `current` diretório contém ligações simbólicas para ficheiros e diretórios localizados no diretório do número de versão. O `current` diretório é fornecida como uma forma conveniente de aceder a ficheiros HDP desde a alteração do número de versão como a HDP versão é atualizada. |
| hadoop streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Em geral, se souber o nome do ficheiro, pode utilizar o seguinte comando numa sessão SSH para localizar o caminho do ficheiro:

    find / -name FILENAME 2>/dev/null

Também pode utilizar carateres universais com o nome de ficheiro. Por exemplo, `find / -name *streaming*.jar 2>/dev/null` devolve o caminho para ficheiros jar que contém a palavra "de transmissão em fluxo' como parte do nome do ficheiro.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig e MapReduce

Cargas de trabalho PIg e MapReduce são semelhantes em clusters baseados em Linux. No entanto, os clusters do HDInsight baseado em Linux podem ser criados utilizando as versões mais recentes do Hadoop, Hive e Pig. Estas diferenças de versão podem introduzir alterações como a função de soluções existentes. Para obter mais informações sobre as versões de componentes incluídos com o HDInsight, consulte [controlo de versões do HDInsight componente](hdinsight-component-versioning.md).

HDInsight baseado em Linux fornece funcionalidade de ambiente de trabalho remota. Em vez disso, pode utilizar o SSH para ligar remotamente a nós principais do cluster. Para obter mais informações, consulte os seguintes documentos:

* [Utilizar o Hive com o SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Utilizar o Pig com o SSH](hdinsight-hadoop-use-pig-ssh.md)
* [Utilizar o MapReduce com SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Se utilizar um metastore do Hive externo, deve copiar em segurança o metastore antes de o utilizar com o HDInsight baseado em Linux. HDInsight baseado em Linux está disponível com versões mais recentes do Hive, pode ter incompatibilidades com metastores criadas por versões anteriores.

O gráfico seguinte fornece orientações para migrar as cargas de trabalho do ramo de registo.

| No baseado no Windows, posso utilizar... | No baseado em Linux... |
| --- | --- |
| **Editor do Hive** |[Vista Ambari Hive](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`Para ativar o Tez |Tez é o motor de execução predefinidas para os clusters baseados em Linux, pelo que a declaração set já não é necessária. |
| Funções definidas pelo utilizador c# | Para obter informações sobre a validação de componentes do c# com o HDInsight baseado em Linux, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Ficheiros CMD ou scripts no servidor invocado como parte de uma tarefa do Hive |Utilize scripts de Bash |
| `hive`comando a partir do ambiente de trabalho remoto |Utilize [Beeline](hdinsight-hadoop-use-hive-beeline.md) ou [do Hive a partir de uma sessão SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| No baseado no Windows, posso utilizar... | No baseado em Linux... |
| --- | --- |
| Funções definidas pelo utilizador c# | Para obter informações sobre a validação de componentes do c# com o HDInsight baseado em Linux, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Ficheiros CMD ou scripts no servidor invocado como parte de uma tarefa Pig |Utilize scripts de Bash |

### <a name="mapreduce"></a>MapReduce

| No baseado no Windows, posso utilizar... | No baseado em Linux... |
| --- | --- |
| C# mapeador de pontos e reducer componentes | Para obter informações sobre a validação de componentes do c# com o HDInsight baseado em Linux, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Ficheiros CMD ou scripts no servidor invocado como parte de uma tarefa do Hive |Utilize scripts de Bash |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Se utilizar um metastore do Oozie externo, deve copiar em segurança o metastore antes de o utilizar com o HDInsight baseado em Linux. HDInsight baseado em Linux está disponível nas versões mais recentes do Oozie, que pode ter incompatibilidades com metastores criadas por versões anteriores.

Fluxos de trabalho do Oozie permitem ações de shell. Ações de shell utilizam a shell predefinida para o sistema operativo para executar comandos da linha de comandos. Se tiver Oozie fluxos de trabalho que se baseiam na shell do Windows, terá de reescrever os fluxos de trabalho para confiar no ambiente de shell do Linux (Bash). Para obter mais informações sobre a utilização de ações de shell com Oozie, consulte [extensão de ação da shell de Oozie](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Se tiver um fluxo de trabalho que utiliza uma aplicação c#, valide estas aplicações num ambiente de Linux. Para obter mais informações, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| No baseado no Windows, posso utilizar... | No baseado em Linux... |
| --- | --- |
| Storm Dashboard |O Storm Dashboard não está disponível. Consulte [topologias de implementar e gerir Storm no HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md) para formas submeter topologias |
| IU do Storm |A IU do Storm está disponível em https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio para criar, implementar e gerir topologias de c# ou híbrida |Visual Studio pode ser utilizado para criar, implementar e gerir c# (SCP.NET) ou topologias híbridas no Storm baseado em Linux no HDInsight. Só pode ser utilizado com clusters criados após 10/28/2016. |

## <a name="hbase"></a>HBase

Em clusters baseados em Linux, é o principal de znode para o HBase `/hbase-unsecure`. Defina este valor na configuração de qualquer cliente de Java as aplicações que utilizam a API de Java nativo do HBase.

Consulte [compilar uma aplicação baseada em Java HBase](hdinsight-hbase-build-java-maven.md) de um cliente de exemplo que define este valor.

## <a name="spark"></a>Spark

Clusters do Spark estavam disponíveis nos clusters do Windows durante a pré-visualização. O Spark GA só está disponível com clusters baseados em Linux. Não há nenhum caminho de migração de um cluster de pré-visualização do Spark baseado no Windows para um cluster do Spark baseado em Linux de versão.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="azure-data-factory-custom-net-activities"></a>Atividades do .NET de personalizadas do Azure Data Factory

Azure Data Factory .NET atividades não são atualmente suportadas nos clusters do HDInsight baseado em Linux. Em vez disso, deve utilizar um dos seguintes métodos para implementar as atividades personalizadas como parte do seu pipeline ADF.

* Execute atividades de .NET num conjunto do Azure Batch. Consulte a utilização do Azure Batch ligado secção do serviço de [utilizar atividades personalizadas num pipeline do Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implemente a atividade como uma atividade de MapReduce. Para obter mais informações, consulte [invocar programas de MapReduce do Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Endings de linha

Em geral, o endings de linha em sistemas baseados em Windows utilizam CRLF, enquanto LF de utilizar sistemas baseados em Linux. Poderá ter de modificar os produtores de dados existente e os consumidores para trabalhar com LF.

Por exemplo, o HDInsight num cluster baseado no Windows com o Azure PowerShell a consulta devolve dados com CRLF. Da mesma consulta com um cluster baseado em Linux devolve LF. Teste para ver se o fim da linha faz com que um problema com a sua solução antes de migrar para um cluster baseado em Linux.

Utilize sempre LF como linha final para scripts que são executados em nós de cluster. Se utilizar CRLF, poderá ver erros ao executar os scripts de um cluster baseado em Linux.

Se os scripts não contêm cadeias de carateres de CR incorporados, pode em massa alteração endings a linha utilizando um dos seguintes métodos:

* **Antes de carregar para o cluster**: Utilize as seguintes declarações de PowerShell para alterar os endings linha CRLF para LF antes de carregar o script para o cluster.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Depois de carregar para o cluster**: Utilize o seguinte comando numa sessão SSH para o cluster baseado em Linux para modificar o script.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como criar clusters do HDInsight baseado em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Utilizar o SSH para ligar para o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerir um cluster baseado em Linux com o Ambari](hdinsight-hadoop-manage-ambari.md)
