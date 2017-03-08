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
ms.sourcegitcommit: 4ba44128ec19d3937643ac934ca3e787cb9819a3
ms.openlocfilehash: 690ba97d2b0634548a0ec424d441ad34d70667b9
ms.lasthandoff: 02/27/2017


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planear clusters do Hadoop associados a um domínio do Azure no HDInsight

O Hadoop tradicional é um cluster de utilizador único. É adequado à maioria das empresas em que as cargas de trabalho de dados de grande dimensão são criadas por pequenas equipas de aplicações. À medida que a popularidade do Hadoop aumenta, muitas empresas estão a migrar para um modelo no qual os clusters são geridos pelas equipas de TI e em que várias equipas de aplicações partilham esses clusters. Assim, as funcionalidades que envolvem clusters multiutilizador estão entre as funcionalidades mais pedidas no Azure HDInsight.

Em vez de criar a sua própria autenticação e autorização multiutilizador, o HDInsight baseia-se no fornecedor de identidades mais popular, o Azure Active Directory (Azure AD). A poderosa funcionalidade de segurança do Azure AD pode ser utilizada para gerir a autorização multiutilizador no HDInsight. Ao integrar o HDInsight no Azure AD, os utilizadores podem utilizar as credenciais do Azure AD para comunicar com os clusters. O HDInsight mapeia o utilizador do Azure AD para um utilizador do Hadoop local, para que todos os serviços em execução no HDInsight (Ambari, servidor Hive, Ranger, servidor Spark thrift, entre outros) funcionem corretamente para o utilizador autenticado.

## <a name="integrate-hdinsight-with-azure-ad"></a>Integrar o HDInsight no Azure AD

Ao integrar o HDInsight no Azure AD, os nós do cluster do HDInsight são associados ao domínio do Azure AD. O HDInsight cria principais de serviço para os serviços do Hadoop em execução no cluster e coloca-os numa unidade organizacional (UO) especificada no Azure AD. O HDInsight também cria mapeamentos de DNS inversos no domínio do Azure AD para os endereços IP dos nós que estão associados ao domínio.

Para obter esta configuração, pode utilizar várias arquiteturas. Pode escolher de entre as arquiteturas seguintes.

**HDInsight integrado no Azure AD em execução no Azure IaaS**

Esta é a arquitetura mais simples para a integração do HDInsight no Azure AD. O controlador de domínio do Azure AD é executado numa ou em várias máquinas virtuais (VMs) no Azure. Normalmente, estas VMs estão dentro de uma rede virtual. Para o cluster do HDInsight, vai configurar outra rede virtual. De modo a que o HDInsight tenha uma linha visual para o Azure AD, tem de utilizar o [peering de VNet para VNet](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md) para configurar o peering nestas redes virtuais.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Nesta arquitetura, não pode utilizar o Azure Data Lake Store com o cluster do HDInsight.


Pré-requisitos para o Azure AD:

* Tem de ser criada uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual vai colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* Os [protocolos LDAP (Lightweight Directory Access Protocols)](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) têm de ser configurados para comunicação com o Azure AD. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da sub-rede do HDInsight (por exemplo, 10.2.0.0/24, na imagem anterior).
* É necessária uma conta de serviço ou uma conta de utilizador. Utilize esta conta para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory

**HDInsight integrado num Azure AD apenas na cloud**

Para um Azure AD apenas na cloud, configure um controlador de domínio para que o HDInsight possa ser integrado no Azure AD. Para tal, tem de utilizar o [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). O Azure AD DS cria máquinas de controlador de domínio na cloud e fornece os endereços IP das mesmas. Cria dois controladores de domínio, para elevada disponibilidade.

Atualmente, o Azure AD DS só existe em redes virtuais clássicas. Só é acessível através do portal clássico do Azure. A rede virtual do HDInsight existe no portal do Azure e é necessário configurar o peering da mesma para a rede virtual clássica, utilizando, para tal, o peering de VNet para VNet.

> [!NOTE]
> Para configurar o peering entre uma rede virtual clássica e uma rede virtual do Azure Resource Manager, ambas as redes virtuais têm de estar na mesma região e na mesma subscrição do Azure.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Azure AD:

* Tem de ser criada uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual vai colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado quando configurar o Azure AD DS. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da sub-rede do HDInsight (por exemplo, 10.2.0.0/24, na imagem anterior).
* Os [hashes de palavras-passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) têm de ser sincronizados a partir do Azure AD para o Azure AD DS.
* É necessária uma conta de serviço ou uma conta de utilizador. Utilize esta conta para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Azure AD

**HDInsight integrado num Active Directory no local através de VPN**

