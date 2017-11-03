---
title: "Considerações de segurança para o SQL Server no Azure | Microsoft Docs"
description: "Este tópico fornece orientações gerais para proteger o SQL Server em execução de uma Máquina Virtual no Azure."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2017
ms.author: jroth
ms.openlocfilehash: 4ad9156e481eac0bae32bca35a2b126363e5d8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações de Segurança para SQL Server em Máquinas Virtuais do Azure

Este tópico inclui geral diretrizes de segurança que ajudam a estabelecer um acesso seguro a instâncias do SQL Server numa máquina virtual do Azure (VM).

Azure está em conformidade com várias normas que podem permitir-lhe criar uma solução em conformidade com o SQL Server em execução numa máquina virtual e regulamentos da indústria. Para obter informações sobre a conformidade regulamentar com o Azure, consulte [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Controlo de acesso à VM do SQL Server

Ao criar a máquina virtual do SQL Server, considere como cuidadosamente controlar quem tem acesso para a máquina e SQL Server. Em geral, deve fazer o seguinte:

- Restringir o acesso ao SQL Server para apenas as aplicações e os clientes que precisem dele.
- Siga as melhores práticas para gerir contas de utilizador e palavras-passe.

As secções seguintes fornecem sugestões sobre pensar através destes pontos.

## <a name="secure-connections"></a>Ligações seguras

Quando cria uma máquina virtual do SQL Server com uma imagem de galeria, o **conectividade do SQL Server** opção dá-lhe a opção de **Local (no interior da VM)**, **privada (na Virtual Network)**, ou **público (Internet)**.

![Conectividade do SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Para garantir a melhor segurança, escolha a opção mais restritiva para o seu cenário. Por exemplo, se estiver a executar uma aplicação que acede ao SQL Server na mesma VM, em seguida, **Local** é a opção mais segura. Se estiver a executar uma aplicação do Azure que requer acesso ao SQL Server, em seguida, **privada** protege a comunicação com o SQL Server apenas dentro especificado [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Se necessitar de **pública** (internest) aceder ao SQL Server VM, em seguida, certifique-se a seguir outros procedimentos recomendados neste tópico para reduzir a sua área de superfície de ataque.

As opções selecionadas no portal do utilizam regras de segurança de entrada nas VMs [grupo de segurança de rede](../../../virtual-network/virtual-networks-nsg.md) (NSG) para permitir ou negar o tráfego de rede à máquina virtual. Pode modificar ou criar novas regras NSG de entrada para permitir tráfego para a porta do SQL Server (a predefinição é 1433). Também pode especificar endereços IP específicos que estão autorizados a comunicar através desta porta.

![Regras do grupo de segurança de rede](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Para além de regras NSG para restringir o tráfego de rede, também pode utilizar a Firewall do Windows na máquina virtual.

Se estiver a utilizar os pontos finais com o modelo de implementação clássica, remova quaisquer pontos finais na máquina virtual se não utilizá-los. Para obter instruções sobre a utilização de ACLs com pontos finais, consulte [gerir ACL num ponto final](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). Não é necessário para as VMs que utilizam o Gestor de recursos.

Por fim, considere ativar ligações encriptadas para a instância do motor de base de dados do SQL Server na sua máquina virtual do Azure. Configure a instância do SQL Server com um certificado assinado. Para obter mais informações, consulte [ativar ligações encriptadas para o motor de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) e [sintaxe da cadeia de ligação](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Utilizar uma porta não predefinido

Por predefinição, o SQL Server escuta numa porta bem conhecida, 1433. Para maior segurança, configure o SQL Server para escutar numa porta não predefinidos, tais como 1401. Se aprovisionar uma imagem de galeria do SQL Server no portal do Azure, pode especificar esta porta no **definições do SQL Server** painel.

Para configurar após o aprovisionamento, tem duas opções:

- Para VMs do Gestor de recursos, pode selecionar **configuração do SQL Server** do painel de descrição geral VM. Isto fornece uma opção para alterar a porta.

  ![Alterar a porta TCP no portal](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Para VMs clássicas ou para VMs de SQL Server que não tenham sido aprovisionados com o portal, pode configurar manualmente a porta ao ligar remotamente à VM. Para os passos de configuração, consulte [configurar um servidor para escutar numa porta TCP específica](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Se utilizar esta técnica manual, também terá de adicionar uma regra de Firewall do Windows para permitir tráfego de entrada nessa porta de TCP.

> [!IMPORTANT]
> Especificar uma porta não predefinida é uma boa ideia se a porta do SQL Server está aberta para ligações de internet pública.

Quando o SQL Server está à escuta numa porta não predefinido, tem de especificar a porta ao estabelecer ligação. Por exemplo, considere um cenário em que o endereço IP do servidor é 13.55.255.255 e SQL Server está a escutar na porta 1401. Para ligar ao SQL Server, tem de especificar `13.55.255.255,1401` na cadeia de ligação.

## <a name="manage-accounts"></a>Gerir contas

Não pretende que os atacantes adivinhar facilmente os nomes das contas ou palavras-passe. Utilize as seguintes sugestões para:

- Criar uma conta de administrador local exclusivo que não tem um nome **administrador**.

- Utilize palavras-passe fortes complexas para todas as suas contas. Para obter mais informações sobre como criar uma palavra-passe segura, consulte [criar uma palavra-passe segura](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) artigo.

- Por predefinição, o Azure seleciona a autenticação do Windows durante a configuração de Máquina Virtual do SQL Server. Por conseguinte, o **SA** início de sessão está desativado e uma palavra-passe é atribuída pelo programa de configuração. Recomendamos que o **SA** início de sessão não deve ser utilizado ou ativado. Se tem de ter um início de sessão do SQL Server, utilize uma das seguintes estratégias:

  - Criar uma conta SQL com um nome exclusivo que tenha **sysadmin** associação. Para fazer isto no portal, permitindo **autenticação SQL** durante o aprovisionamento.

    > [!TIP] 
    > Se não ativar a autenticação de SQL durante o aprovisionamento, tem de alterar manualmente o modo de autenticação para **do SQL Server e o modo de autenticação do Windows**. Para obter mais informações, consulte [Alterar modo de autenticação de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se tiver de utilizar o **SA** início de sessão, ativar o início de sessão após o aprovisionamento e atribuir uma nova palavra-passe segura.

## <a name="follow-on-premises-best-practices"></a>Siga as melhores práticas de no local

Para além das práticas descritas neste tópico, recomendamos que analise e implementar as práticas de segurança tradicionais no local onde for aplicável. Para obter mais informações, consulte [considerações de segurança para uma instalação do SQL Server](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Passos Seguintes

Se também estiver interessado em melhores práticas em torno de desempenho, consulte o artigo [práticas recomendadas do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados com a executar o SQL Server em VMs do Azure, consulte [SQL Server em Virtual Machines do Azure descrição-geral](virtual-machines-windows-sql-server-iaas-overview.md).

