---
title: "Arquitetura do Azure HDInsight associados a um domínio | Microsoft Docs"
description: "Saiba como planear o HDInsight associado a um domínio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: saurinsh
ms.openlocfilehash: 9deb5e4dbd925c4fdc523d8d21afbcfbf85947b8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planear clusters do Hadoop associados a um domínio do Azure no HDInsight

O Hadoop tradicional é um cluster de utilizador único. É adequado à maioria das empresas em que as cargas de trabalho de dados de grande dimensão são criadas por pequenas equipas de aplicações. À medida que a popularidade do Hadoop aumenta, muitas empresas estão a migrar para um modelo no qual os clusters são geridos pelas equipas de TI e em que várias equipas de aplicações partilham esses clusters. Assim, as funcionalidades que envolvem clusters multiutilizador estão entre as funcionalidades mais pedidas no Azure HDInsight.

Em vez de criar a sua própria multiuser autenticação e autorização, o HDInsight baseia-se no fornecedor de identidade mais popular – do Active Directory (AD). A funcionalidade de segurança poderosas no AD pode ser utilizada para gerir a autorização multiuser no HDInsight. Ao integrar o HDInsight com o AD, pode comunicar com os clusters com as suas credenciais do AD. HDInsight mapeia um utilizador do AD para um utilizador local do Hadoop, por isso, todos os serviços em execução no HDInsight (Ambari, thrift de Spark do servidor, Ranger, de ramo de registo do servidor e outros) funcionam perfeitamente para o utilizador autenticado.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight no Active Directory

Quando integrar o HDInsight com o Active Directory, os nós de cluster do HDInsight estão associados a um domínio a um domínio do AD. HDInsight cria principais de serviço para os serviços de Hadoop em execução no cluster e coloca-os dentro de uma unidade organizacional (UO) especificada no domínio. HDInsight também cria mapeamentos inversas de DNS no domínio para os endereços IP de nós que estão associados ao domínio.

Existem duas opções de implementação para o Active Directory:
* **[Os serviços de domínio do Active Directory do Azure](../../active-directory-domain-services/active-directory-ds-overview.md):** este serviço fornece um domínio do Active Directory gerido, que é totalmente compatível com o Windows Server Active Directory. Microsoft encarrega-se de gerir, a aplicação de patches e a monitorização de domínio do AD. Pode implementar o cluster sem se preocupar manter os controladores de domínio. Os utilizadores, grupos e as palavras-passe são sincronizadas a partir do Azure Active Directory, permitindo aos utilizadores iniciar sessão para o cluster utilizando as respetivas credenciais empresariais.

* **Domínio do Active Directory do Windows Server em VMs do IaaS do Azure:** nesta opção, implementar e gerir o seu próprio domínio do Active Directory do Windows Server em VMs do IaaS do Azure. 

Para obter esta configuração, pode utilizar várias arquiteturas. Pode escolher de entre as arquiteturas seguintes.


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>HDInsight integrado com um serviços de domínio do Azure AD geridos domínio AD
Pode implementar um [do Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS) geridos por domínio. Azure AD DS fornece um domínio gerido do AD no Azure, o que é gerido, atualizado e monitorizado pela Microsoft. Cria dois controladores de domínio para elevada disponibilidade e inclui os serviços DNS. Em seguida, pode integrar o cluster do HDInsight com este domínio gerido. Com esta opção de implementação, não precisa de preocupar com a gerir, patches, atualizar e monitorização controladores de domínio.

![Topologia do cluster do HDInsight associado a domínio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para integrar com os serviços de domínio do Azure AD:

* [Aprovisionar um domínio do Azure AD para serviços geridos domínio](../../active-directory-domain-services/active-directory-ds-getting-started.md).
* Criar um [unidade organizacional](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), no qual é colocar o cluster do HDInsight VMs e os principais de serviço utilizada pelo cluster.
* A configuração [LDAPS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md), quando configurar o Azure AD DS. O certificado utilizado para configurar LDAPS deve ser emitido por uma autoridade de certificação pública (não um certificado autoassinado).
* Crie zonas DNS inversas no domínio gerido para o intervalo de endereços IP da sub-rede HDInsight (por exemplo, 10.2.0.0/24 na imagem anterior).
* Configurar [a sincronização de hashes de palavra-passe necessária para a autenticação NTLM e Kerberos](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) do Azure AD para o domínio gerido do Azure AD DS.
* É necessária uma conta de serviço ou uma conta de utilizador. Utilize esta conta para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Azure AD


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>HDInsight integrado com o Windows Server AD em execução no IaaS do Azure

Pode implementar a função do Windows Server Active Directory Domain Services num (ou vários) máquinas de virtuais (VMs) no Azure e promover que sejam controladores de domínio. Estas VMs do controlador de domínio pode ser implementado utilizando o modelo de implementação do Gestor de recursos na mesma rede virtual que o cluster do HDInsight. Se os controladores de domínio são implementados numa rede virtual diferente, terá de elemento estas redes virtuais utilizando [vnet para VNet peering](../../virtual-network/virtual-network-create-peering.md). 

[Mais informações, implementar o Windows Server Active Directory em VMs do Azure](../../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![Topologia do cluster do HDInsight associado a domínio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Nesta arquitetura, não pode utilizar o Azure Data Lake Store com o cluster do HDInsight.


Pré-requisitos para integrar com o Windows Server Active Directory em VMs do Azure:

* Tem de ser criada uma [unidade organizacional](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) na qual vai colocar as VMs do cluster do HDInsight e os principais de serviço que o cluster utiliza.
* [Protocolos de acesso do Lightweight Directory](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAPs) tem de ser configurados para comunicar com o AD. O certificado utilizado para configurar o LDAPS tem de ser um certificado real (não um certificado autoassinado).
* Têm de ser criadas zonas DNS inversas no domínio para o intervalo de endereços IP da sub-rede do HDInsight (por exemplo, 10.2.0.0/24, na imagem anterior).
* É necessária uma conta de serviço ou uma conta de utilizador. Utilize esta conta para criar o cluster do HDInsight. Esta conta tem de ter as seguintes permissões:

    - Permissões para criar objetos de principal de serviço e objetos de máquina na unidade organizacional
    - Permissões para criar regras de proxy do DNS inverso
    - Permissões para associar máquinas ao domínio do Active Directory


## <a name="next-steps"></a>Passos seguintes
* Para configurar um cluster do HDInsight associado a um domínio, veja [Configure domain-joined HDInsight clusters (Configurar clusters do HDInsight associados a um domínio)](apache-domain-joined-configure.md).
* Para gerir clusters do HDInsight associados a um domínio, veja [Manage domain-joined HDInsight clusters (Gerir clusters do HDInsight associados a um domínio)](apache-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, veja [Configurar políticas do Hive para clusters do HDInsight associados a um domínio](apache-domain-joined-run-hive.md).
* Para executar consultas do Hive através de SSH nos clusters do HDInsight associados a um domínio, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
