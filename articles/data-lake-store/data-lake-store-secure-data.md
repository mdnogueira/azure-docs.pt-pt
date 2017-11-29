---
title: Proteger os dados armazenados no Azure Data Lake Store | Microsoft Docs
description: Saiba como proteger dados no Azure Data Lake Store utilizando grupos e listas de controlo de acesso
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e246abaf8d1d7939765e1c878f6dfaf6375294f6
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Proteger os dados armazenados no Azure Data Lake Store
Proteger dados no Azure Data Lake Store é uma abordagem de três passos.

1. Comece por criar grupos de segurança no Azure Active Directory (AAD). Estes grupos de segurança são utilizados para implementar o controlo de acesso baseado em funções (RBAC) no portal do Azure. Para obter mais informações, consulte [controlo de acesso baseado em funções no Microsoft Azure](../active-directory/role-based-access-control-configure.md).
2. Atribua os grupos de segurança do AAD para a conta do Azure Data Lake Store. Controla o acesso à conta do Data Lake Store das operações de gestão de portal e do portal ou de APIs.
3. Atribua os grupos de segurança do AAD como acesso (ACL) de listas de controlo no sistema de ficheiros de Data Lake Store.
4. Além disso, também pode definir um intervalo de endereços IP para clientes que podem aceder aos dados no Data Lake Store.

