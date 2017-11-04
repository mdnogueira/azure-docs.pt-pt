---
title: "Utilizar um VM com o Azure PowerShell de resolução de problemas do Windows | Microsoft Docs"
description: "Saiba como resolver problemas de VM do Windows no Azure ao ligar o disco de SO para uma VM com o Azure PowerShell de recuperação"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 8bac3457e70e86c0f2fb0e70b166097da4a89c23
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Resolver problemas de uma VM do Windows ao anexar o disco de SO para uma VM com o Azure PowerShell de recuperação
Se a máquina virtual (VM) do Windows no Azure encontra um erro de arranque ou de disco, poderá ter de efetuar os passos de resolução de problemas no disco de rígido virtual. Um exemplo comum seria uma atualização da aplicação com falhas que impede a VM de ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar o Azure PowerShell para ligar o seu disco rígido virtual a outra VM do Windows para corrigir os eventuais erros, em seguida, voltar a criar a VM original.


## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM problemas com, mantendo os discos rígidos virtuais.
2. Anexe e montar o disco rígido virtual para outro VM do Windows para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM utilizando o disco rígido virtual original.

Certifique-se de que tem [o Azure PowerShell mais recente](/powershell/azure/overview) instalado e tiver sessão iniciada na sua subscrição:

```powershell
Login-AzureRMAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetros com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.


## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Pode ver uma captura de ecrã da sua VM do Azure para ajudar a resolver problemas de arranque. Nesta captura de ecrã pode ajudar a identificar a razão pela qual não consegue efetuar o arranque de uma VM. O exemplo seguinte obtém a captura de ecrã da VM do Windows com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Consulte a captura de ecrã para determinar o motivo pelo qual a VM está a falhar efetuar o arranque. Tenha em atenção quaisquer mensagens de erro específicas ou códigos de erro fornecidos.


## <a name="view-existing-virtual-hard-disk-details"></a>Ver detalhes de disco rígido virtual existente
Antes de pode anexar o disco rígido virtual a outra VM, tem de identificar o nome do disco rígido virtual (VHD).

O exemplo seguinte obtém as informações para a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Procure `Vhd URI` dentro de `StorageProfile` secção da saída do comando anterior. O seguinte truncado saída de exemplo mostra o `Vhd URI` para o fim do bloco de código:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Eliminar VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde o sistema operativo, as aplicações e configurações são armazenadas. A própria VM é apenas metadados, que definem o tamanho ou localização e referencia recursos, tais como um disco rígido virtual ou o cartão de interface de rede virtual (NIC). Cada disco rígido virtual tem uma concessão atribuída quando ligado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco de SO com uma VM, mesmo quando se encontra num estado parado e desalocado essa VM.

É o primeiro passo para recuperar a VM para eliminar o recurso VM em si. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois da VM é eliminada, anexar o disco rígido virtual a outra VM para resolver os erros.

O exemplo seguinte elimina a VM com o nome `myVM` do grupo de recursos denominado `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Aguarde até que a VM terminou a eliminação antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que associa-a VM tem de ser libertado antes de pode anexar o disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar o disco rígido virtual existente para outra VM
Para os próximos passos, utilize outra VM para fins de resolução de problemas. Anexar o disco rígido virtual existente para esta VM de resolução de problemas para procurar e editar o conteúdo do disco. Este processo permite-lhe corrigir os eventuais erros de configuração ou reveja aplicação adicional ou ficheiros de registo do sistema, por exemplo. Escolha ou crie outra VM a utilizar para fins de resolução de problemas.

Ao anexar o disco rígido virtual existente, especifique o URL para o disco que obteve no precedente `Get-AzureRmVM` comando. O exemplo seguinte anexa um disco rígido virtual existente para a VM de resolução de problemas com o nome `myVMRecovery` no grupo de recursos denominado `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Adicionar um disco requer que especifique o tamanho do disco. Como podemos anexar um disco existente, o `-DiskSizeInGB` está especificado como `$null`. Este valor garante que o disco de dados está corretamente ligado e sem a necessidade para determinar o tamanho do disco de dados verdadeiro.


## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexados

1. RDP para a VM de resolução de problemas utilizando as credenciais adequadas. O exemplo seguinte transfere o ficheiro de ligação do RDP para a VM com o nome `myVMRecovery` no grupo de recursos denominado `myResourceGroup`e transfere-o para `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. O disco de dados é automaticamente detetado e ligado. Ver a lista de volumes a ligados para determinar a letra de unidade da seguinte forma:

    ```powershell
    Get-Disk
    ```

    O resultado de exemplo seguinte mostra o disco rígido virtual ligado um disco **2**. (Também pode utilizar `Get-Volume` para ver a letra de unidade):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Resolva os problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora pode efetuar qualquer manutenção e passos de resolução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmonte e desanexar o disco rígido virtual original
Depois dos erros serem resolvidos, desmontar e desanexar o disco rígido virtual existente a VM de resolução de problemas. Não é possível utilizar o disco rígido virtual com quaisquer outro VM até que a concessão anexar o disco rígido virtual para a VM de resolução de problemas é libertada.

1. Da sua sessão do RDP, desmonte o disco de dados na sua VM de recuperação. É necessário o número do disco do anterior `Get-Disk` cmdlet. Em seguida, utilize `Set-Disk` para definir o disco como offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirme que o disco está agora definido como offline utilizando `Get-Disk` novamente. O resultado de exemplo seguinte mostra que o disco está agora definido como offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Sair da sua sessão do RDP. Da sessão do Azure PowerShell, remova o disco rígido virtual da VM resolução de problemas.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Criar a VM de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). O modelo JSON é a seguinte hiperligação:

- https://RAW.githubusercontent.com/Azure/Azure-QuickStart-Templates/Master/201-VM-Specialized-VHD-existing-vnet/azuredeploy.JSON

O modelo implementa uma VM numa rede virtual existente, utilizando o URL de VHD do comando anterior. O exemplo seguinte implementa o modelo para o grupo de recursos com o nome `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Responda os pedidos para o modelo, tais como o nome da VM, o tipo de SO e o tamanho da VM. O `osDiskVhdUri` é o mesmo utilizado como anteriormente ao anexar o disco rígido virtual existente para a VM de resolução de problemas.


## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque

Quando criar a VM a partir do disco rígido virtual existente, diagnóstico de arranque pode não ser ativado automaticamente. O exemplo seguinte ativa a extensão de diagnóstico da VM com o nome `myVMDeployed` no grupo de recursos denominado `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas em ligar à VM, consulte [nas ligações RDP de resolver para uma VM do Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para problemas com a aceder a aplicações em execução numa VM, consulte [resolver problemas de conectividade de aplicação numa Windows VM](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre como utilizar o Gestor de recursos, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
