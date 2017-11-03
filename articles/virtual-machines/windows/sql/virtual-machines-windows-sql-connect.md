---
title: "Ligar a uma máquina de Virtual do SQL Server (Resource Manager) | Microsoft Docs"
description: "Saiba como ligar ao SQL Server em execução numa máquina Virtual no Azure. Este tópico utiliza o modelo de implementação clássica. Os cenários diferem consoante a configuração de rede e a localização do cliente."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/14/2017
ms.author: jroth
ms.openlocfilehash: 67ba43f9456bbeffbf602067586143c4c68af672
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Ligar a uma Máquina Virtual do SQL Server no Azure (Gestor de Recursos)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Descrição geral

Este tópico descreve como ligar à sua instância do SQL Server em execução numa máquina virtual do Azure. Abrange alguns [cenários de conectividade](#connection-scenarios) e, em seguida, fornece [detalhadas os passos para configurar a conectividade do SQL Server numa VM do Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Se em vez disso, deverá dispor uma passagem de aprovisionamento e conectividade completa, consulte o artigo [aprovisionamento de uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Cenários de ligação

A forma como um cliente liga ao SQL Server em execução numa máquina Virtual é diferente consoante a localização do cliente e a configuração de rede.

Se aprovisionar uma VM do SQL Server no portal do Azure, terá a opção de especificar o tipo de **conectividade do SQL**.

![Opção de conectividade do SQL Server pública durante o aprovisionamento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

As opções de conectividade incluem:

| Opção | Descrição |
|---|---|
| **Público** | Ligar ao SQL Server através da internet |
| **Privada** | Ligar ao SQL Server na mesma rede virtual |
| **Local** | Ligar ao SQL Server localmente na mesma máquina virtual | 

As secções seguintes explicam o **pública** e **privada** opções mais detalhadamente.

## <a name="connect-to-sql-server-over-the-internet"></a>Ligar ao SQL Server através da Internet

Se pretender ligar ao seu motor de base de dados do SQL Server a partir da Internet, selecione **pública** para o **conectividade do SQL** tipo no portal do durante o aprovisionamento. O portal faz automaticamente os seguintes passos:

* Permite que o protocolo de TCP/IP para o SQL Server.
* Configura uma regra de firewall para abrir a porta de TCP do SQL Server (a predefinição é 1433).
* Permite a autenticação de servidor de SQL, necessário para acesso público.
* Configura o grupo de segurança de rede na VM para todo o tráfego TCP na porta do SQL Server.

> [!IMPORTANT]
> As imagens de máquina virtual para o programador do servidor de SQL e edições Express não ativar automaticamente o protocolo de TCP/IP. Para programadores e rápida edições, tem de utilizar o Gestor de configuração do SQL Server para [ative manualmente o protocolo de TCP/IP](#manualtcp) depois de criar a VM.

Qualquer cliente com acesso à internet pode ligar à instância do SQL Server, especificando o endereço IP público da máquina virtual ou qualquer etiqueta de DNS atribuídos a esse endereço IP. Se a porta do SQL Server é 1433, não é necessário especificá-la na cadeia de ligação. A seguinte cadeia de ligação se liga a uma VM do SQL Server com uma etiqueta de DNS de `sqlvmlabel.eastus.cloudapp.azure.com` utilizando a autenticação de SQL (também pode utilizar o endereço IP público).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Apesar de isto ativa a conectividade de clientes através da internet, isto implica que qualquer pessoa pode ligar ao SQL Server. Fora de clientes tem o nome de utilizador correto e a palavra-passe. No entanto, para segurança adicional, pode evitar a conhecidos porta 1433. Por exemplo, se tiver configurado o servidor de SQL para escutar numa porta 1500 e estabelecida firewall adequada e regras de grupo de segurança de rede, pode ligar, acrescentando o número de porta para o nome do servidor. O exemplo seguinte altera o um anterior ao adicionar um número de porta personalizada, **1500**, para o nome do servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando consultar o SQL Server numa VM através da internet, todos os dados de saída do datacenter do Azure está sujeita a normal [preços em transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Ligar ao SQL Server dentro de uma rede virtual

Quando escolhe **privada** para o **conectividade do SQL** tipo no portal do Azure configura a maioria das definições idênticas ao **pública**. A uma diferença é que não há nenhuma regra de grupo de segurança de rede para permitir tráfego externo na porta do SQL Server (a predefinição é 1433).

> [!IMPORTANT]
> As imagens de máquina virtual para o programador do servidor de SQL e edições Express não ativar automaticamente o protocolo de TCP/IP. Para programadores e rápida edições, tem de utilizar o Gestor de configuração do SQL Server para [ative manualmente o protocolo de TCP/IP](#manualtcp) depois de criar a VM.

Conectividade privada, muitas vezes, é utilizada em conjunto com [rede Virtual](../../../virtual-network/virtual-networks-overview.md), que permite que vários cenários. Pode ligar as VMs na mesma rede virtual, mesmo se existem dessas VMs em grupos de recursos diferentes. E com um [VPN site a site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), pode criar uma arquitetura de híbridos que liga as VMs com redes no local e as máquinas.

Redes virtuais também permitem-lhe associar as suas VMs do Azure a um domínio. Esta é a única forma de utilizar a autenticação do Windows para o SQL Server. Outros cenários de ligação exigem a autenticação de SQL com os nomes de utilizador e palavras-passe.

Partindo do princípio que tiver configurado o DNS na sua rede virtual, pode ligar à sua instância do SQL Server, especificando o nome do computador de VM do SQL Server na cadeia de ligação. O exemplo seguinte também assume que a autenticação do Windows também foi configurada e que o utilizador foi concedido acesso a instância do SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a>Alterar as definições de conectividade do SQL Server

Pode alterar as definições de conectividade para a máquina virtual do SQL Server no portal do Azure.

1. No portal do Azure, selecione **máquinas virtuais**.

2. Selecione o seu servidor SQL VM.

3. Em **definições**, clique em **configuração do SQL Server**.

4. Alterar o **o nível de conectividade do SQL Server** para a definição necessária. Opcionalmente, pode utilizar esta área para alterar a porta do SQL Server ou as definições de autenticação do SQL Server.

   ![Alterar a conectividade do SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Aguarde alguns minutos até a atualização concluir.

   ![Notificação de atualização da VM do SQL Server](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a>Ative TCP/IP para edições de programador e o Express

Se alterar as definições de conectividade do SQL Server, Azure automaticamente ative o protocolo de TCP/IP para programadores do SQL Server e edições Express. Os passos abaixo explicam como ativar manualmente o TCP/IP para que consiga ligar remotamente através de um endereço IP.

Em primeiro lugar, ligue a máquina do SQL Server com o ambiente de trabalho remoto.

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Em seguida, ative o protocolo de TCP/IP com **Gestor de configuração do SQL Server**.

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Ligar com o SSMS

Os passos seguintes mostram como criar uma etiqueta de DNS opcional para a VM do Azure e, em seguida, ligue-se com o SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Passos Seguintes

Para ver instruções juntamente com estes passos de conectividade de aprovisionamento, consulte [aprovisionamento de uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados com a executar o SQL Server em VMs do Azure, consulte [do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).