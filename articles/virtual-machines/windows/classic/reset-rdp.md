---
title: "Repor a palavra-passe ou a configuração do ambiente de trabalho remoto numa VM do Windows no Azure | Microsoft Docs"
description: "Saiba como repor uma palavra-passe de conta ou serviços de ambiente de trabalho remoto numa VM do Windows criada utilizando o modelo de implementação clássica, utilizando o portal do Azure ou o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 2a38c792decdc571421c6bed7d2edc5f442591ca
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Como repor o serviço de ambiente de trabalho remoto ou a palavra-passe de início de sessão numa VM do Windows criada utilizando o modelo de implementação clássica
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Também pode [executar estes passos para VMs criadas com o modelo de implementação Resource Manager](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Se não é possível ligar a uma máquina virtual (VM) do Windows, pode repor a palavra-passe de administrador local ou reponha a configuração do serviço de ambiente de trabalho remoto. Pode utilizar o portal do Azure ou a extensão de acesso de VM no Azure PowerShell para repor a palavra-passe.

## <a name="ways-to-reset-configuration-or-credentials"></a>Formas de repor as credenciais ou de configuração
Pode repor os serviços de ambiente de trabalho remoto e as credenciais de diversas formas, consoante as suas necessidades:

- [Reposição através do portal do Azure](#azure-portal)
- [Repor com o Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portal do Azure
Pode utilizar o [portal do Azure](https://portal.azure.com) para repor o serviço de ambiente de trabalho remoto. Para expandir o menu do portal, clique nas barras de três no canto superior esquerdo e, em seguida, clique em **máquinas virtuais (clássicas)**:

![Navegue para a VM do Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Selecione a máquina virtual do Windows e, em seguida, clique em **repor remoto...** . É apresentada a caixa de diálogo seguinte para repor a configuração do ambiente de trabalho remoto:

![Página de configuração reposição RDP](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Pode também repor o nome de utilizador e palavra-passe da conta de administrador local. A VM, clique em **suporte + resolução de problemas** > **Repor palavra-passe**. É apresentado o painel de reposição de palavra-passe:

![Página de reposição de palavra-passe](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Depois de introduzir o novo nome de utilizador e palavra-passe, clique em **guardar**.

## <a name="vmaccess-extension-and-powershell"></a>Extensão VMAccess e PowerShell
Certifique-se de que o agente VM está instalado na máquina virtual. A extensão VMAccess não necessita de ser instalado para poder utilizá-lo, desde que o agente VM está disponível. Certifique-se de que o agente da VM já está instalado utilizando o seguinte comando. (Substituir "myCloudService" e "myVM", os nomes do seu serviço em nuvem e a VM, respetivamente. Pode obter estes nomes executando `Get-AzureVM` sem quaisquer parâmetros.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Se o **escrita anfitrião** comando apresenta **verdadeiro**, o agente VM está instalado. Se apresenta **falso**, consulte as instruções e uma hiperligação para a transferência no [extensões - parte 2 e o agente da VM](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) mensagem de blogue do Azure.

Se criou a máquina virtual através do portal, verifique se `$vm.GetInstance().ProvisionGuestAgent` devolve **verdadeiro**. Caso contrário, pode configurá-lo ao utilizar este comando:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Este comando impede o erro seguinte quando estiver a executar o **conjunto AzureVMExtension** comando nos passos: "Aprovisionar o agente convidado deve ser ativado no objeto de VM antes de definir a extensão de acesso de VM do IaaS."

### <a name="reset-the-local-administrator-account-password"></a>**Repor a palavra-passe da conta de administrador local**
Criar uma credencial de início de sessão com o nome de conta de administrador local atual e uma palavra-passe nova e, em seguida, execute o `Set-AzureVMAccessExtension` da seguinte forma.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Se escrever um nome diferente da conta atual, a extensão VMAccess muda o nome de conta de administrador local, atribui a palavra-passe para essa conta e emite um ambiente de trabalho remoto fim de sessão. Se a conta de administrador local esteja desativada, a extensão VMAccess ativa o mesmo.

Estes comandos também repor a configuração do serviço de ambiente de trabalho remoto.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Repor a configuração do serviço de ambiente de trabalho remoto**
Para repor a configuração do serviço de ambiente de trabalho remoto, execute o seguinte comando:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

A extensão VMAccess executa dois comandos na máquina virtual:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Este comando activa o grupo de Firewall do Windows incorporado que permite o tráfego recebido do ambiente de trabalho remoto, que utiliza a porta TCP 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Este comando define o fDenyTSConnections o valor de registo para 0, ativação de ligações de ambiente de trabalho remoto.

## <a name="next-steps"></a>Passos seguintes
Se a extensão de acesso de VM do Azure não responde e não conseguir repor a palavra-passe, pode [repor o Windows palavra-passe local offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este método é um processo mais avançado e requer que ligue o disco rígido virtual da VM problemático para outra VM. Siga os passos documentados neste artigo pela primeira vez e apenas tente o método de reposição de palavra-passe offline como último recurso.

[Extensões VM do Azure e funcionalidades](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Ligar a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Resolver problemas de ligações de ambiente de trabalho remoto para uma máquina virtual baseado no Windows Azure](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

