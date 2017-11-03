---
title: Autorizar utilizadores para vistas do Ambari - Azure HDInsight | Microsoft Docs
description: "Como gerir as permissões de utilizador e grupos Ambari associados a um domínio para clusters do HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: ad9aa6aee0a9f6407da6e9f45df71f8feb8b1500
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authorize-users-for-ambari-views"></a>Autorizar utilizadores para vistas do Ambari

[Associado a um domínio clusters do HDInsight](hdinsight-domain-joined-introduction.md) fornecem capacidades de nível empresarial, incluindo a autenticação baseada no Azure Active Directory. Pode sincronizar os novos utilizadores
<!-- [synchronize new users](hdinsight-sync-aad-users-to-cluster.md) --> added to Azure AD groups that have been provided access to the cluster, allowing those specific users to perform certain actions. Currently, working with users, groups, and permissions in Ambari is only supported when using a domain-joined HDInsight cluster.

Utilizadores do Active Directory podem iniciar sessão para os nós de cluster utilizando as credenciais de domínio. Também podem utilizar as respetivas credenciais de domínio para autenticar as interações de cluster com outros pontos finais aprovados como Hue, as vistas Ambari, ODBC, JDBC, PowerShell e REST APIs.

> [!WARNING]
> Não altere a palavra-passe watchdog do Ambari (hdinsightwatchdog) no cluster do HDInsight baseado em Linux. Alterar a palavra-passe interrompe a capacidade de utilizar as ações de script ou efetuar operações de dimensionamento com o cluster.

Se ainda não o fez, siga [estas instruções](hdinsight-domain-joined-configure.md) para aprovisionar um novo cluster associados a um domínio.

## <a name="access-the-ambari-management-page"></a>Aceder à página de gestão do Ambari

Para obter o **página de gestão do Ambari** no [IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md), navegue até à  **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Introduza o nome de utilizador de administrador de cluster e a palavra-passe que definiu quando criar o cluster. Em seguida, a partir do dashboard do Ambari, selecione **gerir Ambari** por baixo do **admin** menu:

