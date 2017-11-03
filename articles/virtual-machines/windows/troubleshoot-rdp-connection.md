---
title: "Não é possível estabelecer ligação com RDP para uma VM do Windows no Azure | Microsoft Docs"
description: "Resolver problemas quando não é possível ligar à máquina virtual Windows no Azure utilizando o ambiente de trabalho remoto"
keywords: "Erro de ambiente de trabalho remoto, erro de ligação de ambiente de trabalho remoto, não é possível ligar à VM, resolução de ambiente de trabalho remoto"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/25/2017
ms.author: genli
ms.openlocfilehash: f7810d519da500a33c2e1cd15b8de54835f304d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Resolver problemas de ligações de ambiente de trabalho remoto para uma máquina virtual do Azure
A ligação de protocolo RDP (Remote Desktop Protocol) ao seu baseados em Windows máquina virtual do Azure (VM) pode falhar por diversos motivos, deixando não é possível aceder à VM. O problema pode ser com o serviço de ambiente de trabalho remoto em VM, a ligação de rede ou o cliente de ambiente de trabalho remoto no computador anfitrião. Este artigo orienta-o através de alguns dos métodos mais comuns para resolver problemas de ligação de RDP. 

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporta**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Depois de cada passo de resolução de problemas, tente restabelecer a ligação para a VM:

1. Reponha a configuração do ambiente de trabalho remoto.
2. Grupo de segurança de rede Consulte regras / nuvem pontos finais de serviços.
3. Consulte os registos de consola VM.
4. Repor o NIC para a VM.
5. Verifique o estado de funcionamento do recurso VM.
6. Repor a palavra-passe VM.
7. Reinicie a VM.
8. Volte a implementar a VM.

