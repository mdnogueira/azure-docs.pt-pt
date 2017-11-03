---
title: "Gerir as permissões de utilizador nos níveis de ficheiro e pasta - Azure HDInsight | Microsoft Docs"
description: "Como gerir as permissões de ficheiros e pastas para clusters do HDInsight associados a um domínio."
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
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>Gerir as permissões de utilizador nos níveis de ficheiros e pastas

[Associado a um domínio clusters do HDInsight](hdinsight-domain-joined-introduction.md) utilizar autenticação forte com utilizadores do Azure Active Directory (Azure AD) e também *controlo de acesso baseado em funções* políticas (RBAC) para vários serviços, como o Hive e o YARN. Se o arquivo de dados predefinido para o cluster de armazenamento do Azure ou Blobs de armazenamento do Azure (WASB) da Windows, também pode aplicar permissões ao nível da pasta de ficheiros e. Pode utilizar o Apache Ranger para controlar o acesso aos ficheiros do cluster para o seu sincronizados grupos e utilizadores do Azure AD.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

A instância do Apache Ranger associados a um domínio para clusters do HDInsight vem pré-configurada com o serviço de Ranger WASB. O serviço de Ranger WASB é um motor de gestão de política que é semelhante ao Ranger HDFS, mas com uma diferente a imposição de políticas de acesso do Ranger. No serviço Ranger WASB, se um pedido recebido do recurso não tiver uma política de Ranger correspondente, a resposta de predefinido é NEGAR. O serviço de Ranger não obtiver permissão a WASB verificar.

## <a name="permission-and-policy-model"></a>Modelo de permissão e de política

Pedidos de acesso de recursos são avaliados utilizando o fluxo seguinte:

![Fluxo de avaliação do Apache Ranger política](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

NEGAR regras são avaliadas em primeiro lugar, seguido de regras de permissão. No final de correspondentes, se não existem políticas são correspondidas, é devolvido um NEGAR.

### <a name="user-variable"></a>Variável de utilizador

Pode utilizar o `{USER}` variável quando a atribuição de políticas para um utilizador aceder a um `/{username}` subdiretório, por exemplo:

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

A política acima concede aos utilizadores acesso a sua própria subpasta, por baixo do `/app-logs/` diretório. Eis o aspeto desta política na interface de utilizador Ranger:

![Utilização de exemplo da variável de utilizador](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Exemplos de modelo de política

A tabela seguinte lista alguns exemplos de como funciona o modelo de política:

| Política de Ranger | Sistema de ficheiros existente | Pedido do utilizador | resultado |
| -- | -- | -- | -- |
| /Data/Finanças /, Bernardo, escrita | /data | Bernardo, criar o ficheiro /data/finance/mydatafile.txt | PERMITIR - pasta intermédio 'memorando' é criada, devido a verificação de predecessor |
| /Data/Finanças /, Bernardo, escrita | /data | Alice, criar ficheiros /data/finance/mydatafile.txt | NEGAR - nenhuma política correspondente |
| dados/Finanças *, Bernardo, escrita | /data | Bernardo, criar o ficheiro /data/finance/mydatafile.txt | PERMITIR - neste caso a política recursiva opcional (`*`) está presente; consulte [carateres universais](#wildcards) |
| /Data/Finance/mydatafile.txt, Bernardo, escrita | /data | Bernardo, criar o ficheiro /data/finance/mydatafile.txt | NEGAR - verificação predecessor ' / data' falhar porque não há nenhuma política |
| /Data/Finance/mydatafile.txt, Bernardo, escrita | dados/financeiro | Bernardo, criar o ficheiro /data/finance/mydatafile.txt | NEGAR - nenhuma política para verificação predecessor '/ dados/Finanças' |

São necessárias permissões ao nível da pasta ou ao nível do ficheiro, consoante o tipo de operação. Por exemplo, uma chamada de "leitura/Abra" necessita de acesso de leitura ao nível do ficheiro, enquanto uma chamada de "Criar" necessita de permissões ao nível da pasta de predecessor.

### <a name="wildcards-"></a>Carateres universais (*)

Quando um caráter universal (`*`) está presente no caminho para uma política, o caráter universal aplica-se a que o caminho e a respetiva subárvore completa. Este recursão é igual a utilizar um `recurse-flag`. No Ranger-WASB, o caráter universal indica recursão e nome parcial correspondente.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Gerir ficheiros e permissões de nível de pasta com Apache Ranger

Se ainda não o fez, siga [estas instruções](hdinsight-domain-joined-configure.md) para aprovisionar um novo cluster associados a um domínio.

Abrir Ranger WASB procurando `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`. Introduza o nome de utilizador de administrador de cluster e a palavra-passe que definiu quando criar o cluster.

Depois de iniciarem sessão, consulte o dashboard Ranger:

![Dashboard de Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

Para ver as permissões atuais de ficheiros e pastas para o cluster associado a conta de armazenamento do Azure, clique o  ***CLUSTERNAME*_wasb** ligação na caixa de controlo de WASB.

![Dashboard de Ranger](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

É apresentada a lista de política atual. Várias políticas típicas incluídas como um ponto de partida - Verifique os detalhes de cada política para ver as utilizações de exemplo.

Para cada política, pode ver se a política for ativada, se um registo de auditoria é configurado e quaisquer grupos atribuídos e os utilizadores. Existem dois botões de ação para cada política: editar e eliminar.

![Lista de política](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Adicionar uma nova política

1. No canto superior direito da página de políticas WASB, selecione **Adicionar nova política**.

    ![Adicionar a nova política](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. Introduza um descritivo **nome da política**. Especifique o Azure **conta de armazenamento** para o seu cluster (*nome_conta*. w) e o **contentor da conta de armazenamento** especificada quando criou o cluster. Introduza o **caminho relativo** (relativo ao cluster) para o ficheiro ou pasta acedida.

    ![Novo formulário de política](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Abaixo do formulário, especifique o **permitir condições** para este novo recurso. Selecione utilizadores e grupos aplicáveis e definir as respetivas permissões. No exemplo seguinte, está a permitir que todos os utilizadores no `sales` grupo para ter acesso de leitura/escrita.

    ![Permitir vendas](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Selecione **Guardar**.

### <a name="example-policy-conditions"></a>Condições da política de exemplo

O Apache Ranger [fluxo de avaliação da política](#permission-and-policy-model) permite-lhe especificar qualquer combinação de permissão e negação condições para satisfazer as suas necessidades. Eis alguns exemplos:

1. Permitir que todos os utilizadores de vendas, mas não interns:

    ![Permitir vendas, interns de negação](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Todos os utilizadores de vendas de permissão e negação interns todas, exceto um estagiário com o nome "hiveuser3", que devem ter acesso de leitura:

    ![Permitir vendas, interns exceto hiveuser3 de negação](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Passos seguintes

* [Configurar políticas de Hive no HDInsight associados a um domínio](hdinsight-domain-joined-run-hive.md)
* [Gerir clusters do HDInsight associados a um domínio](hdinsight-domain-joined-manage.md)
* [Gerir Ambari - autorizar os utilizadores Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

