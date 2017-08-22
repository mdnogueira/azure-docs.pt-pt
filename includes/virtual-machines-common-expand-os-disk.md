## <a name="overview"></a>Descrição geral
Quando cria uma nova máquina virtual (VM) num Grupo de Recursos ao implementar uma imagem do [Azure Marketplace](https://azure.microsoft.com/marketplace/), a unidade do SO predefinido é de 127 GB. Embora seja possível adicionar discos de dados na VM (consoante o SKU que escolheu) e além disso é recomendado que instale aplicações e cargas de trabalho intensivas de CPU nestes discos de adenda, é frequente que os clientes precisem de expandir a unidade de SO para suportar determinados cenários, como o seguinte:

1. Suportar aplicações antigas que instalam componentes na unidade do SO.
2. Migrar uma máquina virtual ou PC físicos no local com uma unidade de SO maior.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: Resource Manager e Clássico. Este artigo abrange a utilização do modelo de implementação do Resource Manager. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> 
> 

## <a name="resize-the-os-drive"></a>Redimensionar a unidade do SO
Neste artigo, irá realizar a tarefa de redimensionamento da unidade de SO com os módulos do gestor de recursos do [Azure Powershell](/powershell/azureps-cmdlets-docs). Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Inicie de sessão na sua conta do Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição da seguinte forma:
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Defina o nome do grupo de recursos e o nome VM da seguinte forma:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obter uma referência para a VM da seguinte forma:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Pare a VM antes de redimensionar o disco da seguinte forma:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Eis o momento pelo qual tanto esperou! Defina o tamanho do disco de SO para o valor pretendido e atualize a VM da seguinte forma:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 1023 GB.
   > 
   > 
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

E já está! Agora RDP para a VM, abra a Gestão de Computadores (ou gestão de discos) e expanda a unidade através do espaço alocado recentemente.

## <a name="summary"></a>Resumo
Neste artigo, utilizámos os módulos do Azure Resource Manager do Powershell para expandir o disco de SO da máquina virtual IaaS. Abaixo está o script completo reproduzido para a referência:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Passos Seguintes
Através deste artigo, focámo-nos essencialmente na expansão do disco do SO da VM; o script programado também pode ser utilizado para expandir os discos de dados ligados à VM alterando uma única linha de código. Por exemplo, para expandir o primeiro disco de dados ligado à VM, substitua o ```OSDisk``` objeto do ```StorageProfile``` com a ```DataDisks``` matriz e utilizar um indexador numérico para obter uma referência para o disco de dados anexados primeiro, conforme mostrado abaixo:

```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Da mesma forma, pode referenciar outros discos de dados ligados à VM, utilizando um índice, conforme mostrado acima ou a propriedade ```Name``` do disco conforme ilustrado abaixo:

```Powershell
($vm.StorageProfile.DataDisks | Where {$_.Name -eq 'my-second-data-disk'})[0].DiskSizeGB = 1023
```

Se quiser saber como anexar discos a uma VM do Azure Resource Manager, veja este [artigo](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

