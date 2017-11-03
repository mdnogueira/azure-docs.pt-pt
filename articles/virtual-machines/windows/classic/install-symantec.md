---
title: Instalar o Symantec Endpoint Protection no Windows VM no Azure | Microsoft Docs
description: "Saiba como instalar e configurar a extensão de segurança da Symantec Endpoint Protection numa nova ou existente do Azure VM criada com o modelo de implementação clássica."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: iainfou
ms.openlocfilehash: 1603ebc7ee3c29277f30fbb802bdd8205b92d648
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo mostra como instalar e configurar o cliente Symantec Endpoint Protection numa máquina virtual existente (VM) com o Windows Server. Este cliente completo inclui os serviços, tais como vírus e spyware proteção, a firewall e a prevenção de intrusões. O cliente é instalado como uma extensão de segurança utilizando o agente da VM.

Se tiver uma subscrição existente da Symantec para uma solução no local, pode utilizá-lo a proteger as máquinas virtuais do Azure. Se ainda não estiver um cliente, pode inscrever-se para uma subscrição de avaliação. Para obter mais informações sobre esta solução, consulte [Symantec Endpoint Protection na plataforma do Azure da Microsoft][Symantec]. Esta página também contém ligações para informações de licenciamento e instruções para instalar o cliente se já tiver um cliente da Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalar o Symantec Endpoint Protection numa VM existente
Antes de começar, precisa do seguinte:

* O módulo Azure PowerShell, versão 0.8.2 ou posterior, no seu computador de trabalho. Pode verificar a versão do Azure PowerShell que tenha instalado com o **azure Get-Module | versão de formato tabela** comando. Para obter instruções e uma hiperligação para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell][PS]. Inicie sessão na sua subscrição do Azure utilizando `Add-AzureAccount`.
* O agente de VM em execução na máquina de Virtual do Azure.

Em primeiro lugar, certifique-se de que o agente da VM já está instalado na máquina virtual. Preencha o nome de serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos uma linha de comandos do PowerShell do Azure de nível de administrador. Substituir tudo dentro de aspas, incluindo os < e > carateres.

> [!TIP]
> Se não souber os nomes de máquina virtual e o serviço em nuvem, execute **Get-AzureVM** para listar os nomes de todas as máquinas virtuais na sua subscrição atual.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se o **escrita anfitrião** comando apresenta **verdadeiro**, o agente VM está instalado. Se apresenta **falso**, consulte as instruções e uma hiperligação para a transferência do blogue do Azure [extensões - parte 2 e o agente da VM][Agent].

Se o agente VM está instalado, execute estes comandos para instalar o agente de proteção de ponto final da Symantec.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Para verificar se a extensão de segurança da Symantec foi instalada e se encontra atualizada:

1. Inicie sessão na máquina virtual. Para obter instruções, consulte [como iniciar sessão uma Máquina Virtual a executar o Windows Server][Logon].
2. Para o Windows Server 2008 R2, clique em **Iniciar > Symantec Endpoint Protection**. Para o Windows Server 2012 ou Windows Server 2012 R2, no ecrã Iniciar, escreva **Symantec**e, em seguida, clique em **Symantec Endpoint Protection**.
3. Do **estado** separador do **Estado Symantec Endpoint Protection** janela, aplicar atualizações ou reiniciar se for necessário.

## <a name="additional-resources"></a>Recursos adicionais
[Como iniciar sessão uma Máquina Virtual com o Windows Server][Logon]

[Extensões VM do Azure e funcionalidades][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
