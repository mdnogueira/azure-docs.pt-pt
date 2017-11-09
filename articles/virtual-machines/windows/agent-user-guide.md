---
title: "Descrição geral do agente de Máquina Virtual do Azure | Microsoft Docs"
description: "Descrição geral do agente de Máquina Virtual do Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: f3e4ab075f7cd75bac2d66f0391227c0de6e11a0
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="azure-virtual-machine-agent-overview"></a>Descrição geral do agente da Máquina Virtual do Azure

O agente de Máquina Virtual do Microsoft Azure (AM Agent) é um processo seguro e simples que gere a interação de VM com o controlador de recursos de infraestrutura do Azure. O agente da VM tem uma função primária em ativar e executar as extensões de máquina virtual do Azure. Ativar as extensões de VM após a configuração de implementação de máquinas virtuais, tais como instalar e configurar o software. Extensões de máquina virtual também ativar as funcionalidades de recuperação, tal como repor a palavra-passe administrativa de uma máquina virtual. Sem o agente da VM do Azure, não não possível executar as extensões de máquina virtual.

Este documento fornece detalhes sobre a instalação, a deteção e a remoção do agente de Máquina Virtual do Azure.

## <a name="install-the-vm-agent"></a>Instalar o agente da VM

### <a name="azure-gallery-image"></a>Imagem da galeria do Azure

O agente da VM do Azure está instalado por predefinição em qualquer máquina virtual do Windows implementada a partir de uma imagem de galeria do Azure. Quando implementar uma imagem de galeria do Azure a partir do Portal, do PowerShell, Interface de linha de comandos ou um modelo Azure Resource Manager, também é instalado o agente da VM do Azure. 

### <a name="manual-installation"></a>Instalação manual

O agente da VM do Windows pode ser instalado de manualmente utilizando um pacote de instalador do Windows. Instalação manual pode ser necessária quando criar uma imagem de máquina virtual personalizada que será implementado no Azure. Para instalar manualmente o agente de VM do Windows, transferir o instalador do agente de VM a partir desta localização [transferir de agente do Windows Azure VM](http://go.microsoft.com/fwlink/?LinkID=394789). 

O agente da VM pode ser instalado, fazendo duplo clique o ficheiro do windows installer. Para uma instalação automatizada ou automática do agente VM, execute o seguinte comando.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Detetar o agente da VM

### <a name="powershell"></a>PowerShell

O módulo do PowerShell do Azure Resource Manager pode ser utilizado para obter informações sobre máquinas virtuais do Azure. Executar `Get-AzureRmVM` devolve bastantes bits de informações, incluindo o estado de aprovisionamento para o agente da VM do Azure.

```PowerShell
Get-AzureRmVM
```

Segue-se apenas um subconjunto do `Get-AzureRmVM` saída. Tenha em atenção o `ProvisionVMAgent` propriedade aninhada `OSProfile`, esta propriedade pode ser utilizada para determinar se o agente da VM foi implementado para a máquina virtual.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

O script seguinte pode ser utilizado para devolver uma lista de nomes de máquina virtual e o estado do agente de VM concisa.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Deteção manual

Quando tem sessão iniciada VM do Windows Azure, o Gestor de tarefas pode ser utilizado para examinar os processos em execução. Para verificar se o agente da VM do Azure, abra o Gestor de tarefas > clique no separador de detalhes e procure um nome de processo `WindowsAzureGuestAgent.exe`. A presença deste processo indica que o agente VM está instalado.

## <a name="upgrade-the-vm-agent"></a>Atualize o agente VM

O Azure VM agente para o Windows é atualizado automaticamente. Como novas máquinas virtuais são implementadas para o Azure, estes recebem o agente VM mais recente. Imagens VM personalizadas devem ser atualizadas manualmente para incluir o novo agente VM.