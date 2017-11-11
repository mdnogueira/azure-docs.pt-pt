---
title: "Configurar clusters do HDInsight associados a um domínio - Azure | Microsoft Docs"
description: "Saiba como configurar e clusters do HDInsight associados a um domínio"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 2c844ce8aec04c74a9c2dbecdd1b3effb286df97
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configurar os clusters do HDInsight associados a um domínio (pré-visualização)

Saiba como configurar um cluster do Azure HDInsight com o Azure Active Directory (Azure AD) e [Apache Ranger](http://hortonworks.com/apache/ranger/) para tirar partido da autenticação forte e de acesso baseado em funções abundante políticas de controlo (RBAC).  Associado a um domínio HDInsight só pode ser configurada em clusters baseados em Linux. Para obter mais informações, consulte [clusters do HDInsight associados a um domínio introduzir](apache-domain-joined-introduction.md).

> [!IMPORTANT]
> Oozie não está ativado no HDInsight associados a um domínio.

Este artigo é o primeiro tutorial de uma série de:

* Crie um cluster do HDInsight ligado ao Azure AD (através da capacidade de serviços de domínio do Azure) com Ranger Apache ativada.
* Criar e aplicar políticas de ramo de registo através do Apache Ranger e permitir que os utilizadores (por exemplo, cientistas de dados) ligar ao ramo de registo utilizando ferramentas baseadas em ODBC, por exemplo, Excel, Tableau etc. Microsoft está a funcionar sobre como adicionar outras cargas de trabalho, como o Storm e o HBase no HDInsight associados a um domínio em breve.

Os nomes de serviço do Azure tem de ser globalmente exclusivos. Os nomes seguintes são utilizados neste tutorial. Contoso é um nome fictício. Tem de substituir *contoso* com um nome diferente ao seguir o tutorial. 

**Nomes:**

| Propriedade | Valor |
| --- | --- |
| Diretório do Azure AD |contosoaaddirectory |
| Nome de domínio do Azure AD |Contoso (contoso.onmicrosoft.com) |
| HDInsight VNet |contosohdivnet |
| Grupo de recursos do HDInsight VNet |contosohdirg |
| Cluster do HDInsight |contosohdicluster |

Este tutorial fornece os passos para configurar um cluster do HDInsight associados a um domínio. Cada secção tem ligações para outros artigos com mais informações de fundo.

## <a name="prerequisite"></a>Pré-requisito:
* Familiarize-se com [serviços de domínio do Azure AD](https://azure.microsoft.com/services/active-directory-ds/) respetivo [preços](https://azure.microsoft.com/pricing/details/active-directory-ds/) estrutura.
* Certifique-se de que a sua subscrição está na lista de permissões para esta pré-visualização pública. Pode fazê-lo, enviando um e-mail para hdipreview@microsoft.com com o ID de subscrição.
* Um certificado SSL assinado por uma autoridade de assinatura ou um certificado autoassinado para o seu domínio. O certificado é necessário para a configuração de LDAP seguro.

## <a name="procedures"></a>Procedimentos
1. Crie uma VNet do HDInsight no modo de gestão de recursos do Azure.
2. Criar e configurar o Azure AD e o Azure AD DS.
3. Crie um cluster do HDInsight.

> [!NOTE]
> Este tutorial parte do princípio de que não dispõe de um Azure AD. Se tiver uma, pode ignorar esse parte.
> 
> 

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Criar uma VNet do Resource Manager para o cluster do HDInsight
Nesta secção, irá criar uma VNet do Resource Manager do Azure que será utilizado para o cluster do HDInsight. Para obter mais informações sobre a criação de VNet do Azure através de outros métodos, consulte [criar uma rede virtual](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Depois de criar a VNet, irá configurar o Azure AD DS para utilizar nesta VNet.

**Para criar uma VNet do Resource Manager**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **novo**, **redes**e, em seguida, **rede Virtual**. 
3. No **selecionar um modelo de implementação**, selecione **Resource Manager**e, em seguida, clique em **criar**.
4. Escreva ou selecione os seguintes valores:
   
   * **Nome**: contosohdivnet
   * **Espaço de endereços**: 10.0.0.0/16.
   * **Nome da sub-rede**: Subnet1
   * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
   * **Subscrição**: (selecione a sua subscrição do Azure.)
   * **Grupo de recursos**: contosohdirg
   * **Localização**: (selecionar a mesma localização que a VNet do Azure AD. Por exemplo, contosoaadvnet.)
5. Clique em **Criar**.

**Configurar o DNS para a VNet do Resource Manager**

1. Do [portal do Azure](https://portal.azure.com), clique em **mais serviços** > **redes virtuais**. Certifique-se para não clique **redes virtuais (clássicas)**.
2. Clique em **contosohdivnet**.
3. Clique em **servidores DNS** do lado esquerdo do painel novo.
4. Clique em **personalizada**e, em seguida, introduza os seguintes valores:
   
   * 10.0.0.4
   * 10.0.0.5     
     
5. Clique em **Guardar**.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Criar e configurar o Azure AD DS do Azure AD
Nesta secção, irá:

1. Crie um Azure AD.
2. Crie utilizadores do Azure AD. Estes utilizadores são utilizadores do domínio. Utilize o primeiro utilizador para configurar o cluster do HDInsight com o Azure AD.  Outros dois utilizadores são opcionais para este tutorial. Serão utilizados no [Hive configurar políticas para clusters do HDInsight associados a um domínio](apache-domain-joined-run-hive.md) quando configurar políticas de Apache Ranger.
3. Criar o grupo de administradores de DC do AAD e adicione o utilizador do Azure AD para o grupo. Utilize este utilizador para criar a unidade organizacional.
4. Ative os serviços de domínio do Azure AD (Azure AD DS) para o Azure AD.
5. Configure LDAPS para o Azure AD. O acesso protocolo LDAP (Lightweight Directory) é utilizado para leem e escrevem para o Azure AD.

Se preferir utilizar uma existente do Azure AD, pode ignorar os passos 1 e 2.

**Para criar um Azure AD**

1. Do [portal clássico do Azure](https://manage.windowsazure.com), clique em **novo** > **serviços aplicacionais** > **do Active Directory** > **diretório** > **criação personalizada**. 
2. Introduza ou selecione os seguintes valores:
   
   * **Nome**: contosoaaddirectory
   * **Nome de domínio**: contoso.  Este nome tem de ser globalmente exclusivo.
   * **País ou região**: selecione o seu país ou região.
3. Clique em **Concluído**.

**Criar um utilizador do Azure AD**

1. Do [portal do Azure](https://portal.azure.com), clique em **do Azure Active Directory** > **contosoaaddirectory** > **utilizadores e grupos**. 
2. Clique em **todos os utilizadores** no menu.
3. Clique em **novo utilizador**.
4. Introduza **nome** e **nome de utilizador**e, em seguida, clique em **seguinte**. 
5. Configurar o perfil de utilizador No **função**, selecione **Administrador Global**; e, em seguida, clique em **seguinte**.  A função de Administrador Global é necessário para criar unidades organizacionais.
6. Faça uma cópia da palavra-passe temporária.
7. Clique em **Criar**. Mais tarde no tutorial, irá utilizar este utilizador de administrador global para criar o cluster do HDInsight.

Siga o mesmo procedimento para criar dois utilizadores mais com os **utilizador** função, hiveuser1 e hiveuser2. Os seguintes utilizadores vão ser utilizados na [Hive configurar políticas para clusters do HDInsight associados a um domínio](apache-domain-joined-run-hive.md).

**Para criar o grupo dos administradores de DC AAD e adicionar um utilizador do Azure AD**

1. Do [portal do Azure](https://portal.azure.com), clique em **do Azure Active Directory** > **contosoaaddirectory** > **utilizadores e grupos**. 
2. Clique em **todos os grupos** no menu superior.
3. Clique em **novo grupo**.
4. Introduza ou selecione os seguintes valores:
   
   * **Nome**: os administradores de DC do AAD.  Não altere o nome do grupo.
   * **Tipo de associação**: atribuído.
5. Clique em **Selecionar**.
6. Clique em **membros**.
7. Selecione o primeiro utilizador que criou no passo anterior e, em seguida, clique em **selecione**.
8. Repita os mesmos passos para criar outro grupo chamado **HiveUsers**, e adicione os dois utilizadores de ramo de registo para o grupo.

Para obter mais informações, consulte [serviços de domínio do Azure AD (pré-visualização) - criar o grupo 'AAD DC administradores'](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Para ativar o Azure AD DS para o seu Azure AD**

1. Do [portal do Azure](https://portal.azure.com), clique em **crie um recurso** > **segurança + identidade** > **osserviçosdedomíniodoAzureAD**  >  **Adicionar**. 
2. Introduza ou selecione os seguintes valores:
   * **Nome do diretório**: contosoaaddirectory
   * **Nome de domínio DNS**: Esta ação mostra o nome DNS predefinido do diretório do Azure. Por exemplo, contoso.onmicrosoft.com.
   * **Localização**: selecione a região.
   * **Rede**: selecione a rede virtual e a sub-rede que criou anteriormente. Por exemplo, **contosohdivnet**.
3. Clique em **OK** da página de resumo. Verá **implementação em curso...**  em notificações.
4. Aguarde até que **implementação em curso...**  desaparece, e **endereço IP** obtém preenchido. Irão obter preenchidos dois endereços IP. Estes são os endereços IP dos controladores de domínio aprovisionados pelos serviços de domínio. Cada endereço IP será visível depois do controlador de domínio correspondente está aprovisionado e pronto. Anote os dois endereços IP. Vai precisar deles mais tarde.

Para obter mais informações, consulte [ativar o Azure Active Directory Domain Services no portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Para sincronizar a palavra-passe**

Se utilizar o seu próprio domínio, terá de sincronizar a palavra-passe. Consulte [ativar a sincronização de palavra-passe para serviços de domínio do Azure AD para um Azure apenas na nuvem diretório AD](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**Para configurar o LDAPS para o Azure AD**

1. Obtenha um certificado SSL assinado por uma autoridade de assinatura para o seu domínio.
2. Do [portal do Azure](https://portal.azure.com), clique em **serviços de domínio do Azure AD** > **contoso.onmicrosoft.com**. 
3. Ativar **LDAP seguro**.
6. Siga as instruções para especificar o ficheiro de certificado e a palavra-passe.  
7. Aguarde até que **proteger o certificado de LDAP** obteve preenchido. Isto pode demorar até 10 minutos ou mais.

> [!NOTE]
> Se algumas tarefas em segundo plano estão a ser executadas o Azure AD DS, poderá ver um erro ao carregar o certificado - <i>há uma operação que está a ser executada para este inquilino. Tente novamente mais tarde</i>.  No caso de ocorrer este erro, tente novamente após algum tempo. O segundo IP do controlador de domínio pode demorar até 3 horas a ser aprovisionado.
> 
> 

Para obter mais informações, consulte [configurar Secure LDAP (LDAPS) para um serviços de domínio do Azure AD geridos domínio](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-hdinsight-cluster"></a>Criar cluster do HDInsight
Nesta secção, vai criar um cluster do Hadoop baseados em Linux no HDInsight utilizando o portal do Azure ou [modelo Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Para outros métodos de criação do cluster e compreender as definições, consulte [criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como utilizar o modelo do Resource Manager para criar clusters do Hadoop no HDInsight, consulte [clusters do Hadoop criar no HDInsight com modelos do Resource Manager](../hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Para criar um cluster do HDInsight associados a um domínio no portal do Azure**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **novo**, **Intelligence + análise**e, em seguida, **HDInsight**.
3. Do **cluster do HDInsight novo** painel, introduza ou selecione os seguintes valores:
   
   * **Nome do cluster**: introduza um novo nome de cluster para o cluster do HDInsight associados a um domínio.
   * **Subscrição**: selecione uma subscrição do Azure utilizada para criar este cluster.
   * **Configuração de cluster**:
     
     * **Tipo de cluster**: Hadoop. Associado a um domínio HDInsight atualmente é apenas suportado no Hadoop, Spark e clusters de consulta interativa.
     * **Sistema operativo**: Linux.  Associado a um domínio HDInsight só é suportada nos clusters do HDInsight baseado em Linux.
     * **Versão**: HDI 3.6. Associado a um domínio HDInsight só é suportado em clusters do HDInsight versão 3.6.
     * **Tipo de cluster**: PREMIUM
       
       Clique em **selecione** para guardar as alterações.
   * **Credenciais**: Configure as credenciais para o utilizador de cluster e o utilizador SSH.
   * **Origem de dados**: criar uma nova conta de armazenamento ou utilizar uma conta de armazenamento existente como a conta de armazenamento predefinido para o cluster do HDInsight. A localização tem de ser o mesmo que as duas VNets.  A localização é também a localização do cluster do HDInsight.
   * **Preços**: selecione o número de nós de trabalho do cluster.
   * **As configurações avançadas**: 
     
     * **Associação ao domínio & Vnet/sub-rede**: 
       
       * **As definições de domínio**: 
         
         * **Nome de domínio**: contoso.onmicrosoft.com
         * **Nome de utilizador de domínio**: introduza um nome de utilizador de domínio. Este domínio tem de ter os seguintes privilégios: associar computadores ao domínio e colocá-los na unidade de organização que especificar durante a criação do cluster; Criar principais de serviço dentro a unidade de organização que especificar durante a criação do cluster; Criação de entradas DNS inversas. Este utilizador de domínio deixará do administrador deste cluster do HDInsight associados a um domínio.
         * **Palavra-passe do domínio**: introduza a palavra-passe de utilizador do domínio.
         * **Unidade organizacional**: introduza o nome único da UO que pretende utilizar com o cluster do HDInsight. Por exemplo: UO = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com. Se não existir essa UO, o cluster do HDInsight irá tentar criar essa UO. Certifique-se a UO já está presente ou a conta de domínio tem permissões para criar um novo. Se utilizar a conta de domínio que faz parte dos administradores AADDC, terá as permissões necessárias para criar a unidade Organizacional.
         * **URL de LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Grupo de utilizadores de acesso**: Especifique o grupo de segurança cujos utilizadores que pretende sincronizar para o cluster. Por exemplo, HiveUsers.
           
           Clique em **selecione** para guardar as alterações.
           
           ![Portal de HDInsight associados a um domínio configura a definição de domínio](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Rede virtual**: contosohdivnet
       * **Sub-rede**: Subnet1
         
         Clique em **selecione** para guardar as alterações.        
         Clique em **selecione** para guardar as alterações.
   * **Grupo de recursos**: selecione o grupo de recursos utilizado para a HDInsight VNet (contosohdirg).
4. Clique em **Criar**.  

Outra opção para criar cluster do HDInsight associados a um domínio consiste em utilizar o modelo de gestão de recursos do Azure. O procedimento seguinte mostra-lhe como:

**Para criar um cluster do HDInsight associados a um domínio utilizando um modelo de gestão de recursos**

1. Clique na imagem seguinte para abrir um modelo do Resource Manager no portal do Azure. O modelo do Resource Manager está localizado num contentor de blob público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Do **parâmetros** painel, introduza os seguintes valores:
   
   * **Subscrição**: (selecionar a sua subscrição do Azure).
   * **Grupo de recursos**: clique em **utilizar existente**, e especifique o mesmo grupo de recursos que estiver a utilizar.  Por exemplo contosohdirg. 
   * **Localização**: Especifique uma localização do grupo de recursos.
   * **Nome do Cluster**: introduza um nome para o cluster do Hadoop que irá criar. Por exemplo contosohdicluster.
   * **Tipo de cluster**: selecione um tipo de cluster.  O valor predefinido é **hadoop**.
   * **Localização**: selecione uma localização para o cluster.  A conta de armazenamento predefinido utiliza a mesma localização.
   * **Número de nós de trabalho de cluster**: selecione o número de nós de trabalho.
   * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é **admin**.
   * **Nome de utilizador e palavra-passe SSH**: o nome de utilizador predefinido é **sshuser**.  Pode alterá-lo. 
   * **Id de rede virtual**: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >
   * **Rede de sub-rede virtual**: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName > sub-redes/Subnet1
   * **Nome de domínio**: contoso.onmicrosoft.com
   * **DN de unidade de organização**: UO = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com
   * **Grupo de utilizadores do cluster DNs**: [\"HiveUsers\"]
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (introduzir o nome de utilizador de administrador de domínio)
   * **DomainAdminPassword**: (introduzir a palavra-passe de utilizador do administrador de domínio)
   * **Concordo com os termos e condições indicadas acima**: (verificar)
   * **Afixar ao dashboard**: (verificar)
3. Clique em **Comprar**. Verá um novo mosaico intitulado **A implementar implementação de modelo**. A criação de um cluster demora cerca de 20 minutos. Depois do cluster for criado, pode clicar no painel do cluster no portal para abri-lo.

Depois de concluir o tutorial, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Para instruções de eliminação de um cluster, consulte [clusters do Hadoop gerir no HDInsight ao utilizar o portal do Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Passos seguintes
* Para configurar políticas do Hive e executar consultas do Hive, veja [Configurar políticas do Hive para clusters do HDInsight associados a um domínio](apache-domain-joined-run-hive.md).
* Para utilizar o SSH para ligar a clusters do HDInsight associados a um domínio, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