Esta arquitetura é semelhante à do HDInsight integrado no Azure AD em execução no Azure IaaS. A única diferença é que o Azure AD está no local e a linha de visão do HDInsight para o Azure AD faz-se através de uma [ligação VPN do Azure para a rede no local](../expressroute/expressroute-introduction.md).

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Nesta arquitetura, não pode utilizar o Azure Data Lake Store com o cluster do HDInsight.

Pré-requisitos para o Azure AD:

* Tem de ser criada uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual vai colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado para comunicação com o Azure AD. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da sub-rede do HDInsight (por exemplo, 10.2.0.0/24, na imagem anterior).
* É necessária uma conta de serviço ou uma conta de utilizador. Utilize esta conta para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Azure AD

**HDInsight integrado num Active Directory no local sincronizado com o Azure AD**

Esta arquitetura é semelhante à do HDInsight integrado num Azure AD apenas na cloud. A única diferença é que o Active Directory no local está sincronizado com o Azure AD. Configure um controlador de domínio na cloud para que o HDInsight possa ser integrado no Azure AD. Para tal, tem de utilizar o [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md). O Azure AD DS cria máquinas de controlador de domínio na cloud e fornece os endereços IP das mesmas. Cria dois controladores de domínio, para elevada disponibilidade.

Atualmente, o Azure AD DS só existe em redes virtuais clássicas. Só é acessível através do portal clássico do Azure. A rede virtual do HDInsight existe no portal do Azure e é necessário configurar o peering da mesma para a rede virtual clássica, utilizando, para tal, o peering de VNet para VNet.

> [!NOTE]
> Para configurar o peering entre uma rede virtual clássica e uma rede virtual do Azure Resource Manager, ambas as redes virtuais têm de estar na mesma região e na mesma subscrição do Azure.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Azure AD:

* Tem de ser criada uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual vai colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado quando configurar o Azure AD DS. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da sub-rede do HDInsight (por exemplo, 10.2.0.0/24, na imagem anterior).
* Os [hashes de palavras-passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) têm de ser sincronizados a partir do Azure AD para o Azure AD DS.
* É necessária uma conta de serviço ou uma conta de utilizador. Utilize esta conta para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory

**HDInsight integrado num Azure AD não predefinido (recomendado apenas para teste e desenvolvimento)**

Esta arquitetura é semelhante à do HDInsight integrado num Azure AD apenas na cloud. Na maioria das empresas, o acesso administrativo ao Azure AD está limitado a determinadas pessoas. Desta forma, se quiser realizar uma prova de conceito ou experimentar criar um cluster associado a um domínio, pode ser mais vantajoso criar uma instância do Azure AD na subscrição em vez de esperar que um administrador configure pré-requisitos no Azure AD. Uma vez que se trata de uma instância do Azure AD criada por si, tem permissões totais no Azure AD para configurar o Azure AD DS.

O Azure AD DS cria máquinas de controlador de domínio na cloud e fornece os endereços IP das mesmas. Cria dois controladores de domínio, para elevada disponibilidade.

O Azure AD DS só existe em redes virtuais clássicas, pelo que, para o configurar, tem de aceder ao portal clássico do Azure e de criar uma rede virtual clássica. A rede virtual do HDInsight existe no portal do Azure e é necessário configurar o peering da mesma para a rede virtual clássica, utilizando, para tal, o peering de VNet para VNet.

> [!NOTE]
> Para configurar o peering entre a rede virtual clássica e a rede virtual do Azure Resource Manager, ambas as redes virtuais têm de estar na mesma região e na mesma subscrição do Azure.

![Topologia do cluster do HDInsight associado a domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Azure AD:

* Tem de ser criada uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual vai colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) tem de ser configurado quando configurar o Azure AD DS. Pode criar um [certificado autoassinado](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) para configurar o LDAPS. No entanto, para utilizar um certificado autoassinado, tem de pedir uma exceção através de <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>.
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da sub-rede do HDInsight (por exemplo, 10.2.0.0/24, na imagem anterior).
* Os [hashes de palavras-passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) têm de ser sincronizados a partir do Azure AD para o Azure AD DS.
* É necessária uma conta de serviço ou uma conta de utilizador. Utilize esta conta para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Azure Active Directory

## <a name="next-steps"></a>Passos seguintes
* Para configurar um cluster do HDInsight associado a um domínio, veja [Configure domain-joined HDInsight clusters (Configurar clusters do HDInsight associados a um domínio)](hdinsight-domain-joined-configure.md).
* Para gerir clusters do HDInsight associados a um domínio, veja [Manage domain-joined HDInsight clusters (Gerir clusters do HDInsight associados a um domínio)](hdinsight-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, veja [Configurar políticas do Hive para clusters do HDInsight associados a um domínio](hdinsight-domain-joined-run-hive.md).
* Para executar consultas do Hive através do SSH em clusters do HDInsight associados a um domínio, veja [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

