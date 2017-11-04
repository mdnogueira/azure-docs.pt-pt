---
title: "Instalar a tendência de segurança avançada Micro numa VM | Microsoft Docs"
description: "Este artigo descreve como instalar e configurar a segurança de tendência Micro numa VM criada com o modelo de implementação clássico no Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: iainfou
ms.openlocfilehash: 911b8f12472dcbda3e6bfeb8c97bf1d04a63e1dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar o Trend Micro Deep Security como Serviço numa VM do Windows
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo mostra como instalar e configurar a segurança avançada do tendência Micro como um serviço numa nova ou existente máquina virtual (VM) com o Windows Server. Segurança avançada, como um serviço inclui proteção antimalware, uma firewall, um sistema de prevenção de intrusões e monitorização de integridade.

O cliente é instalado como uma extensão de segurança através do agente da VM. Numa nova máquina virtual, instalar o agente de segurança avançadas, como o agente da VM é automaticamente criado pelo portal do Azure.

VM existente criada utilizando o portal clássico, a CLI do Azure ou o PowerShell não pode ter um agente VM. Para uma máquina virtual existente que não tem o agente da VM, tem de transferir e instalá-lo primeiro. Este artigo abrange ambas as situações.

Se tiver uma subscrição atual de tendência Micro para uma solução no local, pode utilizá-lo para ajudar a proteger as máquinas virtuais do Azure. Se ainda não estiver um cliente, pode inscrever-se para uma subscrição de avaliação. Para obter mais informações sobre esta solução, consulte a mensagem de blogue de tendência Micro [Microsoft Azure VM extensão para a segurança do agente](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instale o agente de segurança avançada numa nova VM

O [portal do Azure](http://portal.azure.com) permite-lhe instalar a extensão de segurança de tendência Micro quando utiliza uma imagem a partir de **Marketplace** para criar a máquina virtual. Se estiver a criar uma única máquina virtual, utilizando o portal é uma forma fácil adicionar proteção de tendência Micro.

Através de uma entrada do **Marketplace** abre um assistente que o ajuda a configurar a máquina virtual. Utilizar o **definições** painel, o painel do assistente, instalar a extensão de segurança de tendência Micro terceiro.  Para instruções gerais, consulte [criar uma máquina virtual com o Windows no portal do Azure](tutorial.md).

Quando chegar ao **definições** painel do assistente, efetue os seguintes passos:

1. Clique em **extensões**, em seguida, clique em **Adicionar extensão** no painel seguinte.

   ![Começar a adicionar a extensão][1]

2. Selecione **profunda de segurança de agente** no **novo recurso** painel. No painel de agente de segurança avançada, clique em **criar**.

   ![Identificar o agente de segurança avançada][2]

3. Introduza o **identificador de inquilino** e **palavra-passe de ativação de inquilino** para a extensão. Opcionalmente, pode introduzir um **identificador de política de segurança**. Em seguida, clique em **OK** para adicionar o cliente.

   ![Forneça detalhes de extensão][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instale o agente de segurança avançada no VM existente
Para instalar o agente numa VM existente, terá dos seguintes itens:

* O módulo Azure PowerShell, versão 0.8.2 ou mais recente, instalada no seu computador local. Pode verificar a versão do Azure PowerShell que tenha instalado utilizando o **azure Get-Module | versão de formato tabela** comando. Para obter instruções e uma hiperligação para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Inicie sessão na sua subscrição do Azure utilizando `Add-AzureAccount`.
* O agente da VM instalado na máquina virtual de destino.

Em primeiro lugar, certifique-se de que o agente da VM já está instalado. Preencha o nome de serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos uma linha de comandos do PowerShell do Azure de nível de administrador. Substituir tudo dentro de aspas, incluindo os < e > carateres.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se não souber o nome da máquina virtual e o serviço em nuvem, execute **Get-AzureVM** para apresentar informações para todas as máquinas virtuais na sua subscrição atual.

Se o **escrita anfitrião** comando devolve **verdadeiro**, o agente VM está instalado. Se devolver **falso**, consulte as instruções e uma hiperligação para a transferência do blogue do Azure [extensões - parte 2 e o agente da VM](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o agente VM está instalado, execute estes comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Passos seguintes
Demora alguns minutos para que o agente iniciar a execução quando é instalado. Depois disso, terá de ativar segurança profunda na máquina virtual, para que possa ser gerido por um Gestor de segurança avançada. Consulte os artigos seguintes para obter instruções adicionais:

* Artigo da tendência sobre esta solução, [Instant-On Cloud Security para o Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* A [script do Windows PowerShell de exemplo](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
* [Instruções](http://go.microsoft.com/fwlink/?LinkId=404099) para o exemplo

## <a name="additional-resources"></a>Recursos adicionais
[Como iniciar sessão a uma máquina virtual com o Windows Server]

[Extensões de VM do Azure e funcionalidades]

<!-- Image references -->
[1]: ./media/install-trend/new_vm_Blade3.png
[2]: ./media/install-trend/find_SecurityAgent.png
[3]: ./media/install-trend/SecurityAgentDetails.png

<!-- Link references -->
[Como iniciar sessão a uma máquina virtual com o Windows Server]:connect-logon.md
[Extensões de VM do Azure e funcionalidades]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
