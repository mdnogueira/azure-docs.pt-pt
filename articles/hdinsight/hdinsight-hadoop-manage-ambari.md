---
title: Monitorizar e gerir o Azure HDInsight utilizando a IU da Web do Ambari | Microsoft Docs
description: "Saiba como utilizar Ambari para monitorizar e gerir clusters do HDInsight baseado em Linux. Neste documento, irá aprender a utilizar a IU da Web do Ambari incluído com clusters do HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: larryfr
ms.openlocfilehash: ec6e6d07b0933504ffee17912aac9ee3ef937688
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Gerir clusters do HDInsight utilizando a IU da Web do Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica a gestão e monitorização de um cluster de Hadoop, fornecendo uma fácil de utilizar a IU da web e REST API. Ambari está incluído nos clusters do HDInsight baseado em Linux e é utilizado para monitorizar o cluster e efetuar alterações de configuração.

Neste documento, irá aprender a utilizar a IU da Web do Ambari com um cluster do HDInsight.

## <a id="whatis"></a>O que é o Ambari?

[Apache Ambari](http://ambari.apache.org) simplifica a gestão do Hadoop, fornecendo uma IU da web de fácil utilização. Pode utilizar Ambari criar, gerir e monitorizar clusters do Hadoop. Os programadores podem integrar estas capacidades nas suas aplicações utilizando o [APIs REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

IU da Web do Ambari é fornecido por predefinição com clusters do HDInsight que utilizam o sistema operativo Linux.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). 

## <a name="connectivity"></a>Conectividade

IU da Web do Ambari está disponível no seu cluster do HDInsight em HTTPS://CLUSTERNAME.azurehdidnsight.net, onde **CLUSTERNAME** é o nome do cluster.

> [!IMPORTANT]
> A ligação a Ambari no HDInsight requer HTTPS. Quando lhe for pedido para a autenticação, utilize o nome da conta de administrador e a palavra-passe que forneceu quando o cluster foi criado.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

