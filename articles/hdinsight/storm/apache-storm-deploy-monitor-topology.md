---
title: Implementar e gerir topologias Apache Storm no HDInsight | Microsoft Docs
description: Saiba como implementar, monitorizar e gerir topologias Apache Storm, utilizar o Dashboard Storm no HDInsight. Utilize as ferramentas Hadoop para o Visual Studio.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 92c1a02cd7d435809914e7f5bb43b2f8d6aa0cdb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Implementar e gerir topologias Apache Storm no HDInsight baseado em Windows

O Storm Dashboard permite-lhe facilmente implementar e executar topologias do Apache Storm ao cluster do HDInsight ao utilizar o seu browser. Também pode utilizar o dashboard para monitorizar e gerir topologias em execução. Se utilizar o Visual Studio, as ferramentas do HDInsight para Visual Studio fornecem semelhantes funcionalidades no Visual Studio.

O Dashboard do Storm e as funcionalidades de Storm nas ferramentas do HDInsight dependem da API de REST de Storm, que pode ser utilizado para criar os seus próprios de monitorização e soluções de gestão.

> [!IMPORTANT]
> Os passos neste documento exigem um Storm no cluster do HDInsight que utiliza o Windows como o sistema operativo. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).
>
> Para obter informações sobre como implementar e gerir topologias do Storm com um cluster do HDInsight que utiliza o Linux, consulte [implementar e gerir topologias Apache Storm no HDInsight baseado em Linux](apache-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Pré-requisitos

* **Apache Storm no HDInsight** -consulte [introdução ao Apache Storm no HDInsight](apache-storm-tutorial-get-started-linux.md) para obter os passos sobre como criar um cluster.

* Para o **Storm Dashboard**: um browser moderno que suporte HTML5.

* Para **Visual Studio** -Azure SDK 2.5.1 ou mais recente e as ferramentas do HDInsight para Visual Studio. Consulte [começar a utilizar as ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) para instalar e configurar as ferramentas do HDInsight para Visual Studio.

    Uma das seguintes versões do Visual Studio:

  * Visual Studio 2012 com atualização 4

  * Visual Studio 2013 atualização 4 ou Visual Studio 2013 Community

  * Visual Studio 2015 (qualquer edição)

  * Visual Studio 2017 (qualquer edição)

## <a name="storm-dashboard"></a>Storm Dashboard

O Dashboard do Storm é uma página web disponíveis no seu cluster do Storm. O URL é **https://&lt;clustername >.azurehdinsight.net/**, onde **clustername** é o nome do seu cluster Storm no HDInsight.

Na parte superior do Storm Dashboard, selecione **submeter topologia**. Siga as instruções na página para executar uma topologia de exemplo ou para carregar e executar uma topologia que criou.

![página de topologia de envio][storm-dashboard-submit]

### <a name="storm-ui"></a>IU do Storm

A partir do Dashboard do Storm, selecione o **IU do Storm** ligação. Esta ação apresenta informações sobre o cluster, além de qualquer topologias em execução.

![a IU do storm][storm-dashboard-ui]

> [!NOTE]
> Com algumas versões do Internet Explorer, poderá descobrir que a IU do Storm não atualize após o primeiro visitaram-lo. Por exemplo, esta operação poderá não mostrar as topologias de novo submetidas ou esta operação poderá mostrar uma topologia como o Active Directory quando desativada anteriormente. A Microsoft está ciente de que este problema e está a funcionar numa solução.

#### <a name="main-page"></a>Página principal

Página principal da IU do Storm fornece as seguintes informações:

* **Resumo do cluster**: informações básicas sobre o cluster do Storm.

* **Resumo da topologia**: uma lista de execução de topologias. Utilize as hiperligações nesta secção para visualizar mais informações sobre topologias específicas.

* **Resumo do supervisor**: informações sobre o supervisor de Storm.

* **Configuração de nimbus**: configuração de Nimbus para o cluster.

#### <a name="topology-summary"></a>Topologia de resumo

Selecionar uma ligação do **resumo da topologia** secção apresenta as seguintes informações sobre a topologia:

* **Resumo da topologia**: informações básicas sobre a topologia.

* **Ações de topologia**: ações de gestão que pode efetuar para a topologia.

  * **Ativar**: retoma o processamento de uma topologia desativada.

  * **Desativar**: pausa uma topologia em execução.

  * **Rebalancear**: ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. Isto permite que a topologia ajuste o paralelismo para compensar o número maior ou menor de nós no cluster.

      Para obter mais informações, consulte [compreender o paralelismo de uma topologia do Storm (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Kill**: termina uma topologia do Storm após o tempo limite especificado.

* **Estatísticas de topologia**: estatísticas sobre a topologia. Utilize as ligações no **janela** coluna para definir o período de tempo para as entradas restantes na página.

* **Spouts**: spouts utilizados pela topologia. Utilize as hiperligações nesta secção para visualizar mais informações sobre spouts específicos.

* **Bolts**: bolts utilizados pela topologia. Utilize as hiperligações nesta secção para visualizar mais informações sobre bolts específicos.

* **Configuração da topologia**: A configuração da topologia selecionada.

#### <a name="spout-and-bolt-summary"></a>Spout e resumo Bolt

Selecionar um spout do **Spouts** ou **Bolts** secções apresenta as seguintes informações sobre o item seleccionado:

* **Resumo de componente**: informações básicas sobre o spout ou bolt.

* **Estatísticas de spout/Bolt**: estatísticas sobre a spout ou bolt. Utilize as ligações no **janela** coluna para definir o período de tempo para as entradas restantes na página.

* **Estatísticas de entrada** (apenas bolt): informações sobre os fluxos de entrada consumidos pelo bolt.

* **Estatísticas de saída**: informações sobre os fluxos emitidos por este spout ou bolt.

* **Executores**: informações sobre as instâncias do spout ou bolt. Selecione o **porta** produzidos de entrada para um executor específico ver um registo de informações de diagnóstico para esta instância.

* **Erros**: quaisquer informações de erro para este spout ou bolt.

## <a name="hdinsight-tools-for-visual-studio"></a>Ferramentas do HDInsight para Visual Studio

As ferramentas do HDInsight pode ser utilizadas para submeter topologias de c# ou híbrida ao cluster do Storm. Os seguintes passos utilizarem uma aplicação de exemplo. Para obter informações sobre como criar as seus próprios topologias utilizando as ferramentas do HDInsight, consulte [desenvolver topologias c# utilizando as ferramentas do HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Utilize os seguintes passos para implementar um exemplo para Storm no cluster do HDInsight, em seguida, ver e gerir a topologia.

1. Se já não instalou a versão mais recente das ferramentas HDInsight para Visual Studio, consulte [começar a utilizar as ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Abra o Visual Studio, selecione **ficheiro** > **novo** > **projeto**.

3. No **novo projeto** diálogo caixa, expanda **instalada** > **modelos**e, em seguida, selecione **HDInsight**. Na lista de modelos, selecione **exemplo do Storm**. Na parte inferior da caixa de diálogo, escreva um nome para a aplicação.

    ![Imagem](./media/apache-storm-deploy-monitor-topology/sample.png)

4. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **envio para Storm no HDInsight**.

   > [!NOTE]
   > Se lhe for solicitado, introduza as credenciais de início de sessão para a sua subscrição do Azure. Se tiver mais do que uma subscrição, inicie sessão no que contém o seu cluster Storm no HDInsight.

5. Selecione o seu cluster Storm no HDInsight do **Cluster do Storm** na lista pendente e, em seguida, selecione **submeter**. Pode monitorizar se a submissão é efetuada com êxito utilizando a **saída** janela.

6. Quando a topologia foi submetida com êxito, o **topologias Storm** para o cluster deve aparecer. Selecione a topologia da lista para ver informações sobre a topologia em execução.

    ![monitor do Visual studio](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Também pode ver **topologias Storm** de **Explorador de servidores** expandindo **Azure** > **HDInsight**e, em seguida, clicar um cluster Storm no HDInsight e selecionando **topologias do Storm vista**.

    Selecione a forma spouts ou bolts para ver informações sobre estes componentes. Abre uma nova janela para cada item selecionado.

   > [!NOTE]
   > O nome da topologia é o nome de classe da topologia (neste caso, `HelloWord`,) com um carimbo anexado.

7. Do **resumo da topologia** visualizar, selecione **Kill** para parar a topologia.

   > [!NOTE]
   > Topologias do Storm continuam em execução até que estão paradas ou o cluster é eliminado.


## <a name="rest-api"></a>API REST

A IU do Storm é desenvolvida com a API REST, pelo que pode efetuar a gestão e semelhantes funcionalidades de monitorização, utilizando a API REST. Pode utilizar a API REST para criar personalizadas ferramentas de gestão e monitorização topologias do Storm.

Para obter mais informações, consulte [API de REST de IU do Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). As seguintes informações são específicas para utilizar a API REST com Apache Storm no HDInsight.

### <a name="base-uri"></a>URI de base

O URI base para a API de REST nos clusters do HDInsight é **https://&lt;clustername >.azurehdinsight.net/stormui/api/v1/**, onde **clustername** é o nome do Storm no HDInsight cluster.

### <a name="authentication"></a>Autenticação

Pedidos para a API REST têm de utilizar **autenticação básica**, por isso, utilize o nome de administrador de cluster do HDInsight e a palavra-passe.

> [!NOTE]
> Porque a autenticação básica é enviada através da utilização de texto não encriptado, deve **sempre** utilizar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações que são devolvidas a partir da API de REST podem apenas ser utilizáveis do dentro do cluster ou máquinas virtuais no mesmo Azure Virtual Network do cluster. Por exemplo, o nome domínio completamente qualificado (FQDN) devolvido para servidores de Zookeeper não são estar acessível a partir da Internet.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como implementar e monitorizar as topologias através do Dashboard do Storm, saiba como:

* [Desenvolver topologias c# utilizando as ferramentas do HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

* [Desenvolver topologias baseadas em Java com o Maven](apache-storm-develop-java-topology.md)

Para obter uma lista das topologias de exemplo mais, consulte [topologias de exemplo para Storm no HDInsight](apache-storm-example-topology.md).

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
