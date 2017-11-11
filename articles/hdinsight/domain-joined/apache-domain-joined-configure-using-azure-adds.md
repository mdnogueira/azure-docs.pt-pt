---
title: "Configurar clusters do HDInsight associados a um domínio através do Azure Active Directory Domain Services - Azure | Microsoft Docs"
description: "Saiba como configurar e clusters do HDInsight associados a um domínio através do Azure Active Directory Domain Services"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: saurinsh
ms.openlocfilehash: cf8532334f03f72691fa09e8dbdd02b78072cf38
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurar clusters do HDInsight associados a um domínio através do Azure Active Directory Domain Services

Os clusters associados a um domínio fornecem empresa multiutilizador capacidades de segurança no HDInsight. Associado a um domínio clusters do HDInsight estão ligados a domínios do Active Directory, para que os utilizadores de domínio podem utilizar as respetivas credenciais de domínio para autenticar com os clusters e executar tarefas de macrodados. 

Existem três formas de configurar um controlador de domínio para que possam ligar um cluster do HDInsight associados a um domínio para:

- Serviços de domínio do Azure Active Directory (Azure AD DS)
- Active Directory no local
- Controlador de domínio de diretório Active Directory em VMs do IaaS do Azure

Neste artigo, irá aprender a configurar um cluster do HDInsight associados a um domínio utilizando o Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Criar ADDS do Azure

Terá de criar um Azure do AD DS antes de poder criar um cluster do HDInsight. Para criar um Azure adiciona, consulte [ativar o Azure Active Directory Domain Services no portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Apenas os administradores do inquilino tem os privilégios para criar serviços de domínio. 

Depois do serviço de domínio tiver sido aprovisionado, terá de criar uma conta de serviço no **os administradores de DC do Azure AD** grupo para criar o cluster do HDInsight. A conta de Service tem de ser um administrador global sobre o Azure AD.

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Criar um cluster do HDInsight associados a um domínio

O passo seguinte consiste em Criar cluster do HDInsight com o AAD DS e a conta de serviço que criou na secção anterior.

É mais fácil colocar o serviço de domínio do Azure AD e o cluster do HDInsight a mesma network(VNet) virtual do Azure. No caso de estarem em VNets diferentes, tem de elemento ambas as VNets. Para obter mais informações, consulte [peering de rede Virtual](../../virtual-network/virtual-network-peering-overview.md).

Quando cria um cluster do HDInsight associados a um domínio, tem de fornecer os parâmetros seguintes:

- **Nome de domínio**: O nome de domínio que estão associado com o Azure AD DS. Por exemplo, contoso.onmicrosoft.com
- **Nome de utilizador de domínio**: A conta de serviço no grupo de administradores de DC do Azure AD que é criado na secção anterior. Por exemplo, hdiadmin@contoso.onmicrosoft.com. Este utilizador de domínio é o administrador deste cluster do HDInsight associados a um domínio.
- **Palavra-passe do domínio**: A palavra-passe da conta de serviço.
- **Unidade organizacional**: O nome distinto da UO que pretende utilizar com o cluster do HDInsight. Por exemplo: UO = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com. Se essa UO não existir, o cluster do HDInsight tenta criar essa UO. 
- **URL de LDAPS**: por exemplo, ldaps://contoso.onmicrosoft.com:636
- **Grupo de utilizadores de acesso**: os grupos de segurança cujos utilizadores que pretende sincronizar para o cluster. Por exemplo, HiveUsers. Se pretender especificar vários grupos de utilizadores, separadas por vírgula ','.
 
A seguinte captura de ecrã mostra as configurações no portal do Azure:

![Configuração de serviços de domínio do Active Directory associados a um domínio do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Passos seguintes
* Para configurar políticas do Hive e executar consultas do Hive, veja [Configurar políticas do Hive para clusters do HDInsight associados a um domínio](apache-domain-joined-run-hive.md).
* Para utilizar o SSH para ligar a clusters do HDInsight associados a um domínio, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

