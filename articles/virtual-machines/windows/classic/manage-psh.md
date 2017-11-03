---
title: "Gerir as máquinas virtuais utilizando o Azure PowerShell | Microsoft Docs"
description: "Conheça os comandos que pode utilizar para automatizar tarefas na gestão de máquinas virtuais."
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: fd2df7e1029ced11974d0b832258bed2cf3bbb27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gerir máquinas virtuais utilizando o Azure PowerShell
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para comandos do PowerShell comuns utilizando o modelo do Resource Manager, consulte [aqui](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Muitas tarefas, fazê-lo por dia para gerir as suas VMs podem ser automatizadas utilizando cmdlets do PowerShell do Azure. Este artigo dá-lhe comandos de exemplo para tarefas mais simples e ligações para artigos que mostram os comandos para as tarefas mais complexas.

> [!NOTE]
> Se ainda não instalado e configurado o Azure PowerShell ainda, pode obter as instruções no artigo [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Como utilizar os comandos de exemplo
Terá de substituir alguns do texto nos comandos com o texto que é adequado para o seu ambiente. O < e > símbolos indicam o texto é necessário substituir. Quando a substituir o texto, remover os símbolos, mas deixe as marcas de aspas no local.

## <a name="get-a-vm"></a>Obter uma VM
Esta é uma tarefa de básica que irá utilizar com frequência. Utilizá-la para obter informações sobre uma VM, efetuar tarefas numa VM ou obter de saída para armazenar numa variável.

Para obter informações sobre a VM, execute este comando, substituindo tudo aspas, incluindo os < e > carateres:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para armazenar o resultado numa variável $vm, execute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Inicie sessão numa VM com base no Windows
Execute estes comandos:

> [!NOTE]
> Pode obter o nome do serviço de máquina virtual e em nuvem a partir de apresentação do **Get-AzureVM** comando.
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "< localização de pasta ou unidade para armazenar o ficheiro RDP transferido, exemplo: c:\temp >" $localFile = $localPath + "\" + $vmname +". rdp"Get-AzureRemoteDesktopFile - ServiceName $svcName-nome $vmName - LocalPath $localFile-iniciar
> 
> 

## <a name="stop-a-vm"></a>Parar uma VM
Execute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Utilize este parâmetro para manter o IP virtual (VIP) do serviço de nuvem, caso seja a última VM em que o serviço de nuvem. <br><br> Se utilizar o parâmetro StayProvisioned, ainda serão cobrados para a VM.
> 
> 

## <a name="start-a-vm"></a>Iniciar uma VM
Execute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Esta tarefa requer alguns passos. Em primeiro lugar, utilize o *** Add-AzureDataDisk *** cmdlet para adicionar o disco para o objeto de $vm. Em seguida, utilize **Update-AzureVM** cmdlet para atualizar a configuração da VM.

Também vai ter de decidir entre anexar um disco novo ou um que contenha dados. Para um novo disco, o comando cria o ficheiro. vhd e anexa-lo.

Para anexar um disco novo, execute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Para anexar um disco de dados existente, execute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para ligar os discos de dados de um ficheiro. vhd existente no armazenamento de BLOBs, execute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Criar uma VM com base no Windows
Para criar uma nova máquina virtual baseado no Windows no Azure, utilize as instruções no [utilizar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](create-powershell.md). Passos neste tópico, através da criação de um conjunto de comandos do Azure PowerShell cria uma VM baseada em Windows que pode ser pré-configurados:

* Com a associação de domínio do Active Directory.
* Com discos adicionais.
* Definir o membro de um existente com balanceamento de carga.
* Com um endereço IP estático.

