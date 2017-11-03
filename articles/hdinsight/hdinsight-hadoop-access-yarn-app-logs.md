---
title: "Aplicações do Hadoop YARN de acesso através de programação - registos do Azure | Microsoft Docs"
description: "Aplicação de acesso através de programação regista num cluster de Hadoop no HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 90323af4a1f4526ab9b26811c8679337076112d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Os registos de aplicação de YARN de acesso no HDInsight baseado em Windows
Este tópico explica como aceder a registos de aplicações de YARN (ainda outro recurso Negotiator) que tem concluído a um cluster do Hadoop baseados em Windows no Azure HDInsight

> [!IMPORTANT]
> As informações neste documento aplicam-se apenas a clusters do HDInsight baseados em Windows. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). Para obter informações sobre aceder YARN registos nos clusters do HDInsight baseado em Linux, consulte [aplicação acesso YARN os registos de Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Pré-requisitos
* Um cluster do HDInsight baseados em Windows.  Consulte [clusters do Hadoop baseados em Windows criar no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>Servidor de linha cronológica YARN
O <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN linha cronológica servidor</a> fornece informações genéricas em aplicações concluídas, bem como framework informações específicas de aplicações através de duas interfaces de rede diferentes. Especificamente:

* Armazenamento e a obtenção de informações de aplicação genérica nos clusters do HDInsight foi ativado com a versão 3.1.1.374 ou superior.
* O componente de informações da aplicação de arquitetura específica do servidor a linha cronológica não está atualmente disponível nos clusters do HDInsight.

Informações genéricas em aplicações incluem os seguintes tipos de dados:

* O ID da aplicação, um identificador exclusivo de uma aplicação
* O utilizador que iniciou a aplicação
* Informações sobre as tentativas para concluir a aplicação
* Os contentores utilizados por qualquer tentativa de aplicação

Nos seus clusters do HDInsight, estas informações serão armazenadas pelo Gestor de recursos do Azure para um arquivo de histórico no contentor predefinido da sua conta do Storage do Azure de predefinição. Estes dados genéricos em aplicações concluídas podem ser obtidos através de uma API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Registos de aplicações de YARN e
YARN suporta vários modelos de programação (MapReduce que está a ser um dos atributos) ao desassociar a gestão de recursos de agendamento/monitorização de aplicações. Isto é feito um global *ResourceManager* (RM), por--nó de trabalho *NodeManagers* (NMs) e por aplicação *ApplicationMasters* (AMs). O AM por aplicação negoceia recursos (CPU, memória, disco, rede) para executar a aplicação com o RM. O RM funciona com NMs para conceder estes recursos, que são concedidos como *contentores*. O AM é responsável por controlar o progresso dos contentores atribuída pelo RM. Uma aplicação pode necessitar de vários contentores dependendo da natureza da aplicação.

Além disso, cada aplicação pode consistir em várias *tentativas da aplicação* para poder concluir na presença de falhas ou devido a perda de comunicação entre um AM e um RM. Por conseguinte, os contentores são concedidos para uma tentativa de uma aplicação específica. Um sentido, um contentor fornece o contexto para a unidade básica de trabalho realizado por uma aplicação YARN e todo o trabalho que é efetuado no contexto de um contentor é executado no nó de trabalho único no qual foi atribuído o contentor. Consulte [YARN conceitos] [ YARN-concepts] para obter mais referência.

Os registos de aplicações (e os registos do contentor associado) são críticos depurar aplicações do Hadoop problemáticas. O YARN fornece uma arquitetura nice para recolher, Agregar e armazenar os registos de aplicação com o [registo agregação] [ log-aggregation] funcionalidade. A funcionalidade de agregação de registo permite ao aceder aos registos de aplicações mais determinista, como agrega registos através de todos os contentores num nó de trabalho e armazena-os como um agregado ficheiro de registo por nó de trabalho no sistema de ficheiros predefinido após a conclusão de uma aplicação. A aplicação pode utilizar centenas ou milhares de contentores, mas os registos para todos os contentores executados num nó único do worker sempre serão agregados para um ficheiro único, resultando num ficheiro de registo por nó de trabalho utilizada pela sua aplicação. Agregação de registo está ativada por predefinição nos clusters do HDInsight (versão 3.0 e superior), e os registos de agregados podem ser encontrados no contentor predefinido do cluster, na seguinte localização:

    wasb:///app-logs/<user>/logs/<applicationId>

Nessa localização *utilizador* é o nome do utilizador que iniciou a aplicação, e *applicationId* é o identificador exclusivo de uma aplicação conforme atribuído pelo RM YARN.

Os registos de agregados não são diretamente legíveis, que são escritos [TFile][T-file], [formato binário] [ binary-format] indexado por contentor. O YARN fornece ferramentas da CLI para informação do Estado estes registos como texto não encriptado para aplicações ou contentores de interesse. Pode ver estes registos como texto simples, executando um dos YARN seguinte comandos diretamente em nós de cluster (depois de se ligar ao mesmo através de RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>IU do YARN ResourceManager
A IU do YARN ResourceManager é executado no headnode cluster e podem ser acedida através do dashboard do portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu da esquerda, clique em **procurar**, clique em **Clusters do HDInsight**, clique num cluster baseado em Windows que pretende aceder os registos YARN da aplicação.
3. No menu superior, clique em **Dashboard**. Verá uma página de abrir um browser novo separador chamado **HDInsight consulta consola**.
4. De **HDInsight consulta consola**, clique em **IU do Yarn**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
