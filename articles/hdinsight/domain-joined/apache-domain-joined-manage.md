---
title: "Gerir clusters do HDInsight associados a um domínio - Azure | Microsoft Docs"
description: "Saiba como gerir clusters do HDInsight associados a um domínio"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 8dd5490fcd5c57077c65b76f3ce44068219d1222
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="manage-domain-joined-hdinsight-clusters-preview"></a>Gerir clusters do HDInsight associados a um domínio (pré-visualização)
Saiba mais utilizadores e funções no HDInsight associados a um domínio e como gerir clusters do HDInsight associados a um domínio.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>Utilizadores de clusters do HDInsight associados a um domínio
Um cluster do HDInsight que não está associado ao domínio, tem duas contas de utilizador que são criadas durante a criação do cluster:

* **Administrador do Ambari**: esta conta também é conhecido como *Hadoop utilizador* ou *utilizador HTTP*. Esta conta pode ser utilizada para iniciar sessão Ambari em https://&lt;clustername >. azurehdinsight.net. Também pode ser utilizado para executar consultas em vistas do Ambari, executar tarefas através de ferramentas externas (ou seja, PowerShell Templeton, Visual Studio) e autenticar com o controlador ODBC do Hive e ferramentas de BI (ou seja, Excel, PowerBI ou Tableau).
* **Utilizador do SSH**: esta conta pode ser utilizado com SSH e executar comandos de sudo. Tem privilégios de raiz para as VMs do Linux.

Um cluster do HDInsight associados a um domínio tem três novos utilizadores além de administração do Ambari e utilizador do SSH.

* **Administração de Ranger**: esta conta é a conta de administrador local do Apache Ranger. Não é um utilizador de domínio do Active Directory. Esta conta pode ser utilizada para configurar políticas e efetuar outros administradores de utilizadores ou administradores delegados (de modo a que os utilizadores podem gerir políticas). Por predefinição, o nome de utilizador é *admin* e a palavra-passe é o mesmo que a palavra-passe de administrador do Ambari. A palavra-passe pode ser atualizada da página de definições no Ranger.
* **Utilizador de domínio de administrador de cluster**: esta conta é um utilizador de domínio do Active Directory designado como o administrador de cluster de Hadoop, incluindo Ambari e Ranger. Tem de fornecer credenciais do utilizador durante a criação do cluster. Este utilizador tem os seguintes privilégios:

  * Associar computadores ao domínio e colocá-los na UO que especificar durante a criação do cluster.
  * Crie principais de serviço na UO que especificar durante a criação do cluster.
  * Criação de entradas DNS inversas.

    Tenha em atenção de que os outros utilizadores do AD têm também esses privilégios.

    Existem alguns pontos finais do cluster (por exemplo, Templeton) que não são geridos pelo Ranger e, por conseguinte, não são seguros. Estes pontos finais são bloqueados para todos os utilizadores, exceto o utilizador de domínio de administrador do cluster.
* **Regular**: durante a criação do cluster, pode fornecer vários grupos do Active Directory. Os utilizadores nestes grupos vão ser sincronizados Ranger e Ambari. Estes utilizadores são utilizadores do domínio e terão acesso a apenas geridos Ranger pontos finais (por exemplo, Hiveserver2). Todas as políticas RBAC e auditoria irá ser aplicável a estes utilizadores.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Funções de clusters do HDInsight associados a um domínio
HDInsight associados a um domínio têm as seguintes funções:

* Administrador de cluster
* Operador de cluster
* Administrador de serviço
* Operador de serviço
* Utilizador de cluster

**Para ver as permissões de uma destas funções**

1. Abra a IU de gestão do Ambari.  Consulte [abra a IU de gestão do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **funções**.
3. Clique na marca de pergunta azul para ver as permissões:

    ![Permissões de funções de HDInsight associados a um domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abra a IU de gestão do Ambari
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster do HDInsight num painel. Consulte [clusters lista e mostrar](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Clique em **Dashboard** no menu superior para abrir o Ambari.
4. Inicie sessão com o nome de utilizador de domínio de administrador de cluster e a palavra-passe do Ambari.
5. Clique em de **Admin** menu pendente do canto superior botão canto direito do rato e, em seguida, clique em **gerir Ambari**.

    ![HDInsight associados a um domínio gerir Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A IU do aspeto de:

    ![Associado a um domínio HDInsight Ambari IU de gestão](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Lista os utilizadores sincronizados a partir do seu Active Directory
1. Abra a IU de gestão do Ambari.  Consulte [abra a IU de gestão do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **utilizadores**. Deverá ver todos os utilizadores sincronizados a partir do Active Directory para o cluster do HDInsight.

    ![Associado a um domínio HDInsight Ambari IU lista utilizadores de gestão](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Lista os grupos de domínio sincronizados a partir do seu Active Directory
1. Abra a IU de gestão do Ambari.  Consulte [abra a IU de gestão do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **grupos**. Deverá ver todos os grupos sincronizados a partir do Active Directory para o cluster do HDInsight.

    ![Associado a um domínio HDInsight Ambari IU lista grupos de gestão](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurar permissões de vistas do Hive
1. Abra a IU de gestão do Ambari.  Consulte [abra a IU de gestão do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **vistas**.
3. Clique em **HIVE** para mostrar os detalhes.

    ![Associado a um domínio de gestão HDInsight Ambari vistas do Hive de IU](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Clique em de **vista do Hive** ligação para configurar as vistas do Hive.
5. Desloque para baixo até o **permissões** secção.

    ![Associado a um domínio de gestão HDInsight Ambari vistas do Hive de IU configurar permissões](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Clique em **adicionar utilizador** ou **adicionar grupo**e, em seguida, especifique os utilizadores ou grupos que podem utilizar vistas do Hive.

## <a name="configure-users-for-the-roles"></a>Configurar os utilizadores para as funções
 Para ver uma lista de funções e as respetivas permissões, consulte [clusters do HDInsight associados a funções de domínios](#roles-of-domain---joined-hdinsight-clusters).

1. Abra a IU de gestão do Ambari.  Consulte [abra a IU de gestão do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **funções**.
3. Clique em **adicionar utilizador** ou **adicionar grupo** atribuir utilizadores e grupos para diferentes funções.

## <a name="next-steps"></a>Passos seguintes
* Para configurar um cluster do HDInsight associado a um domínio, veja [Configurar clusters do HDInsight associados a um domínio](apache-domain-joined-configure.md).
* Para configurar políticas do Hive e executar consultas do Hive, veja [Configurar políticas do Hive para clusters do HDInsight associados a um domínio](apache-domain-joined-run-hive.md).
* Para executar consultas do Hive com o SSH nos clusters do HDInsight associados a um domínio, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
