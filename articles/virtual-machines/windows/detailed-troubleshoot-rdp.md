---
title: "Detalhadas ambiente de trabalho remoto, resolução de problemas no Azure | Microsoft Docs"
description: "Reveja os passos de resolução de problemas detalhados para erros de ambiente de trabalho remotos onde não é possível para máquinas virtuais do Windows no Azure"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: "Não é possível ligar ao ambiente de trabalho remoto, resolver problemas de ambiente de trabalho remoto, não é possível ligar o ambiente de trabalho remoto, erros de ambiente de trabalho remotos, a resolução de ambiente de trabalho remoto, problemas de ambiente de trabalho remotos"
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2017
ms.author: genli
ms.openlocfilehash: afbaa3afc78efd220d74def2e9f106e9fbd1ee2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Passos de resolução de problemas detalhados para problemas de ligação de ambiente de trabalho remoto para VMs do Windows no Azure
Este artigo fornece os passos detalhados de resolução de problemas para diagnosticar e corrigir os erros de ambiente de trabalho remoto complexos baseados em Windows máquinas virtuais do Azure.

> [!IMPORTANT]
> Para eliminar os erros mais comuns do ambiente de trabalho remoto, certifique-se de que lê [o artigo de resolução de problemas básico para ambiente de trabalho remoto](troubleshoot-rdp-connection.md) antes de continuar.