Enquanto Ambari para o cluster está acessível diretamente através da Internet, algumas ligações a partir do IU da Web do Ambari (como juntar JobTracker) não são expostas na internet. Para aceder a estes serviços, tem de criar um túnel SSH. Para obter mais informações, consulte [utilize túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>IU da Web do Ambari

> [!WARNING]
> Nem todas as funcionalidades da IU da Web do Ambari são suportadas no HDInsight. Para obter mais informações, consulte o [não suportado operações](#unsupported-operations) secção deste documento.

Ao ligar a IU da Web do Ambari, lhe for pedido para autenticar para a página. Utilize o utilizador de administrador de cluster (predefinição Admin) e a palavra-passe que utilizou durante a criação do cluster.

Quando abre a página, tenha em atenção a barra na parte superior. Esta barra contém as seguintes informações e controlos de:

![ambari nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logótipo do Ambari** -abre o dashboard, o que pode ser utilizado para monitorizar o cluster.

* **Ops n. º de nome de cluster** -apresenta o número de operações em curso do Ambari. Selecionar o nome de cluster ou **# ops** apresenta uma lista de operações em segundo plano.

* **alertas de #** -mostra os avisos ou alertas críticos, se existir, para o cluster.

* **Dashboard** -apresenta o dashboard.

* **Serviços** -informações e definições de configuração para os serviços em cluster.

* **Anfitriões** -informações e definições de configuração para os nós do cluster.

* **Alertas** -um registo de informações, avisos e alertas críticos.

* **Administração** -Software pilha/serviços que estão instalados no cluster, informações de conta de serviço e segurança de Kerberos.

* **Botão de Admin** -Ambari gestão, as definições de utilizador e de fim de sessão.

## <a name="monitoring"></a>Monitorização

### <a name="alerts"></a>Alertas

A lista seguinte contém os Estados de alerta comuns utilizados pelo Ambari:

* **OK**
* **Aviso**
* **CRÍTICO**
* **DESCONHECIDO**

Alertas que **OK** fazer com que o **alertas #** entrada na parte superior da página para apresentar o número de alertas. Selecionar esta entrada apresenta os alertas e o respetivo estado.

Alertas são organizados em vários grupos predefinidos, que podem ser visualizados a **alertas** página.

![Página de alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Pode gerir os grupos, utilizando o **ações** menu e selecionando **gerir grupos alerta**.

![Gerir a caixa de diálogo de alerta de grupos](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Também pode gerir os métodos de alertas e criar notificações de alerta da **ações** menu selecionando __gerir notificações de alerta__. Quaisquer notificações atuais são apresentadas. Também pode criar notificações a partir daqui. As notificações podem ser enviadas através de **E-Mail** ou **SNMP** quando ocorrem combinações de alerta/gravidade específico. Por exemplo, pode enviar uma mensagem de e-mail quando qualquer um dos alertas no **YARN predefinido** grupo está definido como **crítico**.

![Criar caixa de diálogo de alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, selecionar __gerir definições de alerta__ do __ações__ menu permite-lhe definir o número de vezes que um alerta deve ocorrer antes do envio de uma notificação. Esta definição pode ser utilizada para impedir que as notificações de erros transitórios.

### <a name="cluster"></a>Cluster

O **métricas** separador do dashboard contém uma série de widgets que torna mais fácil monitorizar o estado do cluster de forma rápida. Vários widgets, tais como **utilização da CPU**, fornecem informações adicionais quando clicado.

![Dashboard de métricas](./media/hdinsight-hadoop-manage-ambari/metrics.png)

O **Heatmaps** separador apresenta métricas como heatmaps coloridos, que vai da verde para vermelho.

![dashboard com heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obter mais informações sobre os nós do cluster, selecione **anfitriões**. Em seguida, selecione o nó específico que está interessado.

![Detalhes do anfitrião](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Serviços

O **serviços** barra lateral no dashboard fornece rápida aprofundadas sobre o estado dos serviços em execução no cluster. Ícones de vários são utilizados para indicar o estado ou ações que deverão ser executadas. Por exemplo, um símbolo de reciclagem amarelo é apresentado se um serviço tem de ser reciclado.

![barra lateral de serviços](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Os serviços apresentados diferem entre as versões e tipos de cluster do HDInsight. Os serviços apresentados aqui poderão ser diferentes de serviços apresentados para o cluster.

A seleção de um serviço apresenta informações mais detalhadas sobre o serviço.

![informações de resumo de serviço](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Hiperligações rápidas

Alguns serviços apresentar um **ligações rápidas** ligação na parte superior da página. Isto pode ser utilizado para aceder à específicos do serviço web UIs, tais como:

* **Histórico da tarefa** -histórico de tarefas do MapReduce.
* **Gestor de recursos** -YARN ResourceManager IU.
* **NameNode** -ficheiro (HDFS sistema) NameNode IU de distribuído Hadoop.
* **IU da Web do Oozie** -Oozie IU.

Selecionar qualquer uma destas ligações é aberto um novo separador no seu browser, que apresenta a página selecionada.

> [!NOTE]
> Selecionar o **ligações rápidas** entrada para um serviço pode devolver um erro "o servidor não encontrado". Se ocorrer este erro, tem de utilizar um túnel SSH ao utilizar o **ligações rápidas** entrada para este serviço. Para informações, consulte [utilize túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gestão

### <a name="ambari-users-groups-and-permissions"></a>Ambari utilizadores, grupos e permissões

Trabalhar com os utilizadores, grupos e as permissões são suportados quando utilizar um [associado a um domínio](./domain-joined/apache-domain-joined-introduction.md) cluster do HDInsight. Para obter informações sobre como utilizar a IU de gestão do Ambari num cluster associados a um domínio, consulte [gerir clusters do HDInsight associados a um domínio](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]
> Não altere a palavra-passe watchdog do Ambari (hdinsightwatchdog) no cluster do HDInsight baseado em Linux. Alterar a palavra-passe interrompe a capacidade de utilizar as ações de script ou efetuar operações de dimensionamento com o cluster.

### <a name="hosts"></a>Anfitriões

O **anfitriões** página apresenta uma lista de todos os anfitriões do cluster. Para gerir anfitriões, siga estes passos.

![página de anfitriões](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> Adicionar, desativar e recommissioning um anfitrião não devem ser utilizadas com clusters do HDInsight.

1. Selecione o anfitrião que pretende gerir.

2. Utilize o **ações** menu para selecionar a ação que pretende efetuar:

   * **Iniciar todos os componentes** -iniciar todos os componentes no anfitrião.

   * **Parar todos os componentes** -parar todos os componentes no anfitrião.

   * **Reinicie todos os componentes** - parar e iniciar a todos os componentes no anfitrião.

   * **Ativar o modo de manutenção** -suprime alertas para o anfitrião. Este modo deve ser ativado se estiver a efetuar ações que geram alertas. Por exemplo, parar e iniciar um serviço.

   * **Desativar o modo de manutenção** -devolve o anfitrião para alertar normal.

   * **Parar** -interrompe DataNode ou NodeManagers no anfitrião.

   * **Iniciar** -DataNode inicia ou NodeManagers no anfitrião.

   * **Reiniciar** -interrompe e inicia DataNode ou NodeManagers no anfitrião.

   * **Desativar** -remove um anfitrião do cluster.

     > [!NOTE]
     > Não utilize esta ação nos clusters do HDInsight.

   * **Recommission** -adiciona um anfitrião anteriormente desativado para o cluster.

     > [!NOTE]
     > Não utilize esta ação nos clusters do HDInsight.

### <a id="service"></a>Serviços

Do **Dashboard** ou **serviços** página, utilize o **ações** na parte inferior da lista de serviços para parar e iniciar a todos os serviços.

![ações de serviço](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Enquanto **Adicionar serviço** está listado deste menu, não deve ser utilizado para adicionar serviços ao cluster do HDInsight. Novos serviços devem ser adicionados com uma ação de Script durante o aprovisionamento de cluster. Para obter mais informações sobre como utilizar as ações de Script, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

Enquanto o **ações** botão pode reiniciar todos os serviços, muitas vezes, pretende iniciar, parar ou reiniciar um serviço específico. Utilize os seguintes passos para efetuar ações num serviço individuais:

1. Do **Dashboard** ou **serviços** página, selecione um serviço.

2. Na parte superior do **resumo** separador, utilize o **serviço ações** botão e selecione a ação a tomar. Esta ação reinicia o serviço em todos os nós.

    ![ação de serviço](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]
   > Reiniciar alguns serviços, enquanto o cluster está em execução, pode gerar alertas. Para evitar alertas, pode utilizar o **serviço ações** botão Ativar **modo de manutenção** para o serviço antes de efetuar o reinício.

3. Depois de uma ação tiver sido selecionada, o **# op** entrada no topo da incrementos de página para mostrar uma operação em segundo plano está a ocorrer. Se configurado para apresentar, é apresentada a lista de operações em segundo plano.

   > [!NOTE]
   > Se tiver ativado **modo de manutenção** para o serviço, lembre-se de o desativar utilizando o **serviço ações** botão depois da operação foi concluída.

Para configurar um serviço, utilize os seguintes passos:

1. Do **Dashboard** ou **serviços** página, selecione um serviço.

2. Selecione o **folhas** separador. A configuração atual é apresentada. Também é apresentada uma lista de configurações anteriores.

    ![Configurações](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Utilize os campos apresentados para modificar a configuração e, em seguida, selecione **guardar**. Ou selecione uma configuração anterior e, em seguida, selecione **tornar atual** para reverter para as definições anteriores.

## <a name="ambari-views"></a>Vistas Ambari

As vistas Ambari permitem aos programadores para elementos de IU para a IU da Web do Ambari, utilizando o [Framework de vistas do Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). O HDInsight fornece as vistas seguintes tipos de cluster do Hadoop:

* Gestor de filas yarn: O Gestor de filas fornece uma IU simple para ver e modificar filas YARN.

* Vista do Hive: A vista do Hive permite-lhe executar consultas do Hive diretamente a partir do seu browser. Pode guardar consultas, ver os resultados, guardar resultados para o armazenamento de cluster ou transferir os resultados para o sistema local. Para obter mais informações sobre como utilizar as vistas do Hive, consulte [vistas de utilizar o Hive com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Vista de Tez: O Tez vista permite para melhor compreender e otimizar tarefas. Pode ver informações sobre como Tez tarefas são executadas e os recursos que são utilizados.

## <a name="unsupported-operations"></a>Operações não suportadas

As seguintes operações do Ambari não são suportadas no HDInsight:

* __Mover o serviço do Recoletor de métricas__. Ao visualizar informações sobre o serviço do Recoletor de métricas, uma das ações disponíveis no menu de ações de serviço é __recoletor mover métricas__. Não é suportada com o HDInsight.

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar o [API de REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) com o HDInsight.