Continue a ler se precisar de mais detalhados de passos e explicações. Certifique-se de que equipamento de rede local, tais como routers e firewalls não estão a bloquear porta de saída de TCP 3389, conforme indicado no [RDP cenários de resolução de problemas de detalhado](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Se o **Connect** botão para a VM está desativada terminar no portal e não se encontra ligado ao Azure através de um [Express Route](../../expressroute/expressroute-introduction.md) ou [VPN Site a Site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) ligação, terá de criar e atribuir um endereço IP público à VM antes de poder utilizar o RDP. Pode ler mais sobre [endereços IP públicos no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Opções para solucionar problemas RDP
Pode resolver VMs criadas utilizando o modelo de implementação Resource Manager, utilizando um dos seguintes métodos:

* [Portal do Azure](#using-the-azure-portal) - excelente se precisar de repor rapidamente as credenciais de utilizador ou de configuração de RDP e não tem as ferramentas do Azure instaladas.
* [O Azure PowerShell](#using-azure-powershell) - se estiver familiarizado com a linha de comandos do PowerShell, rapidamente repor as credenciais de utilizador ou de configuração de RDP utilizando os cmdlets do PowerShell do Azure.

Também pode encontrar passos sobre como resolver problemas de VMs criadas utilizando o [modelo de implementação clássica](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Resolver problemas com o portal do Azure
Depois de cada passo de resolução de problemas, tente ligar novamente à VM. Se ainda não é possível estabelecer ligação, repita o passo seguinte.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe da configuração RDP quando as ligações remotas estão desativadas ou regras de Firewall do Windows estão a bloquear o RDP, por exemplo.
   
    Selecione a VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique em de **Repor palavra-passe** botão. Definir o **modo** para **apenas a configuração de reposição** e, em seguida, clique em de **atualização** botão:
   
    ![Reposição da configuração RDP no portal do Azure](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Regras do grupo de segurança de rede verifique**. Utilize a [verificação do fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra num Grupo de Segurança de Rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode consultar regras do grupo de segurança eficaz para garantir que a entrada "Permitir" NSG regra existe e está definida para a porta RDP (predefinição 3389). Para obter mais informações, consulte [utilizando regras de segurança eficaz para resolver problemas de VM de fluxo de tráfego](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

3. **Reveja o diagnóstico de arranque VM**. Este passo de resolução de problemas revê os registos de consola VM para determinar se a VM está a comunicar um problema. Nem todas as VMs têm de diagnóstico de arranque ativado, pelo que este passo de resolução de problemas pode ser opcional.
   
    Passos de resolução de problemas específicos estão fora do âmbito deste artigo, mas podem indicar um problema vasta que está a afetar a conectividade RDP. Para obter mais informações sobre a rever os registos de consola e a captura de ecrã da VM, consulte [diagnóstico de arranque para VMs](boot-diagnostics.md).

4. **Repor o NIC para a VM**. Para obter mais informações, consulte [como repor o NIC para o Azure Windows VM](reset-network-interface.md).
5. **Verifique o estado de funcionamento do recurso VM**. Este passo de resolução de problemas verifica existem não existem problemas conhecidos com a plataforma do Azure que possam afetar a conectividade à VM.
   
    Selecione a VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique em de **estado de funcionamento do recurso** botão. Uma bom estado de funcionamento VM relatórios como sendo **disponível**:
   
    ![Verifique o estado de funcionamento de recursos VM no portal do Azure](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Repor as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe numa conta de administrador local quando não souber ou se tenha esquecido as credenciais.
   
    Selecione a VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique em de **Repor palavra-passe** botão. Certifique-se de que o **modo** está definido como **Repor palavra-passe** e, em seguida, introduza o nome de utilizador e uma palavra-passe nova. Por fim, clique em de **atualização** botão:
   
    ![Repor as credenciais de utilizador no portal do Azure](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Reiniciar a VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que a própria VM está a ter.
   
    Selecione a VM no portal do Azure e clique em de **descrição geral** separador. Clique em de **reiniciar** botão:
   
    ![Reiniciar a VM no portal do Azure](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Volte a implementar a VM**. Este passo de resolução de problemas redeploys a VM para outro anfitrião no Azure para corrigir problemas de rede ou de qualquer plataforma subjacente.
   
    Selecione a VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique em de **Reimplementar** botão e, em seguida, clique em **Reimplementar**:
   
    ![Volte a implementar a VM no portal do Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Depois de concluída esta operação, os dados de disco efémeras são perdidos e endereços IP dinâmicos que estão associados a VM são atualizados.

Se ainda tiver com problemas RDP, pode [abrir um pedido de suporte](https://azure.microsoft.com/support/options/) ou ler [mais detalhadas RDP conceitos e passos de resolução de problemas](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Resolver problemas com o Azure PowerShell
Se ainda não o fez, [instalar e configurar o Azure PowerShell mais recente](/powershell/azure/overview).

Os exemplos seguintes utilizam variáveis como `myResourceGroup`, `myVM`, e `myVMAccessExtension`. Substitua estes nomes de variáveis e localizações com os seus próprios valores.

> [!NOTE]
> Repor as credenciais de utilizador e a configuração RDP utilizando o [conjunto AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) cmdlet do PowerShell. Nos exemplos a seguir, `myVMAccessExtension` é um nome que especificar como parte do processo. Se anteriormente já trabalhou com o VMAccessAgent, pode obter o nome da extensão existente utilizando `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para verificar as propriedades da VM. Para ver o nome, procure na secção 'Extensões' de saída.

Depois de cada passo de resolução de problemas, tente ligar novamente à VM. Se ainda não é possível estabelecer ligação, repita o passo seguinte.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe da configuração RDP quando as ligações remotas estão desativadas ou regras de Firewall do Windows estão a bloquear o RDP, por exemplo.
   
    O exemplo a seguir repõe a ligação de RDP numa VM chamada `myVM` no `WestUS` localização e o grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Regras do grupo de segurança de rede verifique**. Este passo de resolução de problemas verifica que tem uma regra no seu grupo de segurança de rede para permitir tráfego RDP. A porta predefinida para RDP é a porta TCP 3389. Uma regra para permitir tráfego RDP de não pode ser criada automaticamente quando criar a VM.
   
    Atribui pela primeira vez, todos os dados de configuração para o grupo de segurança de rede para o `$rules` variável. O exemplo seguinte obtém informações sobre o grupo de segurança de rede com o nome `myNetworkSecurityGroup` no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Agora, ver as regras que estão configuradas para este grupo de segurança de rede. Certifique-se de que existe uma regra para permitir que a porta TCP 3389 em ligações de entrada da seguinte forma:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    O exemplo seguinte mostra uma regra de segurança válido que permite tráfego RDP. Pode ver `Protocol`, `DestinationPortRange`, `Access`, e `Direction` estão configuradas corretamente:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Se não tiver uma regra que permite que o tráfego RDP, [criar uma regra de grupo de segurança de rede](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Permitir que a porta TCP 3389.
3. **Repor as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe da conta de administrador local que especificou quando não souber da ou ter esquecimento, as credenciais.
   
    Em primeiro lugar, especifique o nome de utilizador e uma nova palavra-passe através da atribuição de credenciais para o `$cred` variável da seguinte forma:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Agora, Atualize as credenciais da sua VM. O exemplo seguinte atualiza as credenciais de uma VM chamada `myVM` no `WestUS` localização e o grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Reiniciar a VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que a própria VM está a ter.
   
    O exemplo seguinte reinicia a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Volte a implementar a VM**. Este passo de resolução de problemas redeploys a VM para outro anfitrião no Azure para corrigir problemas de rede ou de qualquer plataforma subjacente.
   
    O exemplo seguinte redeploys VM com o nome `myVM` no `WestUS` localização e o grupo de recursos com o nome `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Se ainda tiver com problemas RDP, pode [abrir um pedido de suporte](https://azure.microsoft.com/support/options/) ou ler [mais detalhadas RDP conceitos e passos de resolução de problemas](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Resolver problemas de VMs criadas utilizando o modelo de implementação clássica
Depois de cada passo de resolução de problemas, tente restabelecer a ligação à VM.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe da configuração RDP quando as ligações remotas estão desativadas ou regras de Firewall do Windows estão a bloquear o RDP, por exemplo.
   
    Selecione a VM no portal do Azure. Clique em de **... Mais** botão, em seguida, clique em **repor o acesso remoto**:
   
    ![Reposição da configuração RDP no portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Certifique-se de pontos finais de serviços em nuvem**. Este passo de resolução de problemas verifica que tem pontos finais nos seus serviços em nuvem para permitir tráfego RDP. A porta predefinida para RDP é a porta TCP 3389. Uma regra para permitir tráfego RDP de não pode ser criada automaticamente quando criar a VM.
   
   Selecione a VM no portal do Azure. Clique em de **pontos finais** botão para ver os pontos finais atualmente configurados para a VM. Certifique-se de que existem pontos finais que permitam o tráfego RDP na porta TCP 3389.
   
   O exemplo seguinte mostra os pontos finais válidos que permitem o tráfego RDP de:
   
   ![Certifique-se de pontos finais de serviços em nuvem no portal do Azure](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se não tiver um ponto final que permita o tráfego RDP, [criar um ponto final de serviços em nuvem](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Permitir que o TCP 3389 uma porta privada.
3. **Reveja o diagnóstico de arranque VM**. Este passo de resolução de problemas revê os registos de consola VM para determinar se a VM está a comunicar um problema. Nem todas as VMs têm de diagnóstico de arranque ativado, pelo que este passo de resolução de problemas pode ser opcional.
   
    Passos de resolução de problemas específicos estão fora do âmbito deste artigo, mas podem indicar um problema vasta que está a afetar a conectividade RDP. Para obter mais informações sobre a rever os registos de consola e a captura de ecrã da VM, consulte [diagnóstico de arranque para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Verifique o estado de funcionamento do recurso VM**. Este passo de resolução de problemas verifica existem não existem problemas conhecidos com a plataforma do Azure que possam afetar a conectividade à VM.
   
    Selecione a VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique em de **estado de funcionamento do recurso** botão. Uma bom estado de funcionamento VM relatórios como sendo **disponível**:
   
    ![Verifique o estado de funcionamento de recursos VM no portal do Azure](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Repor as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe da conta de administrador local que especificou quando não souber ou se tenha esquecido as credenciais.
   
    Selecione a VM no portal do Azure. Desloque para baixo do painel de definições para o **suporte + resolução de problemas** secção perto da parte inferior da lista. Clique em de **Repor palavra-passe** botão. Introduza o nome de utilizador e uma palavra-passe nova. Por fim, clique em de **guardar** botão:
   
    ![Repor as credenciais de utilizador no portal do Azure](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Reiniciar a VM**. Este passo de resolução de problemas pode corrigir quaisquer problemas subjacentes que a própria VM está a ter.
   
    Selecione a VM no portal do Azure e clique em de **descrição geral** separador. Clique em de **reiniciar** botão:
   
    ![Reiniciar a VM no portal do Azure](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

Se ainda tiver com problemas RDP, pode [abrir um pedido de suporte](https://azure.microsoft.com/support/options/) ou ler [mais detalhadas RDP conceitos e passos de resolução de problemas](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Resolver erros específicos de RDP
Poderá encontrar uma mensagem de erro específico ao tentar ligar à VM através do RDP. Seguem-se as mensagens de erro mais comuns:

* [A sessão remota foi desligada porque existem não existem servidores de licenciamento de ambiente de trabalho remoto disponíveis para fornecer uma licença](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Ambiente de trabalho remoto não é possível localizar o computador "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Ocorreu um erro de autenticação. Não é possível contactar a autoridade de segurança Local](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Erro de segurança do Windows: as suas credenciais não foi possível funcionar](troubleshoot-specific-rdp-errors.md#wincred).
* [Este computador não é possível ligar ao computador remoto](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Recursos adicionais
Se nenhum destes erros ocorreu e ainda não é possível ligar à VM através do ambiente de trabalho remoto, leia a secção detalhada [guia para o ambiente de trabalho remoto de resolução de problemas](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para aceder a aplicações em execução numa VM passos de resolução de problemas, consulte [resolver problemas de acesso a uma aplicação em execução numa VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se estiver a ter problemas utilizando Secure Shell (SSH) para ligar a uma VM com Linux no Azure, consulte [resolver problemas de SSH ligações a uma VM com Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

