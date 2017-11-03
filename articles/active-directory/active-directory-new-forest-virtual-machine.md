---
title: Instalar uma floresta do Active Directory numa rede virtual do Azure | Microsoft Docs
description: "Um tutorial que explica como criar uma nova floresta do Active Directory numa máquina virtual (VM) numa rede Virtual do Azure."
services: active-directory, virtual-network
keywords: "máquinas de virtuais do Active Directory, floresta do Active Directory de instalação, vídeos do azure Active Directory "
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
tags: 
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: joflore
ms.openlocfilehash: 0a45a563d8aed45dd30cc76a13b0e197c248be84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Instalar uma nova floresta do Active Directory numa rede virtual do Azure
Este tópico mostra como criar um novo ambiente do Windows Server Active Directory numa rede virtual do Azure numa máquina virtual (VM) num [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Neste caso, a rede virtual do Azure não está ligada a uma rede no local.

Poderá também estar interessado nos seguintes tópicos relacionados:

* Para obter um vídeo que mostra estes passos, consulte [como instalar uma nova floresta do Active Directory numa rede virtual do Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* Pode, opcionalmente, [configurar uma VPN de site para site](../vpn-gateway/vpn-gateway-site-to-site-create.md) e, em seguida, instalar uma nova floresta ou expandir uma floresta no local a uma rede virtual do Azure. Para esses passos, consulte [instalar um controlador de domínio do Active Directory réplica numa rede Virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md).
* Para obter orientações conceptual sobre a instalação de serviços de domínio do Active Directory (AD DS) numa rede virtual do Azure, consulte [diretrizes para implementar o Windows Server Active Directory em Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama do cenário
Neste cenário, os utilizadores externos tem de aceder a aplicações que são executadas em servidores associados a um domínio. As VMs que executam os servidores de aplicações e as VMs que executam os controladores de domínio são instaladas instalado no seu próprio serviço em nuvem numa rede virtual do Azure. Também estão incluídas dentro de um conjunto de disponibilidade para tolerância a falhas melhorada.

![Floresta do Active Directory nas máquinas virtuais na rede Virtual do Azure ][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>Como isto diferem no local?
Não é muito diferença entre a instalar um controlador de domínio no Azure versus no local. As principais diferenças são listadas na seguinte tabela.

| Para configurar... | Local | Rede virtual do Azure |
| --- | --- | --- |
| **Endereço IP para o controlador de domínio** |Atribuir o endereço IP estático nas propriedades do adaptador de rede |Execute o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático |
| **Resolução de cliente DNS** |Definir o endereço do servidor DNS alternativo e preferencial na rede propriedades da placa de membros de domínio |Definir o endereço do servidor DNS nas propriedades de rede virtual |
| **Armazenamento de base de dados do Active Directory** |Opcionalmente, altere a localização de armazenamento predefinida de C:\ |Terá de alterar a localização de armazenamento predefinida de C:\ |

## <a name="create-an-azure-virtual-network"></a>Criar uma Azure virtual network
1. Inicie sessão no Portal Clássico do Azure.
2. Crie uma rede virtual Clique em **redes** > **criar uma rede virtual**. Utilize os valores na tabela seguinte para concluir o assistente.

   | Na página do assistente... | Especificar estes valores |
   | --- | --- |
   |  **Detalhes de rede virtual** |<p>Nome: Introduza um nome para a rede virtual</p><p>Região: Escolha a região mais próxima</p> |
   |  **DNS e VPN** |<p>Servidor DNS, deixar em branco</p><p>Não selecione uma opção de VPN</p> |
   |  **Espaços de endereços de rede virtual** |<p>Nome da sub-rede: introduza um nome para a sub-rede</p><p>A iniciar IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Criar as VMs para executar o controlador de domínio e funções de servidor DNS
Repita os passos seguintes para criar as VMs para alojar a função de DC conforme necessário. Deve implementar, pelo menos, dois controladores de domínio virtuais para fornecer tolerância a falhas e redundância. Se a rede virtual do Azure inclui, pelo menos, dois controladores de domínio que estão configurados da mesma forma (ou seja, são ambas as GCs, servidor DNS executar, e não contém qualquer função FSMO e assim sucessivamente), em seguida, colocar as VMs que são executados os DCs num conjunto de disponibilidade para tolerância a falhas melhorada.

Para criar as VMs através do Windows PowerShell em vez da IU, consulte [utilize o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. No portal clássico, clique em **novo** > **computação** > **Máquina Virtual** > **da galeria**. Utilize os seguintes valores para concluir o assistente. Aceite o valor predefinido para uma definição, a menos que outro valor é sugerido ou necessário.

   | Na página do assistente... | Especificar estes valores |
   | --- | --- |
   |  **Escolha uma imagem** |Windows Server 2012 R2 Datacenter |
   |  **Configuração da máquina virtual** |<p>Nome da máquina virtual: Escreva um nome de etiqueta única (por exemplo, AzureDC1).</p><p>Novo nome de utilizador: Escreva o nome de um utilizador. Este utilizador será um membro do grupo Administradores local na VM. Precisará para iniciar sessão para a VM pela primeira vez. A conta incorporada com o nome de administrador não funcionará.</p><p>Nova palavra-passe/confirmar: Escreva uma palavra-passe</p> |
   |  **Configuração da máquina virtual** |<p>O serviço em nuvem: Escolha <b>criar um novo serviço de nuvem</b> para a primeira VM e selecione esse nome de serviço em nuvem mesmo quando criar mais VMs que irá alojar a função de DC.</p><p>Nome DNS do serviço de nuvem: Especifique um nome globalmente exclusivo</p><p>Região/grupo de afinidade/rede Virtual: Especifique o nome de rede virtual (por exemplo, WestUSVNet).</p><p>Conta de armazenamento: Escolha <b>utilizar uma conta de armazenamento gerado automaticamente</b> para a primeira VM e, em seguida, selecione esse nome de conta de armazenamento mesmo quando criar mais VMs que irá alojar a função de DC.</p><p>Conjunto de disponibilidade: Escolher <b>criar um conjunto de disponibilidade</b>.</p><p>Nome do conjunto de disponibilidade: escreva um nome para o conjunto de disponibilidade quando criar a VM primeiro e, em seguida, selecione se o mesmo nome quando criar as VMs mais.</p> |
   |  **Configuração da máquina virtual** |<p>Selecione <b>instale o agente VM</b> e quaisquer outras extensões precisa.</p> |
2. Ligar um disco para cada VM que irão executar a função de servidor DC. O disco adicional é necessário para armazenar a base de dados, os registos e SYSVOL do AD. Especifique um tamanho para o disco (por exemplo, 10 GB) e deixar o **preferência de Cache do anfitrião** definido como **nenhum**. Para obter os passos, consulte [como anexar um disco de dados para uma Máquina Virtual do Windows](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Depois de a sessão pela primeira vez para a VM, abra **Gestor de servidor** > **File and Storage Services** para criar um volume neste disco utilizando o NTFS.
4. Reserve um endereço IP estático para VMs com a função de DC. Para reservar um endereço IP estático, transferir o instalador de plataforma Web Microsoft e [instalar o Azure PowerShell](/powershell/azure/overview) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

Para obter mais informações sobre como definir um endereço IP estático, consulte [configurar um endereço de IP estático interno para uma VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Instalar o Windows Server Active Directory
Utilizar a mesma rotina para [instalar o AD DS](https://technet.microsoft.com/library/jj574166.aspx) que utilizam no local (ou seja, pode utilizar a IU, um ficheiro de resposta ou o Windows PowerShell). Tem de fornecer credenciais de administrador para instalar uma nova floresta. Para especificar a localização para a base de dados do Active Directory, os registos e SYSVOL, altere a localização de armazenamento predefinida da unidade do sistema operativo para o disco de dados adicionais ligados à VM.

Depois de concluída a instalação de DC, ligar à VM novamente e inicie sessão no DC. Lembre-se especificar as credenciais do domínio.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Repor o servidor DNS para a rede virtual do Azure
1. Repor a definição de reencaminhador DNS no novo servidor DC/DNS.
   1. No Gestor de servidor, clique em **ferramentas** > **DNS**.
   2. No **Gestor de DNS**, o nome do servidor DNS com o botão direito e clique em **propriedades**.
   3. No **reencaminhadores** separador, clique no endereço IP do reencaminhador e clique em **editar**.  Selecione o endereço IP e clique em **eliminar**.
   4. Clique em **OK** para fechar o editor e **Ok** novamente para fechar as propriedades do servidor DNS.
2. Atualize a definição do servidor DNS para a rede virtual.
   1. Clique em **redes virtuais** > faça duplo clique a rede virtual que criou > **configurar** > **servidores DNS**, escreva o nome e o DIP de uma das VMs que executa a função de servidor DC/DNS e clique em **guardar**.
   2. Selecione a VM e clique em **reiniciar** para acionar a VM para configurar definições de resolução DNS com o endereço IP do novo servidor DNS.

## <a name="create-vms-for-domain-members"></a>Criar as VMs para membros do domínio
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

## <a name="see-also"></a>Veja Também
* [Como instalar uma nova floresta do Active Directory numa rede virtual do Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Diretrizes para a implementação do Windows Server Active Directory em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Configurar uma VPN de Site a Site](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Instalar um controlador de domínio do Active Directory réplica numa rede virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: Noções básicas de (01) Máquina Virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e conectividade em vários locais](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Definir o endereço IP estático de VM do Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Como atribuir o IP estático a VM do Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [Instalar uma nova floresta de diretório do Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
* [Introdução à virtualização de serviços (AD DS) de domínio do Active Directory (nível 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
