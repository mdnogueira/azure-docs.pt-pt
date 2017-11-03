---
title: "Instalar um controlador de domínio do Active Directory de réplica no Azure | Microsoft Docs"
description: "Um tutorial que explica como instalar um controlador de domínio numa floresta do Active Directory no local numa máquina virtual do Azure."
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
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: fb0bacac346445e6bde9df22f3355419e3162a3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalar um controlador de domínio do Active Directory de réplica numa rede virtual do Azure
Este tópico mostra como instalar controladores de domínio adicionais (também conhecido como a réplica DCs) para um domínio do Active Directory no local em máquinas virtuais do Azure (VMs) numa rede virtual do Azure.

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo.

Poderá também estar interessado nos seguintes tópicos relacionados:

* Opcionalmente, pode instalar uma nova floresta do Active Directory numa rede virtual do Azure. Para esses passos, consulte [instalar uma nova floresta do Active Directory numa rede virtual do Azure](active-directory-new-forest-virtual-machine.md).
* Para obter orientações conceptual sobre a instalação de serviços de domínio do Active Directory (AD DS) numa rede virtual do Azure, consulte [diretrizes para implementar o Windows Server Active Directory em Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama do cenário
Neste cenário, os utilizadores externos tem de aceder a aplicações que são executadas em servidores associados a um domínio. As VMs que executam os servidores de aplicações e os controladores de domínio de réplica estão instalados numa rede virtual do Azure. A rede virtual pode ser ligada à rede no local por um [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) ligação, conforme mostrado no diagrama seguinte ou pode utilizar [ExpressRoute](../expressroute/expressroute-locations-providers.md) para uma ligação mais rápida.

Os servidores de aplicações e os controladores de domínio são implementadas nos serviços de cloud separado para distribuir o processamento de computação e dentro de [conjuntos de disponibilidade](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para tolerância a falhas melhorada.
Os controladores de domínio replicar entre si e com os controladores de domínio no local utilizando a replicação do Active Directory. Sem as ferramentas de sincronização são necessárias.

![Controlador de domínio do Active Directory de réplica de pf de diagrama uma vnet do Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Criar um site do Active Directory para a rede virtual do Azure
É uma boa ideia para criar um site no Active Directory que representa a região de rede correspondente à rede virtual. Que ajuda a otimizar a autenticação, a replicação e outras operações de localização de DC. Os passos seguintes explicam como criar um site e para obter mais em segundo plano, consulte [adicionar um novo Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra serviços e locais do Active Directory: **Gestor de servidor** > **ferramentas** > **serviços e locais do Active Directory**.
2. Criar um site para representar a região onde criou uma Azure virtual network: clique em **Sites** > **ação** > **novo site** > tipo a nome do novo site, tais como o Azure dos EUA oeste > Selecione uma ligação de site > **OK**.
3. Crie uma sub-rede e associar o novo site: faça duplo clique em **Sites** > com o botão direito **sub-redes** > **nova sub-rede** > escreva o intervalo de endereços IP a rede virtual (por exemplo, 10.1.0.0/16 no diagrama cenário) > selecione o novo site do Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Criar uma Azure virtual network
1. No [portal clássico do Azure](https://manage.windowsazure.com), clique em **novo** > **serviços de rede** > **rede Virtual**  >  **Criação personalizada** e utilize os seguintes valores para concluir o assistente.

   | Na página do assistente... | Especificar estes valores |
   | --- | --- |
   |  **Detalhes de rede virtual** |<p>Nome: Escreva um nome para a rede virtual, tais como WestUSVNet.</p><p>Região: Escolha a região mais próxima.</p> |
   |  **Conectividade DNS e VPN** |<p>Servidores DNS: Especifique o nome e endereço IP de um ou mais servidores DNS no local.</p><p>Conectividade: Selecione **configurar uma VPN de site para site**.</p><p>Rede local: Especifique uma nova rede local.</p><p>Se estiver a utilizar o ExpressRoute, em vez de uma VPN, consulte o artigo [configurar uma ligação ExpressRoute através de um fornecedor de Exchange](../expressroute/expressroute-locations-providers.md).</p> |
   |  **Conetividade site a site** |<p>Nome: Escreva um nome para a rede no local.</p><p>Endereço IP do dispositivo VPN: Especifique o endereço IP público do dispositivo que irão ligar à rede virtual. O dispositivo VPN não pode estar localizado atrás de um NAT.</p><p>Endereço: Especifique os intervalos de endereços da rede no local (por exemplo, 192.168.0.0/16 no diagrama cenário).</p> |
   |  **Espaços de endereços de rede virtual** |<p>Espaço de endereços: Especifique o intervalo de endereços IP para VMs que pretende executar na rede virtual do Azure (por exemplo, 10.1.0.0/16 no diagrama cenário). Este intervalo de endereços não pode sobrepor-se com os intervalos de endereços da rede no local.</p><p>Sub-redes: Especifique um nome e endereço para uma sub-rede para servidores de aplicações (como front-end, 10.1.1.0/24) e para os controladores de domínio (por exemplo, o back-end, 10.1.2.0/24).</p><p>Clique em **adicionar sub-rede do gateway**.</p> |
2. Em seguida, irá configurar o gateway de rede virtual para criar uma ligação de VPN de site a site segura. Consulte [configurar um Gateway de rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para as instruções.
3. Crie a ligação de VPN de site a site entre a nova rede virtual e um dispositivo VPN no local. Consulte [configurar um Gateway de rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para as instruções.

## <a name="create-azure-vms-for-the-dc-roles"></a>Criar as VMs do Azure para as funções de DC
Repita os passos seguintes para criar as VMs para alojar a função de DC conforme necessário. Deve implementar, pelo menos, dois controladores de domínio virtuais para fornecer tolerância a falhas e redundância. Se a rede virtual do Azure inclui, pelo menos, dois controladores de domínio que estão configurados da mesma forma (ou seja, são ambas as GCs, servidor DNS executar, e não contém qualquer função FSMO e assim sucessivamente), em seguida, colocar as VMs que são executados os DCs num conjunto de disponibilidade para tolerância a falhas melhorada.
Para criar as VMs através do Windows PowerShell em vez da IU, consulte [utilize o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. No [portal clássico do Azure](https://manage.windowsazure.com), clique em **novo** > **computação** > **Máquina Virtual**  >  **Da galeria**. Utilize os seguintes valores para concluir o assistente. Aceite o valor predefinido para uma definição, a menos que outro valor é sugerido ou necessário.

   | Na página do assistente... | Especificar estes valores |
   | --- | --- |
   |  **Escolha uma imagem** |Windows Server 2012 R2 Datacenter |
   |  **Configuração da máquina virtual** |<p>Nome da máquina virtual: Escreva um nome de etiqueta única (por exemplo, AzureDC1).</p><p>Novo nome de utilizador: Escreva o nome de um utilizador. Este utilizador será um membro do grupo Administradores local na VM. Precisará para iniciar sessão para a VM pela primeira vez. A conta incorporada com o nome de administrador não funcionará.</p><p>Nova palavra-passe/confirmar: Escreva uma palavra-passe</p> |
   |  **Configuração da máquina virtual** |<p>O serviço em nuvem: Escolha <b>criar um novo serviço de nuvem</b> para a primeira VM e selecione esse nome de serviço em nuvem mesmo quando criar mais VMs que irá alojar a função de DC.</p><p>Nome DNS do serviço de nuvem: Especifique um nome globalmente exclusivo</p><p>Região/grupo de afinidade/rede Virtual: Especifique o nome de rede virtual (por exemplo, WestUSVNet).</p><p>Conta de armazenamento: Escolha <b>utilizar uma conta de armazenamento gerado automaticamente</b> para a primeira VM e, em seguida, selecione esse nome de conta de armazenamento mesmo quando criar mais VMs que irá alojar a função de DC.</p><p>Conjunto de disponibilidade: Escolher <b>criar um conjunto de disponibilidade</b>.</p><p>Nome do conjunto de disponibilidade: escreva um nome para o conjunto de disponibilidade quando criar a VM primeiro e, em seguida, selecione se o mesmo nome quando criar as VMs mais.</p> |
   |  **Configuração da máquina virtual** |<p>Selecione <b>instale o agente VM</b> e quaisquer outras extensões precisa.</p> |
2. Ligar um disco para cada VM que irão executar a função de servidor DC. O disco adicional é necessário para armazenar a base de dados, os registos e SYSVOL do AD. Especifique um tamanho para o disco (por exemplo, 10 GB) e deixar o **preferência de Cache do anfitrião** definido como **nenhum**. Para obter os passos, consulte [como anexar um disco de dados para uma Máquina Virtual do Windows](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Depois de a sessão pela primeira vez para a VM, abra **Gestor de servidor** > **File and Storage Services** para criar um volume neste disco utilizando o NTFS.
4. Reserve um endereço IP estático para VMs com a função de DC. Para reservar um endereço IP estático, transferir o instalador de plataforma Web Microsoft e [instalar o Azure PowerShell](/powershell/azure/overview) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

    ' Get-AzureVM - ServiceName AzureDC1-nome AzureDC1 | Conjunto AzureStaticVNetIP - IPAddress 10.0.0.4 | Update-AzureVM

Para obter mais informações sobre como definir um endereço IP estático, consulte [configurar um endereço de IP estático interno para uma VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instalar o AD DS em VMs do Azure
Inicie sessão para uma VM e verifique se tem conectividade entre a site para site VPN ou ExpressRoute ligação aos recursos na sua rede no local. Em seguida, instale o AD DS nas VMs do Azure. Pode utilizar o mesmo processo que utilizar para instalar um DC adicional na sua rede no local (IU, do Windows PowerShell ou um ficheiro de resposta). Como instalar o AD DS, certifique-se de que especifique o novo volume para a localização da base de dados AD, os registos e SYSVOL. Se precisar de um atualizador na instalação do AD DS, consulte [instalar o Active Directory Domain Services (nível 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [instalar um controlador de domínio do réplica do Windows Server 2012 num domínio existente (Nível 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigurar o servidor DNS para a rede virtual
1. No [portal do Azure](https://portal.azure.com), no **procurar recursos** box, introduza *redes virtuais*, em seguida, clique em **redes virtuais (clássicas)** no resultados da pesquisa. Clique no nome da rede virtual e, em seguida, [reconfigurar os endereços de IP do servidor DNS para a rede virtual](../virtual-network/virtual-network-manage-network.md#dns-servers) para utilizar os endereços IP estáticos atribuídos à réplica DCs em vez dos endereços IP dos servidores DNS no local.
2. Para garantir que todas as réplicas DC VMs na rede virtual estão configuradas com a utilizar servidores DNS na rede virtual, clique em **máquinas virtuais**, clique na coluna de estado para cada VM e, em seguida, clique em **reiniciar**. Aguarde até que a VM mostra **executar** estado antes de tentar iniciar sessão na mesma.

## <a name="create-vms-for-application-servers"></a>Criar as VMs para servidores de aplicações
1. Repita os passos seguintes para criar as VMs para ser executado como servidores de aplicações. Aceite o valor predefinido para uma definição, a menos que outro valor é sugerido ou necessário.

   | Na página do assistente... | Especificar estes valores |
   | --- | --- |
   |  **Escolha uma imagem** |Windows Server 2012 R2 Datacenter |
   |  **Configuração da máquina virtual** |<p>Nome da máquina virtual: Escreva um nome de etiqueta única (por exemplo, AppServer1).</p><p>Novo nome de utilizador: Escreva o nome de um utilizador. Este utilizador será um membro do grupo Administradores local na VM. Precisará para iniciar sessão para a VM pela primeira vez. A conta incorporada com o nome de administrador não funcionará.</p><p>Nova palavra-passe/confirmar: Escreva uma palavra-passe</p> |
   |  **Configuração da máquina virtual** |<p>O serviço em nuvem: Escolha **criar um novo serviço de nuvem** para a primeira VM e selecione esse nome de serviço em nuvem mesmo quando criar mais VMs que irá alojar a aplicação.</p><p>Nome DNS do serviço de nuvem: Especifique um nome globalmente exclusivo</p><p>Região/grupo de afinidade/rede Virtual: Especifique o nome de rede virtual (por exemplo, WestUSVNet).</p><p>Conta de armazenamento: Escolha **utilizar uma conta de armazenamento gerado automaticamente** para a primeira VM e, em seguida, selecione esse nome de conta de armazenamento mesmo quando criar mais VMs que irá alojar a aplicação.</p><p>Conjunto de disponibilidade: Escolher **criar um conjunto de disponibilidade**.</p><p>Nome do conjunto de disponibilidade: escreva um nome para o conjunto de disponibilidade quando criar a VM primeiro e, em seguida, selecione se o mesmo nome quando criar as VMs mais.</p> |
   |  **Configuração da máquina virtual** |<p>Selecione <b>instale o agente VM</b> e quaisquer outras extensões precisa.</p> |
2. Depois de cada VM é aprovisionado, inicie sessão e associar ao domínio. No **Gestor de servidor**, clique em **servidor Local** > **WORKGROUP** > **alterar...** e, em seguida, selecione **domínio** e escreva o nome do seu domínio no local. Forneça credenciais de um utilizador de domínio e, em seguida, reiniciar a VM para concluir a associação ao domínio.

Para criar as VMs através do Windows PowerShell em vez da IU, consulte [utilize o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Para obter mais informações sobre como utilizar o Windows PowerShell, consulte [introdução aos Cmdlets do Azure](/powershell/azure/overview) e [referência de cmdlets do Azure](/powershell/azure/get-started-azureps).

## <a name="additional-resources"></a>Recursos adicionais
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
