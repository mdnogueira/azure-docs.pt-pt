---
title: "Ligar a uma Máquina Virtual do SQL Server no Azure (clássica) | Microsoft Docs"
description: "Saiba como ligar ao SQL Server em execução numa máquina Virtual no Azure. Este tópico utiliza o modelo de implementação clássica. Os cenários diferem consoante a configuração de rede e a localização do cliente."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 4218b6d274abbeda542c1507aec998ba56f5c145
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Ligar a uma Máquina Virtual do SQL Server no Azure (Implementação Clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este tópico descreve como ligar à sua instância do SQL Server em execução numa máquina virtual do Azure. Abrange alguns [cenários de conectividade](#connection-scenarios) e, em seguida, fornece [detalhadas os passos para configurar a conectividade do SQL Server numa VM do Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Se estiver a utilizar as VMs do Gestor de recursos, consulte o artigo [ligar a um SQL Server Máquina Virtual no Azure com o Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Cenários de ligação
A forma como um cliente liga ao SQL Server em execução numa máquina Virtual é diferente consoante a localização do cliente e a configuração de máquina/redes. Estes cenários incluem:

* [Ligar ao SQL Server no mesmo serviço em nuvem](#connect-to-sql-server-in-the-same-cloud-service)
* [Ligar ao SQL Server através da internet](#connect-to-sql-server-over-the-internet)
* [Ligar ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Antes de ligar com qualquer um destes métodos, tem de seguir a [passos neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Ligar ao SQL Server no mesmo serviço em nuvem
Várias máquinas virtuais podem ser criadas no mesmo serviço em nuvem. Para compreender este cenário de máquinas virtuais, consulte [como ligar a máquinas virtuais com um serviço de nuvem ou de rede virtual](../classic/connect-vms.md#connect-vms-in-a-standalone-cloud-service). Este cenário ocorre quando um cliente na máquina virtual de um tenta estabelecer ligação ao SQL Server em execução outra máquina virtual no mesmo serviço em nuvem.

Neste cenário, pode ser ligados utilizando a VM **nome** (também são mostradas como **nome do computador** ou **hostname** no portal). Este é o nome fornecido para a VM durante a criação. Por exemplo, se denominar a VM do SQL Server **mysqlvm**, um cliente VM no mesmo serviço em nuvem foi possível utilizar a seguinte cadeia de ligação para ligar:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Ligar ao SQL Server através da Internet
Se pretender ligar ao seu motor de base de dados do SQL Server a partir da Internet, tem de criar um ponto final da máquina virtual para comunicação de TCP recebidas. Este passo de configuração do Azure direciona o tráfego da porta TCP recebido para uma porta TCP a que a máquina virtual tenha acesso.

Para ligar através da internet, tem de utilizar o nome DNS da VM e o número de porta de ponto final VM (configurado neste artigo). Para localizar o nome de DNS, navegue para o portal do Azure e selecione **máquinas virtuais (clássicas)**. Em seguida, selecione a máquina virtual. O **nome DNS** é apresentado no **descrição geral** secção.

Por exemplo, considere uma máquina virtual clássica chamada **mysqlvm** com um nome de DNS de **mysqlvm7777.cloudapp.net** e um ponto final de VM de **57500**. Partindo do princípio de conectividade está corretamente configurada, a seguinte cadeia de ligação pode ser utilizada para aceder a máquina virtual a partir de qualquer lugar na internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Apesar de isto ativa a conectividade de clientes através da internet, isto implica que qualquer pessoa pode ligar ao SQL Server. Fora de clientes tem o nome de utilizador correto e a palavra-passe. Para segurança adicional, não utilize a porta 1433 conhecida para o ponto final público máquina virtual. E se for possível, considere adicionar uma ACL no seu ponto final para restringir o tráfego apenas para os clientes permitir. Para obter instruções sobre a utilização de ACLs com pontos finais, consulte [gerir ACL num ponto final](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> É importante ter em atenção que, quando utilizar esta técnica para comunicar com o SQL Server, todos os dados de saída do datacenter do Azure está sujeita a normal [preços em transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Ligar ao SQL Server na mesma rede virtual
[Rede virtual](../../../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Pode ligar as VMs na mesma rede virtual, mesmo que essas VMs existem nos serviços de nuvem diferente. E com um [VPN site a site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), pode criar uma arquitetura de híbridos que liga as VMs com redes no local e as máquinas.

Redes virtuais também permite-lhe associar as suas VMs do Azure a um domínio. Esta é a única forma de utilizar a autenticação do Windows para o SQL Server. Outros cenários de ligação exigem a autenticação de SQL com os nomes de utilizador e palavras-passe.

Se pretender configurar um ambiente de domínio e a autenticação do Windows, não terá de utilizar os passos neste artigo para configurar o ponto final público ou a autenticação de SQL e inícios de sessão. Neste cenário, pode ligar à sua instância do SQL Server, especificando o nome da VM do SQL Server na cadeia de ligação. O exemplo seguinte parte do princípio de que a autenticação do Windows também foi configurada e que o utilizador foi concedido acesso a instância do SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Passos para configurar a conectividade do SQL Server numa VM do Azure
Os passos seguintes demonstram como ligar à instância do SQL Server através da internet utilizando o SQL Server Management Studio (SSMS). No entanto, os mesmos passos aplicam-se para tornar a máquina virtual do SQL Server acessíveis para as suas aplicações, em execução no local e no Azure.

Antes de poder ligar à instância do SQL Server a partir de outra VM ou da internet, tem de concluir as seguintes tarefas, tal como descrito nas secções que se seguem:

* [Criar um ponto final TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Portas TCP abertas na firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurar o SQL Server para escutar o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurar o SQL Server para a autenticação de modo misto](#configure-sql-server-for-mixed-mode-authentication)
* [Criar inícios de sessão de autenticação de SQL Server](#create-sql-server-authentication-logins)
* [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
* [Ligar ao motor de base de dados a partir de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de ligação é resumido pelo diagrama a seguir:

![Ligar a uma máquina virtual do SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Passos Seguintes
Se também estiver a planear utilizar grupos de Disponibilidade AlwaysOn para disponibilidade elevada e recuperação após desastre, deve considerar implementar um serviço de escuta. Os clientes de base de dados ligam ao serviço de escuta, em vez de diretamente a uma das instâncias do SQL Server. O serviço de escuta encaminha os clientes para a réplica primária do grupo de disponibilidade. Para obter mais informações, consulte [configurar um serviço de escuta do ILB para grupos de Disponibilidade AlwaysOn no Azure](../classic/ps-sql-int-listener.md).

É importante rever todas as melhores práticas de segurança para o SQL Server em execução numa máquina virtual do Azure. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).

[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure. 

Para outros tópicos relacionados com a executar o SQL Server em VMs do Azure, consulte [do SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