![Gerir Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-hive-views"></a>Conceder permissões para vistas do Hive

Ambari vem com instâncias de vista para o ramo de registo e Tez, entre outros. Para conceder acesso a uma ou mais instâncias de vista do Hive, vá para o **página de gestão do Ambari**.

1. Na página Gestão, selecione o **vistas** ligação sob o **vistas** título do menu à esquerda.

    ![Associar vistas](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Na página de vistas, expanda o **HIVE** linha. Há uma vista de ramo de registo predefinido que é criada quando o serviço de ramo de registo é adicionado ao cluster. Também pode criar mais instâncias de vista do Hive conforme necessário. Selecione uma vista de ramo de registo:

    ![Vistas - vista do Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Desloque-se para a parte inferior da página de vista. Sob o *permissões* secção, tem duas opções para a concessão as respetivas permissões para a vista de utilizadores de domínio:

**Conceder permissão para estes utilizadores** ![conceder permissão aos utilizadores](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Conceder permissão a estes grupos** ![conceder permissão a estes grupos](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

4. Para adicionar um utilizador, selecione o **adicionar utilizador** botão.

    * Início escrevendo o nome de utilizador e o utilizador irá ver uma lista de lista pendente de nomes definidas anteriormente.

    ![Utilizador autocompletes](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Selecione ou concluir escrevendo o nome de utilizador. Para adicionar este nome de utilizador como um novo utilizador, selecione o **novo** botão.

    * Para guardar as alterações, selecione o **caixa de verificação azul**.

    ![Utilizador introduzida](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Para adicionar um grupo, selecione o **adicionar grupo** botão.

    * Comece a escrever o nome do grupo. O processo de selecionar um nome de grupo existente ou adicionar um novo grupo, é igual a adição de utilizadores.
    * Para guardar as alterações, selecione o **caixa de verificação azul**.

    ![Grupo introduzido](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Adicionar utilizadores diretamente para uma vista é útil quando pretende atribuir permissões a um utilizador utilizar nessa vista, mas não pretende que sejam membros de um grupo que tem permissões adicionais. Para reduzir a quantidade de sobrecarga administrativa, poderá ser mais simples atribuir permissões a grupos.

## <a name="grant-permissions-to-tez-views"></a>Conceder permissões para vistas Tez

As instâncias de vista Tez permitir que os utilizadores a monitorizar e todas as tarefas de Tez, submetidas pelo consultas do Hive e Pig scripts de depuração. Não há um Tez vista instância predefinida que é criada quando o cluster seja aprovisionado.

Para atribuir utilizadores e grupos para uma instância de vista Tez, expanda o **TEZ** linha na página de vistas, conforme descrito anteriormente.

![Vistas - Tez vista](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Para adicionar utilizadores ou grupos, repita os passos 3 a 5, na secção anterior.

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções

Existem cinco funções de segurança para utilizadores e grupos, listados por ordem de diminuir as permissões de acesso:

* Administrador de cluster
* Operador de cluster
* Administrador de serviço
* Operador de serviço
* Utilizador de cluster

Para gerir funções, vá para o **página de gestão do Ambari**, em seguida, selecione o **funções** ligação dentro o *Clusters* grupo menu à esquerda.

![Ligação de menu de funções](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Para ver a lista de permissões para cada função, clique em do ponto de interrogação azul junto a **funções** cabeçalho de tabela na página de funções.

![Ligação de menu de funções](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Nesta página, existem duas vistas diferentes, pode utilizar para gerir funções de utilizadores e grupos: blocos e de lista.

### <a name="block-view"></a>Vista de bloco

A vista de bloco apresenta cada função na sua própria linha e fornece o **atribuir funções para estes utilizadores** e **atribuir funções a estes grupos** opções conforme descrito anteriormente.

![Funções de bloquear a vista](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Vista de lista

A vista de lista fornece capacidades de edição rápidas em duas categorias: utilizadores e grupos.

* A categoria de utilizadores de vista de lista mostra uma lista de todos os utilizadores, permitindo-lhe selecionar uma função para cada utilizador na lista pendente.

    ![Vista - os utilizadores de lista de funções](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* A categoria de grupos de vista de lista apresenta todos os grupos e a função atribuído a cada grupo. No nosso exemplo, a lista de grupos é sincronizada a partir de grupos do Azure AD especificados no **grupo de utilizadores de acesso** propriedade das definições de domínio do cluster. Consulte [cluster do HDInsight criar](hdinsight-domain-joined-configure.md#create-hdinsight-cluster).

    ![Vista - grupos de lista de funções](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na imagem acima, o grupo "hiveusers" está atribuído a *Cluster utilizador* função. Esta é uma função só de leitura que permite que os utilizadores desse grupo para ver, mas não alterar as configurações de serviço e as métricas de cluster.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Inicie sessão no Ambari como um utilizador apenas de visualização

Iremos atribuiu nosso utilizador de domínio do Azure AD "hiveuser1" permissões para vistas do Hive e Tez. Quando vamos iniciar a IU de Web do Ambari e introduza as credenciais de domínio do utilizador (nome de utilizador do Azure AD no formato de mensagem de correio eletrónico e palavra-passe), o utilizador é redirecionado para a página de vistas do Ambari. Aqui, o utilizador pode selecionar qualquer vista acessível. O utilizador não é possível aceda a qualquer outra parte do site, incluindo as páginas do dashboard, serviços, anfitriões, alertas ou administrador.

![Utilizador com apenas vistas](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Inicie sessão no Ambari como um utilizador de cluster

Iremos atribuiu nosso utilizador de domínio do Azure AD "hiveuser2" para o *Cluster utilizador* função. Esta função é capaz de aceder ao dashboard e todos os itens de menu. Um utilizador do cluster tem menos opções permitidas que um administrador. Por exemplo, hiveuser2 podem visualizar as configurações para cada um dos serviços, mas não é possível editá-los.

![Utilizador com função de utilizador de Cluster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Passos seguintes

* [Configurar políticas de Hive no HDInsight associados a um domínio](hdinsight-domain-joined-run-hive.md)
* [Gerir clusters do HDInsight associados a um domínio](hdinsight-domain-joined-manage.md)
* [Utilize a vista do Hive com o Hadoop no HDInsight](hdinsight-hadoop-use-hive-ambari-view.md)

<!-- * [Synchronize Azure AD users to the cluster](hdinsight-sync-aad-users-to-cluster.md) -->
