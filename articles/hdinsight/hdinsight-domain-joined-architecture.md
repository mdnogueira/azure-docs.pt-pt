---
title: "Arquitetura do Azure HDInsight associado a um domínio| Microsoft Docs"
description: "Saiba como planear o HDInsight associado a um domínio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: b5cd321f3cd4c415f5e97ca550b7ab1679324921
ms.openlocfilehash: 94ef8147f1f73b293e818fc508096789373df7fc


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planear clusters do Hadoop associados a um domínio do Azure no HDInsight

O Hadoop tradicional é um cluster de utilizador único. Adequa-se à maioria das empresas em que as cargas de trabalho de macrodados são criadas por pequenas equipas de aplicações. À medida que a popularidade do Hadoop aumenta, muitas empresas estão a migrar para um modelo no qual os clusters são geridos pelas equipas de TI e em que várias equipas de aplicações partilham esses clusters. Assim, os clusters de multi-utilizadores são uma das funcionalidades mais pedidas no HDInsight.

Em vez de criar a sua própria autenticação e autorização de multi-utilizadores, o HDInsight baseia-se no fornecedor de identidades mais popular, o Active Directory (AD). A poderosa funcionalidade de grupos de segurança do Active Directory pode ser utilizada para gerir a autorização de multi-utilizadores no HDInsight. Ao integrar o Active Directory no HDInsight, os utilizadores do Active Directory podem utilizar as credenciais deste serviço para comunicar com os clusters. O HDInsight mapeia o utilizador do Active Directory para um utilizador do Hadoop local, para que todos os serviços em execução no HDInsight (Ambari, servidor Hive, Ranger, servidor Spark thrift, etc.) funcionem corretamente para o utilizador autenticado.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight no Active Directory

Ao integrar o HDInsight no Active Directory, os nós do cluster do HDInsight são associados ao domínio do Active Directory. O HDInsight cria principais de serviço para os serviços do Hadoop em execução no cluster e coloca-os numa Unidade Organizacional (UO) especificada no Active Directory. Também cria mapeamentos de DNS inversos no domínio do Active Directory para os endereços IP dos nós que estão associados ao domínio.

Para obter esta configuração, pode seguir várias arquiteturas. Tem de determinar que arquitetura funciona melhor para o seu caso.

**1. HDInsight integrado no AD em execução no Azure IaaS**

Esta é a arquitetura mais simples para a integração do HDInsight no Active Directory. O controlador de domínio do Active Directory é executado numa ou em várias máquinas virtuais no Azure. Normalmente, estas VMs estão dentro de uma rede virtual. Para o cluster do HDInsight, vai configurar outra rede virtual. De modo a que o HDInsight tenha uma linha visual para o Active Directory, tem de utilizar o [peering de VNet para VNet](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md) para configurar o peering nestas redes virtuais.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Nesta arquitetura, não pode utilizar o Azure Data Lake Store com o cluster do HDInsight.
 

Pré-requisitos para o Active Directory:

* Tem de ser criada uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual pretenda colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado para comunicação com o Active Directory. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da Sub-rede do HDInsight (por exemplo, 10.2.0.0/24, na imagem anterior).
* É necessária uma conta de serviço ou uma conta de utilizador, que é utilizada para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional.
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory.

**2. HDInsight integrado num Azure AD apenas na cloud**

Para um Azure Active Directory apenas na cloud (Azure AD), tem de configurar um controlador de domínio para que o HDInsight possa ser integrado no seu Azure Active Directory. Para tal, tem de utilizar o [Azure Active Directory domain services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). O Azure AD DS cria máquinas de controlador de domínio na cloud e fornece-lhe os endereços IP das mesmas. Cria dois controladores de domínio, para elevada disponibilidade.

Atualmente, o Azure AD DS só existe em VNets clássicas. Só é acessível através do portal clássico do Azure. A VNet do HDInsight existe no portal do Azure e é necessário configurar o peering da mesma para a VNet clássica, utilizando, para tal, o peering de VNet para Vnet.

> [!NOTE]
> Para configurar o peering entre uma VNet clássica e uma VNet do Azure Resource Manager, ambas as VNets têm de estar na mesma região e na mesma subscrição do Azure.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Active Directory:

* Tem de ser criada uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual pretenda colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza. 
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado quando configurar o AD DS. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da Sub-rede do HDI (por exemplo, 10.2.0.0/24, na imagem anterior). 
* Os [hashes de palavras-passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) têm de ser sincronizados a partir do Azure AD para o AD DS.
* É necessária uma conta de serviço ou uma conta de utilizador, que é utilizada para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional.
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory.

**3. HDInsight integrado num AD no local através de VPN**

