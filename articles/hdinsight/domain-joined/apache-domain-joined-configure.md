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
ms.openlocfilehash: af75d63caca24f389345c964e2dc506a255bec19
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configurar os clusters do HDInsight associados a um domínio (pré-visualização)

Saiba como configurar um cluster do Azure HDInsight com o Azure Active Directory (Azure AD) e [Apache Ranger](http://hortonworks.com/apache/ranger/) para tirar partido da autenticação forte e de acesso baseado em funções abundante políticas de controlo (RBAC).  Associado a um domínio HDInsight só pode ser configurada em clusters baseados em Linux. Para obter mais informações, consulte [clusters do HDInsight associados a um domínio introduzir](apache-domain-joined-introduction.md).

> [!IMPORTANT]
> Oozie não está ativado no HDInsight associados a um domínio.

Este artigo é o primeiro tutorial de uma série de:

* Crie um cluster do HDInsight ligado ao Azure AD (através da capacidade de serviços de domínio do Azure) com Ranger Apache ativada.
* Criar e aplicar políticas de ramo de registo através do Apache Ranger e permitir que os utilizadores (por exemplo, cientistas de dados) ligar ao ramo de registo utilizando ferramentas baseadas em ODBC, por exemplo, Excel, Tableau etc. Microsoft está a trabalhar na adição de outras cargas de trabalho, como o HBase, Spark e Storm, para o HDInsight associados a um domínio em breve.

Um exemplo de topologia final procura da seguinte forma:

![Topologia de HDInsight associados a um domínio](./media/apache-domain-joined-configure/hdinsight-domain-joined-topology.png)

Uma vez do Azure AD atualmente suporta apenas a redes virtuais clássicas (VNets) e clusters do HDInsight baseado em Linux suportam apenas do Azure Resource Manager com base em VNets, integração de HDInsight do Azure AD requer duas VNets e um peering entre eles. Para as informações de comparação entre os modelos de implementação de dois, consulte [vs. de implementação clássica do Azure Resource Manager: compreender o estado dos seus recursos e modelos de implementação](../../azure-resource-manager/resource-manager-deployment-model.md). Tem de ser as duas VNets na mesma região que o Azure AD DS.

Os nomes de serviço do Azure tem de ser globalmente exclusivos. Os nomes seguintes são utilizados neste tutorial. Contoso é um nome fictício. Tem de substituir *contoso* com um nome diferente ao seguir o tutorial. 

**Nomes:**

| Propriedade | Valor |
| --- | --- |
| VNet do Azure AD |contosoaadvnet |
| Grupo de recursos do Azure AD Vnet |contosoaadrg |
| Diretório do Azure AD |contosoaaddirectory |
| Nome de domínio do Azure AD |Contoso (contoso.onmicrosoft.com) |
| HDInsight VNet |contosohdivnet |
| Grupo de recursos do HDInsight VNet |contosohdirg |
| Cluster do HDInsight |contosohdicluster |

Este tutorial fornece os passos para configurar um cluster do HDInsight associados a um domínio. Cada secção tem ligações para outros artigos com mais informações de fundo.

## <a name="prerequisite"></a>Pré-requisito:
* Familiarize-se com [serviços de domínio do Azure AD](https://azure.microsoft.com/services/active-directory-ds/) respetivo [preços](https://azure.microsoft.com/pricing/details/active-directory-ds/) estrutura.
* Certifique-se de que a sua subscrição está na lista de permissões para esta pré-visualização pública. Pode fazê-lo, enviando um e-mail para hdipreview@microsoft.com com o ID de subscrição.
* Um certificado SSL assinado por uma autoridade de assinatura para o seu domínio. O certificado é exigido pela configuração de LDAP seguro. Não não possível utilizar certificados autoassinados.

## <a name="procedures"></a>Procedimentos
1. Crie uma VNet clássica do Azure do Azure AD.  
2. Criar e configurar o Azure AD e o Azure AD DS.
3. Crie uma VNet do HDInsight no modo de gestão de recursos do Azure.
4. Ponto a ponto as duas VNets.
5. Crie um cluster do HDInsight.

> [!NOTE]
> Este tutorial parte do princípio de que não dispõe de um Azure AD. Se tiver uma, pode ignorar a parte no passo 2.
> 
> 

## <a name="create-an-azure-virtual-network-classic"></a>Criar uma rede virtual do Azure (clássica)
Nesta secção, pode cria uma rede virtual (clássica) no portal do Azure. Na secção seguinte, ativar o Azure AD DS do Azure AD na rede virtual. Para obter mais informações sobre o seguinte procedimento e utilização de outros métodos de criação de rede virtual, consulte [criar uma rede virtual (clássica) utilizando o portal do Azure](../../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

**Para criar uma VNet clássica**

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Clique em **novo** > **redes** > **rede Virtual**.
3. No **selecionar um modelo de implementação**, selecione **clássico**e, em seguida, clique em **criar**.
4. Introduza ou selecione os seguintes valores:
   
   * **Nome**: contosoaadvnet
   * **Espaço de endereços**: 10.1.0.0/16
   * **Nome da sub-rede**: Subnet1
   * **Intervalo de endereços da sub-rede**: 10.1.0.0/24
   * **Subscrição**: (selecionar uma subscrição utilizada para criar esta VNet).
   * **ResourceGroup**: contosoaadrg
   * **Localização**: (selecione uma região para o cluster de HDInsight).
     
     > [!IMPORTANT]
     > Tem de escolher uma localização que suporte do Azure AD DS. Para obter mais informações, veja [Produtos disponíveis por região](https://azure.microsoft.com/en-us/regions/services/). 
     > 
     > A VNet clássica e a VNet do grupo de recursos tem de ser na mesma região que o Azure AD DS.
     > 
     > 
5. Clique em **Criar** para criar a VNet.

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

1. Do [portal clássico do Azure](https://manage.windowsazure.com), clique em **do Active Directory** -> **contosoaaddirectory**. 
2. Clique em **utilizadores** no menu superior.
3. Clique em **adicionar utilizador**.
4. Introduza **nome de utilizador**e, em seguida, clique em **seguinte**. 
5. Configurar o perfil de utilizador No **função**, selecione **Administrador Global**; e, em seguida, clique em **seguinte**.  A função de Administrador Global é necessário para criar unidades organizacionais.
6. Clique em **criar** para obter uma palavra-passe temporária.
7. Fazer uma cópia da palavra-passe e, em seguida, clique em **concluída**. Mais tarde no tutorial, irá utilizar este utilizador de administrador global para criar o cluster do HDInsight.

Siga o mesmo procedimento para criar dois utilizadores mais com os **utilizador** função, hiveuser1 e hiveuser2. Os seguintes utilizadores vão ser utilizados na [Hive configurar políticas para clusters do HDInsight associados a um domínio](apache-domain-joined-run-hive.md).

**Para criar o grupo dos administradores de DC AAD e adicionar um utilizador do Azure AD**

1. Do [portal clássico do Azure](https://manage.windowsazure.com), clique em **do Active Directory** > **contosoaaddirectory**. 
2. Clique em **grupos** no menu superior.
3. Clique em **adicionar um grupo** ou **adicionar grupo**.
4. Introduza ou selecione os seguintes valores:
   
   * **Nome**: os administradores de DC do AAD.  Não altere o nome do grupo.
   * **Tipo de grupo**: segurança.
5. Clique em **Concluído**.
6. Clique em **AAD DC administradores** para abrir o grupo.
7. Clique em **adicionar membros**.
8. Selecione o primeiro utilizador que criou no passo anterior e, em seguida, clique em **concluída**.
9. Repita os mesmos passos para criar outro grupo chamado **HiveUsers**, e adicione os dois utilizadores de ramo de registo para o grupo.

Para obter mais informações, consulte [serviços de domínio do Azure AD (pré-visualização) - criar o grupo 'AAD DC administradores'](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Para ativar o Azure AD DS para o seu Azure AD**

1. Do [portal clássico do Azure](https://manage.windowsazure.com), clique em **do Active Directory** > **contosoaaddirectory**. 
2. Clique em **configurar** no menu superior.
3. Desloque para baixo até **dos serviços de domínio**e defina os seguintes valores:
   
   * **Ativar os serviços de domínio para este diretório**: Sim.
   * **Nome de domínio DNS dos serviços de domínio**: Esta ação mostra o nome DNS predefinido do diretório do Azure. Por exemplo, contoso.onmicrosoft.com.
   * **Ligar os serviços de domínio a esta rede virtual**: selecione a rede virtual clássica que criou anteriormente, ou seja, **contosoaadvnet**.
4. Clique em **guardar** na parte inferior da página. Verá **pendentes...**  junto a **ativar os serviços de domínio para este diretório**.  
5. Aguarde até que **pendentes...**  desaparece, e **endereço IP** obtém preenchido. Irão obter preenchidos dois endereços IP. Estes são os endereços IP dos controladores de domínio aprovisionados pelos serviços de domínio. Cada endereço IP será visível depois do controlador de domínio correspondente está aprovisionado e pronto. Anote os dois endereços IP. Vai precisar deles mais tarde.

Para obter mais informações, consulte [ativar o Azure Active Directory Domain Services no portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Para sincronizar a palavra-passe**

Se utilizar o seu próprio domínio, terá de sincronizar a palavra-passe. Consulte [ativar a sincronização de palavra-passe para serviços de domínio do Azure AD para um Azure apenas na nuvem diretório AD](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**Para configurar o LDAPS para o Azure AD**

1. Obtenha um certificado SSL assinado por uma autoridade de assinatura para o seu domínio. Se pretender utilizar um certificado autoassinado, contacte hdipreview@microsoft.com para uma exceção.
2. Do [portal clássico do Azure](https://manage.windowsazure.com), clique em **do Active Directory** > **contosoaaddirectory**. 
3. Clique em **configurar** no menu superior.
4. Desloque-se para **dos serviços de domínio**.
5. Clique em **configurar certificado**.
6. Siga as instruções para especificar o ficheiro de certificado e a palavra-passe. Verá **pendentes...**  junto a **ativar os serviços de domínio para este diretório**.  
7. Aguarde até que **pendentes...**  desaparece, e **proteger o certificado de LDAP** obteve preenchido.  Isto pode demorar até 10 minutos ou mais.

> [!NOTE]
> Se algumas tarefas em segundo plano estão a ser executadas o Azure AD DS, poderá ver um erro ao carregar o certificado - <i>há uma operação que está a ser executada para este inquilino. Tente novamente mais tarde</i>.  No caso de ocorrer este erro, tente novamente após algum tempo. O segundo IP do controlador de domínio pode demorar até 3 horas a ser aprovisionado.
> 
> 

Para obter mais informações, consulte [configurar Secure LDAP (LDAPS) para um serviços de domínio do Azure AD geridos domínio](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Criar uma VNet do Resource Manager para o cluster do HDInsight
Nesta secção, irá criar uma VNet do Resource Manager do Azure que será utilizado para o cluster do HDInsight. Para obter mais informações sobre a criação de VNET do Azure através de outros métodos, consulte [criar uma rede virtual](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Depois de criar a VNet, irá configurar a VNet do Resource Manager para utilizar os mesmos servidores DNS para a VNet do Azure AD. Se seguiu os passos neste tutorial para criar a VNet clássica e o Azure AD, os servidores DNS são 10.1.0.4 e 10.1.0.5.

**Para criar uma VNet do Resource Manager**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **novo**, **redes**e, em seguida, **rede Virtual**. 
3. No **selecionar um modelo de implementação**, selecione **Resource Manager**e, em seguida, clique em **criar**.
4. Escreva ou selecione os seguintes valores:
   
   * **Nome**: contosohdivnet
   * **Espaço de endereços**: 10.2.0.0/16. Certifique-se de que o intervalo de endereços não pode sobrepor-se com o intervalo de endereços IP da VNet clássica.
   * **Nome da sub-rede**: Subnet1
   * **Intervalo de endereços da sub-rede**: 10.2.0.0/24
   * **Subscrição**: (selecione a sua subscrição do Azure.)
   * **Grupo de recursos**: contosohdirg
   * **Localização**: (selecionar a mesma localização como o Azure AD VNet, ou seja, contosoaadvnet.)
5. Clique em **Criar**.

**Configurar o DNS para a VNet do Resource Manager**

1. Do [portal do Azure](https://portal.azure.com), clique em **mais serviços** -> **redes virtuais**. Certifique-se para não clique **redes virtuais (clássicas)**.
2. Clique em **contosohdivnet**.
3. Clique em **servidores DNS** do lado esquerdo do painel novo.
4. Clique em **personalizada**e, em seguida, introduza os seguintes valores:
   
   * 10.1.0.4
   * 10.1.0.5
     
     Estes endereços IP do servidor DNS têm de corresponder aos servidores DNS da VNet do Azure AD (VNet clássica).
5. Clique em **Guardar**.

## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Elemento VNet do Azure AD e a VNet do HDInsight
**A ponto da VNet dois**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **mais serviços** no menu esquerdo.
3. Clique em **redes virtuais**. Não clique **redes virtuais (clássicas)**.
4. Clique em **contosohdivnet**.  Esta é a VNet do HDInsight.
5. Clique em **Peerings** no menu da esquerda do painel.
6. Clique em **adicionar** no menu superior. É aberto o **adicionar peering** painel.
7. No **adicionar peering** painel, definir ou selecione os seguintes valores:
   
   * **Nome**: ContosoAADHDIVNetPeering
   * **Modelo de implementação de rede virtual**: clássico
   * **Subscrição**: selecione o nome de subscrição utilizado para a vnet clássica (Azure AD).
   * **Rede virtual**: contosoaadvnet.
   * **Permitir o acesso de rede virtual**: (verificar)
   * **Permitir tráfego reencaminhado**: (verificar). Deixe as outras duas caixas de verificação desmarcada.
8. Clique em **OK**.

## <a name="create-hdinsight-cluster"></a>Criar cluster do HDInsight
Nesta secção, vai criar um cluster do Hadoop baseados em Linux no HDInsight utilizando o portal do Azure ou [modelo Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Para outros métodos de criação do cluster e compreender as definições, consulte [criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como utilizar o modelo do Resource Manager para criar clusters do Hadoop no HDInsight, consulte [clusters do Hadoop criar no HDInsight com modelos do Resource Manager](../hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Para criar um cluster do HDInsight associados a um domínio no portal do Azure**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **novo**, **Intelligence + análise**e, em seguida, **HDInsight**.
3. Do **cluster do HDInsight novo** painel, introduza ou selecione os seguintes valores:
   
   * **Nome do cluster**: introduza um novo nome de cluster para o cluster do HDInsight associados a um domínio.
   * **Subscrição**: selecione uma subscrição do Azure utilizada para criar este cluster.
   * **Configuração de cluster**:
     
     * **Tipo de cluster**: Hadoop. HDInsight associados a um domínio atualmente é apenas suportada em Hadoop clusters.
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