Este artigo fornece instruções sobre como utilizar o portal do Azure para realizar as tarefas acima. Para obter informações aprofundadas sobre como o Data Lake Store implementa a segurança ao nível da conta e de dados, consulte [segurança no Azure Data Lake Store](data-lake-store-security-overview.md). Para informações de descrição profunda sobre como as ACLs são implementadas no Azure Data Lake Store, consulte [descrição geral do controlo de acesso no Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Azure Active Directory
Para obter instruções sobre como criar grupos de segurança do AAD e como adicionar utilizadores ao grupo, consulte [gerir grupos de segurança no Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Pode adicionar utilizadores e outros grupos a um grupo no Azure AD através do portal do Azure. No entanto, para poder adicionar um principal de serviço a um grupo, utilize [módulo do PowerShell do Azure AD](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Atribuir utilizadores ou grupos de segurança para contas de Azure Data Lake Store
Quando atribui os utilizadores ou grupos de segurança para contas de Azure Data Lake Store, controlar o acesso às operações de gestão da conta a utilizar o portal do Azure e as APIs do Azure Resource Manager. 

1. Abra uma conta do Azure Data Lake Store. No painel esquerdo, clique em **procurar**, clique em **Data Lake Store**e, em seguida, no painel Data Lake Store, clique no nome de conta para o qual pretende atribuir um utilizador ou grupo de segurança.

2. No painel de definições de conta do Data Lake Store, clique em **controlo de acesso (IAM)**. O painel por listas de predefinição **administradores da subscrição** grupo como um proprietário.
   
    ![Atribua o grupo de segurança à conta do Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "atribuir o grupo de segurança à conta do Azure Data Lake Store")

    Existem duas formas de adicionar um grupo e atribuir funções relevantes.
   
    * Adicione um utilizador/grupo para a conta e, em seguida, atribuir uma função, ou
    * Adicionar uma função e, em seguida, atribuir utilizadores/grupos à função.
     
    Nesta secção, vamos ver a primeira abordagem, adicionar um grupo e, em seguida, atribuir funções. Pode efetuar os passos semelhantes para primeiro selecione uma função e, em seguida, atribuir os grupos a essa função.
4. No **utilizadores** painel, clique em **adicionar** para abrir o **adicionar acesso** painel. No **adicionar acesso** painel, clique em **selecionar uma função**e, em seguida, selecione uma função de utilizador/grupo.
   
    ![Adicionar uma função para o utilizador](./media/data-lake-store-secure-data/adl.add.user.1.png "adicionar uma função de utilizador")
   
    O **proprietário** e **contribuinte** função fornecer acesso a uma variedade de funções de administração sobre a conta do data lake. Para os utilizadores que irão interagir com os dados no data lake, pode adicioná-los para o * * leitor * * função. O âmbito destas funções está limitado para as operações de gestão relacionados com a conta do Azure Data Lake Store.
   
    Para dados permissões do sistema de ficheiros individuais de operações definem o que os utilizadores podem fazer. Por conseguinte, um utilizador ter uma função de leitor pode ver apenas as definições administrativas associadas à conta, mas pode potencialmente ler e escrever dados com base nas permissões do sistema de ficheiros atribuídas. Permissões de sistema de ficheiros do Data Lake Store são descritas em [atribuir grupo de segurança como ACLs o sistema de ficheiros do Azure Data Lake Store](#filepermissions).
5. No **adicionar acesso** painel, clique em **adicionar utilizadores** para abrir o **adicionar utilizadores** painel. Neste painel, procure o grupo de segurança que criou anteriormente no Azure Active Directory. Se tiver muitos grupos a pesquisar a partir do, utilize a caixa de texto na parte superior para filtrar o nome do grupo. Clique em **Selecionar**.
   
    ![Adicionar um grupo de segurança](./media/data-lake-store-secure-data/adl.add.user.2.png "adicionar um grupo de segurança")
   
    Se pretender adicionar um grupo/utilizador não estiver listado, pode convidá-los utilizando o **convidar** ícone e especificar o endereço de correio eletrónico para o utilizador/grupo.
6. Clique em **OK**. Deverá ver o grupo de segurança adicionado conforme mostrado abaixo.
   
    ![Grupo de segurança adicionado](./media/data-lake-store-secure-data/adl.add.user.3.png "grupo de segurança adicionado")

7. O grupo de segurança/utilizador tem agora acesso à conta do Azure Data Lake Store. Se pretender fornecer acesso a utilizadores específicos, pode adicioná-los ao grupo de segurança. Da mesma forma, se de que pretende revogar o acesso de um utilizador, pode removê-los do grupo de segurança. Também pode atribuir vários grupos de segurança para uma conta. 

## <a name="filepermissions"></a>Atribuir utilizadores ou grupo de segurança como ACLs para o sistema de ficheiros do Azure Data Lake Store
Através da atribuição de grupos de segurança/utilizador para o sistema de ficheiros do Azure Data Lake, definir o controlo de acesso em dados armazenados no Azure Data Lake Store.

1. No painel da conta do Data Lake Store, clique em **Explorador de Dados**.
   
    ![Criar diretórios na conta do Data Lake Store](./media/data-lake-store-secure-data/adl.start.data.explorer.png "criar diretórios na conta do Data Lake")
2. No **Explorador de dados** painel, clique no ficheiro ou pasta para o qual pretende configurar a ACL e, em seguida, clique em **acesso**. Para atribuir ACL para um ficheiro, tem de clicar em **acesso** do **pré-visualização do ficheiro** painel.
   
    ![Definir ACLs no sistema de ficheiros do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "definir ACLs no sistema de ficheiros do Data Lake")
3. O **acesso** painel lista o acesso padrão e personalizada de acesso já atribuída para a raiz. Clique em de **adicionar** ícone para adicionar as ACLs de nível de personalizado.
   
    ![Lista de acesso padrão e personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "lista acesso padrão e personalizado")
   
   * **Acesso padrão** é o acesso de UNIX estilo, onde pode Especifica ler, escrever, executar (rwx) para três classes de utilizador distintos: proprietário, grupo e outros.
   * **Acesso personalizados** corresponde às ACLs de POSIX permite-lhe definir as permissões para utilizadores nomeados específicos ou grupos e não apenas o ficheiro proprietário ou grupo. 
     
     Para obter mais informações, consulte [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para obter mais informações sobre como as ACLs são implementadas no Data Lake Store, consulte [controlo de acesso no Data Lake Store](data-lake-store-access-control.md).
4. Clique em de **adicionar** ícone para abrir o **adicionar acesso de personalizado** painel. Neste painel, clique em **selecionar utilizador ou grupo**e, em seguida, no **selecionar utilizador ou grupo** painel, procure o grupo de segurança que criou anteriormente no Azure Active Directory. Se tiver muitos grupos a pesquisar a partir do, utilize a caixa de texto na parte superior para filtrar o nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **selecione**.
   
    ![Adicione um grupo](./media/data-lake-store-secure-data/adl.acl.3.png "adicionar um grupo")
5. Clique em **selecionar permissões**, selecione as permissões e se pretende atribuir as permissões por predefinição ACL, aceder a ACL, ou ambos. Clique em **OK**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.acl.4.png "atribuir permissões de grupo")
   
    Para obter mais informações sobre as permissões no Data Lake Store e predefinido/acesso ACLs, consulte [controlo de acesso no Data Lake Store](data-lake-store-access-control.md).
6. No **adicionar acesso de personalizado** painel, clique em **OK**. O grupo recentemente adicionado, com as permissões associadas, será agora listado no **acesso** painel.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.acl.5.png "atribuir permissões de grupo")
   
   > [!IMPORTANT]
   > Na versão atual, só pode ter 9 entradas em **personalizada acesso**. Se pretender adicionar mais do que 9 utilizadores, deve criar grupos de segurança, adicionar utilizadores para grupos de segurança, adicione fornecer acesso aos grupos de segurança para a conta de Data Lake Store.
   > 
   > 
7. Se necessário, também pode modificar as permissões de acesso, depois de ter adicionado o grupo. Desmarque ou selecione a caixa de verificação para cada tipo de permissão (leitura, escrita, execução) com base em se pretende remover ou atribuir essa permissão ao grupo de segurança. Clique em **guardar** para guardar as alterações ou **rejeitar** de anular as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir o intervalo de endereços IP para acesso a dados
O Azure Data Lake Store permite-lhe bloqueio mais baixo de acesso ao seu arquivo de dados ao nível da rede. Pode ativar a firewall, especifique um endereço IP ou definir um intervalo de endereços IP para os seus clientes fidedignos. Uma vez ativada, apenas os clientes com endereços IP dentro do intervalo definido podem ligar para o arquivo.

![As definições da firewall e IP acesso](./media/data-lake-store-secure-data/firewall-ip-access.png "endereço IP e definições da Firewall")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Remover grupos de segurança para uma conta do Azure Data Lake Store
Quando remover grupos de segurança das contas do Azure Data Lake Store, está a alterar apenas acesso para as operações de gestão da conta a utilizar o Portal do Azure e as APIs do Azure Resource Manager.

1. No painel de conta do Data Lake Store, clique em **definições**. Do **definições** painel, clique em **utilizadores**.
   
    ![Atribua o grupo de segurança à conta do Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "atribuir o grupo de segurança para a conta do Azure Data Lake")
2. No **utilizadores** painel clique no grupo de segurança que pretende remover.
   
    ![Grupo de segurança para remover](./media/data-lake-store-secure-data/adl.add.user.3.png "grupo de segurança para remover")
3. No painel do grupo de segurança, clique em **remover**.
   
    ![Grupo de segurança removido](./media/data-lake-store-secure-data/adl.remove.group.png "removido de grupo de segurança")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Remova as ACLs do grupo de segurança do sistema de ficheiros do Azure Data Lake Store
Quando remover ACLs de grupos de segurança do sistema de ficheiros do Azure Data Lake Store, alterar o acesso aos dados do Data Lake Store.

1. No painel da conta do Data Lake Store, clique em **Explorador de Dados**.
   
    ![Criar diretórios na conta do Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "criar diretórios na conta do Data Lake")
2. No **Explorador de dados** painel, clique no ficheiro ou pasta para a qual pretende remover a ACL e, em seguida, no painel da conta, clique em de **acesso** ícone. Para remover a ACL de um ficheiro, tem de clicar em **acesso** do **pré-visualização do ficheiro** painel.
   
    ![Definir ACLs no sistema de ficheiros do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "definir ACLs no sistema de ficheiros do Data Lake")
3. No **acesso** painel, do **personalizada acesso** secção, clique no grupo de segurança que pretende remover. No **personalizada acesso** painel, clique em **remover** e, em seguida, clique em **OK**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.remove.acl.png "atribuir permissões de grupo")

## <a name="see-also"></a>Consultar também
* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
* [Copiar dados de Blobs de armazenamento do Azure para o Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introdução ao Data Lake Store através do PowerShell](data-lake-store-get-started-powershell.md)
* [Introdução ao Data Lake Store com .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Access diagnostic logs for Data Lake Store (Registos de diagnóstico de acesso do Data Lake Store)](data-lake-store-diagnostic-logs.md)

