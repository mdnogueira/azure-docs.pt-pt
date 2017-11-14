---
title: "Instalar controladores de domínio de réplica para o domínio do Active Directory no local em máquinas virtuais do Azure | Microsoft Docs"
description: "Como instalar o DC de réplica para um domínio do Active Directory no local em máquinas de virtuais (VMs) do Azure numa rede virtual do Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 60839f93954bfe38f0346b235259f68e479b8a00
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalar um controlador de domínio do Active Directory de réplica numa rede virtual do Azure
Este artigo descreve como instalar controladores de domínio adicional (DCs) a utilizar como réplica DCs de um domínio do Active Directory no local em máquinas de virtuais (VMs) do Azure numa rede virtual do Azure. Também pode [instalar uma floresta do Active Directory do Windows Server numa rede virtual do Azure](active-directory-new-forest-virtual-machine.md). Para instalar serviços de domínio do Active Directory (AD DS) numa rede virtual do Azure, consulte [diretrizes para implementar o Windows Server Active Directory em Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama do cenário
Neste cenário, os utilizadores externos tem de aceder a aplicações que são executadas em servidores associados a um domínio. As VMs que executam os servidores de aplicações e os controladores de domínio de réplica estão instalados numa rede virtual do Azure. A rede virtual pode ser ligada à rede no local pelo [ExpressRoute](../expressroute/expressroute-locations-providers.md), ou pode utilizar um [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) ligação, conforme mostrado: 

![Controlador de domínio do Active Directory de réplica de pf de diagrama uma vnet do Azure][1]

Os servidores de aplicações e os controladores de domínio são implementadas nos serviços de cloud separado para distribuir o processamento de computação e dentro de conjuntos de disponibilidade para tolerância a falhas melhorada.
Os controladores de domínio replicar entre si e com os controladores de domínio no local utilizando a replicação do Active Directory. Sem as ferramentas de sincronização são necessárias.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Criar um site do Active Directory no local para a rede virtual do Azure
Pode criar um site no Active Directory que representa a região de rede correspondente à rede virtual. Este site pode ajudá-lo a otimizar a autenticação, a replicação e outras operações de localização de DC. Os passos seguintes explicam como criar um site e para obter mais em segundo plano, consulte [adicionar um novo Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra serviços e locais do Active Directory: **Gestor de servidor** > **ferramentas** > **serviços e locais do Active Directory**.
2. Criar um site para representar a região onde criou uma Azure virtual network: clique em **Sites** > **ação** > **novo site** > tipo a nome do novo site, tais como o Azure dos EUA oeste > Selecione uma ligação de site > **OK**.
3. Crie uma sub-rede e associar o novo site: faça duplo clique em **Sites** > com o botão direito **sub-redes** > **nova sub-rede** > escreva o intervalo de endereços IP a rede virtual (por exemplo, 10.1.0.0/16 no diagrama cenário) > selecione o novo site do Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Criar uma Azure virtual network
Para criar uma Azure virtual network e configurar a VPN site a site, siga os passos incluídos no artigo [criar uma ligação Site a Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

Também pode configurar o gateway de rede virtual para criar uma ligação de VPN de site a site segura. Crie a ligação de VPN de site a site entre a nova rede virtual e um dispositivo VPN no local. Para obter instruções, consulte [configurar um Gateway de rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>Criar as VMs do Azure para as funções de DC
Para criar as VMs para alojar a função do DC, repita os passos [criar máquina virtual do Windows com o portal do Azure](../virtual-machines/windows/quick-create-portal.md) conforme necessário. Implemente, pelo menos, dois controladores de domínio virtuais para fornecer tolerância a falhas e redundância. Se a rede virtual do Azure tem, pelo menos, dois controladores de domínio que estão configurados da mesma forma, pode colocar as VMs que são executados os DCs num conjunto de disponibilidade para tolerância a falhas melhorada.

Para criar as VMs através do Windows PowerShell em vez do portal do Azure, consulte [utilize o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Reserve um endereço IP estático para VMs com a função de DC. Para reservar um endereço IP estático, transferir o instalador de plataforma Web Microsoft e [instalar o Azure PowerShell](/powershell/azure/overview) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Para obter mais informações sobre como definir um endereço IP estático, consulte [configurar um endereço IP estático interno para uma VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instalar o AD DS em VMs do Azure
Inicie sessão para uma VM e verifique se tem conectividade entre a site para site VPN ou ExpressRoute ligação aos recursos na sua rede no local. Em seguida, instale o AD DS nas VMs do Azure. Pode utilizar o mesmo processo que utilizar para instalar um DC adicional na sua rede no local (IU, do Windows PowerShell ou um ficheiro de resposta). Como instalar o AD DS, certifique-se de que especifique o novo volume para a localização da base de dados AD, os registos e SYSVOL. Se precisar de um atualizador na instalação do AD DS, consulte [instalar o Active Directory Domain Services (nível 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [instalar um controlador de domínio do réplica do Windows Server 2012 num domínio existente (Nível 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigurar o servidor DNS para a rede virtual
1. Para obter uma lista de nomes de rede virtual, além de [portal do Azure](https://portal.azure.com), procure *redes virtuais*, em seguida, selecione **redes virtuais** para ver a lista. 
2. Abra a rede virtual que pretende gerir, e, em seguida, [reconfigurar os endereços de IP do servidor DNS para a rede virtual](../virtual-network/virtual-network-manage-network.md#dns-servers) para utilizar os endereços IP estáticos atribuídos para a DC de réplica em vez dos endereços IP para servidores DNS no local.
3. Para se certificar de que todas as réplicas DC VMs na rede virtual estão configurados com para utilizar servidores DNS na rede virtual:
  1. Selecione **máquinas virtuais**.
  2. Selecione as VMs e, em seguida, selecione **reiniciar**. 
  3. Aguarde até que a VM seja **executar** novamente e, em seguida, inicie sessão no mesmo.

## <a name="create-vms-for-application-servers"></a>Criar as VMs para servidores de aplicações

Para criar as VMs para alojar a função de servidor de aplicações, repita os passos [criar máquina virtual do Windows com o portal do Azure](../virtual-machines/windows/quick-create-portal.md) conforme necessário. Para criar as VMs através do PowerShell da Microsoft em vez do portal do Azure, consulte [utilize o Azure PowerShell para criar e configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). A tabela seguinte contém definições sugeridas.

| Definição | Valores |
| --- | --- |
|  **Escolha uma imagem** | Windows Server 2012 R2 Datacenter |
|  **Configuração da máquina virtual** |<p>Nome da máquina virtual: Escreva um nome de etiqueta única (por exemplo, AppServer1).</p><p>Novo nome de utilizador: Escreva o nome de um utilizador. Este utilizador será um membro do grupo Administradores local na VM. Precisará para iniciar sessão para a VM pela primeira vez. A conta incorporada com o nome de administrador não funcionará.</p><p>Nova palavra-passe/confirmar: Escreva uma palavra-passe</p> |
|  **Configuração da máquina virtual** |<p>O serviço em nuvem: Escolha **criar um novo serviço de nuvem** para a primeira VM e selecione esse nome de serviço em nuvem mesmo quando criar mais VMs que irá alojar a aplicação.</p><p>Nome DNS do serviço de nuvem: Especifique um nome globalmente exclusivo</p><p>Região/grupo de afinidade/rede Virtual: Especifique o nome de rede virtual (por exemplo, WestUSVNet).</p><p>Conta de armazenamento: Escolha **utilizar uma conta de armazenamento gerado automaticamente** para a primeira VM e, em seguida, selecione esse nome de conta de armazenamento mesmo quando criar mais VMs que irá alojar a aplicação.</p><p>Conjunto de disponibilidade: Escolher **criar um conjunto de disponibilidade**.</p><p>Nome do conjunto de disponibilidade: escreva um nome para o conjunto de disponibilidade quando criar a VM primeiro e, em seguida, selecione se o mesmo nome quando criar as VMs mais.</p> |
|  **Configuração da máquina virtual** |<p>Selecione <b>instale o agente VM</b> e quaisquer outras extensões precisa.</p> |
  
Depois de cada VM é aprovisionado, inicie sessão e associar ao domínio. 
1. No **Gestor de servidor** &gt; **servidor Local** &gt; **WORKGROUP** &gt; **alterar...** , selecione **domínio**.
2. Introduza o nome do seu domínio no local. 
3. Forneça credenciais de utilizador do domínio.
4. Reinicie a VM.

## <a name="additional-resources"></a>Recursos adicionais

* Para obter mais informações sobre como utilizar o Windows PowerShell, consulte [introdução aos Cmdlets do Azure](/powershell/azure/overview) e [referência de cmdlets do Azure](/powershell/azure/get-started-azureps).
* [Diretrizes para a implementação do Windows Server Active Directory em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Como carregar controladores de domínio de Hyper-V no local existentes para o Azure utilizando o Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Instalar uma nova floresta do Active Directory numa rede virtual do Azure](active-directory-new-forest-virtual-machine.md)
* [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IT Pro IaaS: Noções básicas de (01) Máquina Virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e conectividade em vários locais](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Cmdlets de gestão do Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