Pode encontrar um ambiente de trabalho remoto a mensagem de erro não assemelhar-se qualquer uma das mensagens de erro específicas abordada [o guia de resolução de problemas do ambiente de trabalho remoto básico](troubleshoot-rdp-connection.md). Siga estes passos para determinar por que motivo o cliente de ambiente de trabalho remoto (RDP) não é possível ligar ao serviço RDP na VM do Azure.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [do MSDN Azure e os fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode também ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**. Para informações sobre como utilizar o suporte do Azure, leia o [FAQ de suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma ligação de ambiente de trabalho remoto
Os seguintes componentes envolvidos numa ligação de RDP:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de continuar, poderá ser útil rever mentally que tenha sido alterado desde a última ligação de ambiente de trabalho remoto com êxito para a VM. Por exemplo:

* O endereço IP público de VM ou o serviço em nuvem que contém a VM (também denominado o endereço IP virtual [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) foi alterada. A falha RDP poderá ter ocorrido porque a cache do cliente DNS ainda tem o *endereço IP antigo* registado para o nome DNS. Esvaziar a cache do cliente DNS e tente ligar novamente a VM. Ou tente ligar-se diretamente com o novo VIP.
* Estão a utilizar uma aplicação de terceiros para gerir as suas ligações de ambiente de trabalho remoto em vez de utilizar a ligação gerada pelo portal do Azure. Certifique-se de que a configuração da aplicação inclui a porta TCP correta para o tráfego de ambiente de trabalho remoto. Pode verificar esta porta para uma máquina virtual clássica no [portal do Azure](https://portal.azure.com), clicando definições a VM > pontos finais.

## <a name="preliminary-steps"></a>Passos preliminares
Antes de avançar para a resolução de detalhado,

* Verifique o estado da máquina virtual no portal do Azure para quaisquer problemas óbvios.
* Siga o [quick fix passos para erros RDP comuns no guia de resolução de problemas básico](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Para imagens personalizadas, certifique-se de que o VHD é devidamente preparado antes de a carregá-la. Para obter mais informações, consulte [preparar um VHD do Windows ou o VHDX para carregar para o Azure](prepare-for-upload-vhd-image.md).


Tente restabelecer a ligação à VM através do ambiente de trabalho remoto após estes passos.

## <a name="detailed-troubleshooting-steps"></a>Passos detalhados de resolução de problemas
O cliente de ambiente de trabalho remoto pode não ser capaz de alcançar o serviço de ambiente de trabalho remoto na VM do Azure devido a problemas nas seguintes origens:

* [Computador cliente de ambiente de trabalho remoto](#source-1-remote-desktop-client-computer)
* [Dispositivo de limite de intranet de organização](#source-2-organization-intranet-edge-device)
* [Ponto final do serviço em nuvem e de acesso (ACL) de lista de controlo](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [VM do Azure baseados em Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Origem 1: Computador de cliente de ambiente de trabalho remoto
Certifique-se de que o computador pode efetuar ligações de ambiente de trabalho remoto para outro local, computador baseado em Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Se não for possível, verificar as seguintes definições no seu computador:

* Uma definição de local firewall que está a bloquear o tráfego de ambiente de trabalho remoto.
* Software de proxy do cliente que está a impedir ligações de ambiente de trabalho remoto foi instalado localmente.
* Localmente instalado software que está a impedir ligações de ambiente de trabalho remoto da monitorização de rede.
* Outros tipos de software de segurança que monitorizar o tráfego ou permitirem/não permitir tipos específicos de tráfego que está a impedir ligações de ambiente de trabalho remoto.

Nestes casos, temporariamente desative o software e tente estabelecer ligação com um computador no local através de ambiente de trabalho remoto. Se lhe pode descobrir a causa real desta forma, trabalhar com o administrador da rede para corrigir as definições de software para permitir ligações de ambiente de trabalho remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Origem 2: Dispositivo de limite de intranet de organização
Certifique-se de que o computador diretamente ligado à Internet pode efetuar ligações de ambiente de trabalho remoto à máquina virtual do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Se não tiver um computador que está diretamente ligado à Internet, criar e testar com uma nova máquina virtual do Azure no serviço de nuvem ou grupo de recursos. Para obter mais informações, consulte [criar uma máquina virtual com o Windows no Azure](../virtual-machines-windows-hero-tutorial.md). É possível eliminar a máquina virtual e o grupo de recursos ou o serviço em nuvem, após o teste.

Se pode criar uma ligação de ambiente de trabalho remoto com um computador ligado diretamente à Internet, verifique o seu dispositivo de limite de intranet de organização para:

* Uma firewall interna bloquear ligações de HTTPS para a Internet.
* Um servidor proxy impedir ligações de ambiente de trabalho remoto.
* Deteção de intrusões ou software em execução em dispositivos na sua rede de limite que está a impedir ligações de ambiente de trabalho remoto de monitorização de rede.

Trabalhar com o administrador da rede para corrigir as definições do seu dispositivo de limite de intranet de organização para permitir ligações de ambiente de trabalho remoto baseado em HTTPS para a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem 3: Ponto final do serviço de nuvem e a ACL
Para VMs criadas utilizando o modelo de implementação clássica, certifique-se de que outra VM do Azure que está no mesmo serviço em nuvem ou de rede virtual pode efetuar ligações de ambiente de trabalho remoto para a VM do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Para máquinas virtuais no Gestor de recursos, avance para o [origem 4: grupos de segurança de rede](#source-4-network-security-groups).

Se não tiver outra máquina virtual no mesmo serviço em nuvem ou de rede virtual, crie uma. Siga os passos no [criar uma máquina virtual com o Windows no Azure](../virtual-machines-windows-hero-tutorial.md). Após a conclusão do teste, elimine a máquina virtual de teste.

Se podem ligar através do ambiente de trabalho remoto para uma máquina virtual no mesmo serviço em nuvem ou de rede virtual, verifique a existência destas definições:

* A configuração de ponto final para o tráfego de ambiente de trabalho remoto no destino VM: A porta TCP privada do ponto final tem de corresponder à porta TCP no qual o serviço de ambiente de trabalho remoto a VM está à escuta (a predefinição é 3389).
* A ACL de ponto final do tráfego do ambiente de trabalho remoto no destino VM: ACLs permitem-lhe especificar permitido ou negado o tráfego de entrada a partir da Internet com base no respetivo endereço IP de origem. ACLs mal configuradas podem impedir o tráfego recebido do ambiente de trabalho remoto para o ponto final. Verifique as ACLs para garantir que esse tráfego recebido dos seus endereços IP públicos do seu proxy ou outro servidor edge é permitido. Para obter mais informações, consulte [o que é uma lista de controlo de acesso de rede (ACL)?](../../virtual-network/virtual-networks-acl.md)

Para verificar se o ponto final é a origem do problema, remova o ponto final atual e crie um novo, escolher uma porta aleatória entre 49152 – 65535 para o número de porta externa. Para obter mais informações, consulte [como configurar pontos finais para uma máquina virtual](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Origem 4: Grupos de segurança de rede
Grupos de segurança de rede permite um controlo mais granular do tráfego de entrada e saída permitido. Pode criar regras de sub-redes de expansão e serviços em nuvem na uma rede virtual do Azure.

Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode consultar regras do grupo de segurança eficaz para garantir que a entrada "Permitir" NSG regra existe e está definida para a porta RDP (predefinição 3389). Para obter mais informações, consulte [utilizando regras de segurança eficaz para resolver problemas de VM de fluxo de tráfego](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

## <a name="source-5-windows-based-azure-vm"></a>Origem 5: Baseados em Windows VM do Azure
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Siga as instruções em [neste artigo](reset-rdp.md). Este artigo repõe o serviço de ambiente de trabalho remoto na máquina virtual:

* Ative a regra predefinida do Firewall do Windows "Ambiente de trabalho remoto" (a porta TCP 3389).
* Ative as ligações de ambiente de trabalho remoto, definindo o valor de registo HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections como 0.

Tente novamente a ligação do seu computador. Se ainda não conseguir estabelecer ligação através do ambiente de trabalho remoto, verifique os seguintes problemas possíveis:

* O serviço de ambiente de trabalho remoto não está em execução a VM de destino.
* O serviço de ambiente de trabalho remoto não está a escutar na porta TCP 3389.
* Firewall do Windows ou outra firewall local tem uma regra de saída que está a impedir o tráfego de ambiente de trabalho remoto.
* Deteção de intrusões ou software em execução na máquina virtual do Azure de monitorização de rede está a impedir ligações de ambiente de trabalho remoto.

Para VMs criadas com o modelo de implementação clássica, pode utilizar uma sessão remota do PowerShell do Azure para a máquina virtual do Azure. Em primeiro lugar, tem de instalar um certificado para o serviço de nuvem de alojamento da máquina virtual. Aceda a [configurar Secure PowerShell acesso remoto para máquinas virtuais do Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e transferir o **InstallWinRMCertAzureVM.ps1** ficheiro de script para o seu computador local.

Em seguida, instale o Azure PowerShell se ainda não o fez. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Em seguida, abra uma linha de comandos do Azure PowerShell e altere a pasta atual para a localização do **InstallWinRMCertAzureVM.ps1** ficheiro de script. Para executar um script do PowerShell do Azure, tem de definir a política de execução correta. Execute o **Get-ExecutionPolicy** o comando para determinar o nível de política atual. Para obter informações sobre como definir o nível adequado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome da sua subscrição do Azure, o nome do serviço de nuvem e o nome da máquina virtual (remover a < e > carateres), e, em seguida, execute estes comandos.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Pode obter o nome da subscrição correta do *SubscriptionName* propriedade a apresentação do **Get-AzureSubscription** comando. Pode obter o nome do serviço em nuvem para a máquina virtual a partir de *ServiceName* coluna na apresentação do **Get-AzureVM** comando.

Verifique se tem o novo certificado. Abrir um snap-in de certificados para o utilizador atual e procure no **authorities\certificate de certificação de raiz fidedigna** pasta. Deverá ver um certificado com o nome DNS do seu serviço em nuvem na coluna emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do PowerShell do Azure utilizando estes comandos.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Depois de introduzir as credenciais de administrador válido, deverá ver algo semelhante a linha de comandos da Azure PowerShell seguinte:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A primeira parte da linha de comandos, é o nome do serviço em nuvem que contém o destino VM, que pode ser diferente de "cloudservice4testing.cloudapp.net". Agora, pode emitir Azure PowerShell comandos para este serviço em nuvem investigar os problemas mencionados e corrija a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente os serviços de ambiente de trabalho remoto está a escutar a porta TCP
Linha de sessão remota do Azure PowerShell, execute este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A propriedade PortNumber mostra o número da porta atual. Se necessário, altere o ambiente de trabalho remoto da porta número anterior para o valor predefinido (3389) utilizando este comando.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Certifique-se de que a porta foi alterada para 3389 utilizando este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Sair da sessão remota do PowerShell do Azure utilizando este comando.

```powershell
Exit-PSSession
```

Certifique-se de que o ponto final de ambiente de trabalho remoto para a VM do Azure também está a utilizar a porta TCP 3398 como a respetiva porta interna. Reinicie a VM do Azure e tente novamente a ligação de ambiente de trabalho remoto.

## <a name="additional-resources"></a>Recursos adicionais
[Como repor uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas virtuais do Windows](reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

[Resolver problemas de ligações de Secure Shell (SSH) para uma máquina virtual do Azure baseados em Linux](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Resolver problemas de acesso a uma aplicação em execução numa máquina virtual do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