Esta arquitetura é semelhante à primeira. A única diferença é que o Active Directory está no local e a linha de visão do HDInsight para o Active Directory faz-se através de uma [ligação VPN do Azure para a rede no local](../expressroute/expressroute-introduction.md).

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Nesta arquitetura, não pode utilizar o Azure Data Lake Store com o cluster do HDInsight.

Pré-requisitos para o Active Directory:

* Tem de ser criada uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual pretenda colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado para comunicação com o Active Directory. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da Sub-rede do HDI (por exemplo, 10.2.0.0/24, na imagem anterior).
* É necessária uma conta de serviço ou uma conta de utilizador, que é utilizada para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional.
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory.

**4. HDInsight integrado num AD no local sincronizado com um Azure AD**

Esta arquitetura é semelhante à segunda. A única diferença é que o Active Directory no local está sincronizado com o Azure Active Directory. Tem de configurar um controlador de domínio na cloud, de modo a que o HDInsight possa ser integrado no seu Azure Active Directory. Para tal, tem de utilizar o [Azure Active Directory domain services](../active-directory-domain-services/active-directory-ds-overview.md) (AD DS). O AD DS cria máquinas de controlador de domínio na cloud e fornece-lhe os endereços IP das mesmas. Cria dois controladores de domínio, para elevada disponibilidade.

Atualmente, o Azure AD DS só existe em VNets clássicas. Só é acessível através do portal clássico do Azure. A VNet do HDInsight existe no portal do Azure e é necessário configurar o peering da mesma para a VNet clássica, utilizando, para tal, o peering de VNet para Vnet.

> [!NOTE]
> Para configurar o peering entre uma VNet clássica e uma VNet do Azure Resource Manager, ambas as VNets têm de estar na mesma região e na mesma subscrição do Azure.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Active Directory:

* Tem de ser criada uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual pretenda colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza. 
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado quando configurar o AD DS. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da Sub-rede do HDI (por exemplo, 10.2.0.0/24, na imagem anterior). 
* Os [hashes de palavras-passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) têm de ser sincronizados a partir do Azure AD para o AD DS.
* É necessária uma conta de serviço ou uma conta de utilizador, que é utilizada para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional.
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory.

**5. HDInsight integrado num Azure AD não predefinido (recomendado apenas para teste e desenvolvimento)**

Esta arquitetura é semelhante à segunda. Na maioria das empresas, o acesso administrativo ao Active Directory está limitado a determinadas pessoas. Desta forma, se quiser adicionar uma validação de conceito ou criar apenas um cluster associado a domínio, em vez de esperar que o administrador configure pré-requisitos no Active Directory, pode ser mais vantajoso criar simplesmente um Azure Active Directory novo na subscrição. Uma vez que este é um Azure AD criado por si, tem permissões completas no mesmo para configurar o AD DS.

O AD DS cria máquinas de controlador de domínio na cloud e fornece-lhe os endereços IP das mesmas. Cria dois controladores de domínio, para elevada disponibilidade.

Atualmente, o AD DS só existe em VNets clássicas, pelo que, para o configurar, tem de aceder ao portal Clássico e de criar uma VNet clássica. A VNet do HDInsight existe no portal do Azure e é necessário configurar o peering da mesma para a VNet clássica, utilizando, para tal, o peering de VNet para Vnet.

> [!NOTE]
> Para configurar o peering entre VNets clássicas e do Azure Resource Manager, ambas as VNets têm de estar na mesma região e na mesma subscrição do Azure.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Active Directory:

* Tem de ser criada uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual pretenda colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza. 
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado quando configurar o AD DS. Pode criar um [certificado autoassinado](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) para configurar o LDAPS. No entanto, para utilizar um certificado autoassinado, tem de pedir uma exceção através de <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>.
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da Sub-rede do HDI (por exemplo, 10.2.0.0/24, na imagem anterior). 
* Os [hashes de palavras-passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) têm de ser sincronizados a partir do Azure AD para o AD DS.
* É necessária uma conta de serviço ou uma conta de utilizador, que é utilizada para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional.
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory.

## <a name="next-steps"></a>Passos seguintes
* Para configurar um cluster do HDInsight associado a um domínio, veja [Configurar clusters do HDInsight associados a um domínio](hdinsight-domain-joined-configure.md).
* Para gerir clusters do HDInsight associados a um domínio, veja [Manage Domain-joined HDInsight clusters (Gerir clusters do HDInsight associados a um domínio)](hdinsight-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, veja [Configurar políticas do Hive para clusters do HDInsight associados a um domínio](hdinsight-domain-joined-run-hive.md).
* Para executar consultas do Hive através do SSH em clusters do HDInsight associados a um domínio, veja [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).




<!--HONumber=Feb17_HO1-->


