Pode ligar a uma VM que é implementada nos VNet criando uma Ligação de Ambiente de Trabalho Remoto para a VM. A melhor forma de verificar, inicialmente, que se pode ligar à VM é ligar-se utilizando o respetivo endereço IP privado, em vez do nome do computador. Dessa forma, está a testar a possibilidade de ligação, não se a resolução de nomes está corretamente configurada.

1. **Localizar o endereço IP privado.** Pode encontrar o endereço IP privado de uma VM observando as propriedades da VM no portal do Azure ou utilizando o PowerShell.

  - Portal do Azure - Localizar a máquina virtual no Portal do Azure. Ver as propriedades da VM. O endereço IP privado está listado.

  - PowerShell - Utilize o exemplo para ver uma lista de VMs e endereços IP privados a partir de grupos de recursos. Não é necessário modificar este exemplo antes de o utilizar.

    ```powershell
    $vms = get-azurermvm
    $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null

    foreach($nic in $nics)
    {
      $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
      $prv = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
      $alloc = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($vm.Name): $prv,$alloc"
    }
    ```

2. **Ligar à VM.** Certifique-se de que está ligado ao VNet utilizando a ligação VPN. Abrir a Ligação de Ambiente de Trabalho Remoto escrevendo "RDP" ou "Ligação de Ambiente de Trabalho Remoto" na caixa de pesquisa da barra de tarefas e, em seguida, selecione a Ligação de Ambiente de Trabalho Remoto. Também pode abrir a Ligação de Ambiente de Trabalho Remoto utilizando o comando `mstsc` do PowerShell. Na Ligação de Ambiente de Trabalho Remoto, introduza o endereço IP privado da VM. Pode clicar em "Mostrar Opções" para ajustar as definições adicionais e, em seguida, em ligar.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para resolver problemas de ligação RDP numa VM

Se estiver a ter problemas para estabelecer ligação a uma máquina virtual através da ligação VPN, existem algumas coisas que pode verificar.

- Certifique-se de que a ligação VPN é efetuada com êxito.
- Certifique-se de que está a ligar-se ao endereço IP privado da VM.
- Caso consiga ligar-se à VM utilizando o endereço IP privado, mas não o nome do computador, certifique-se de que configurou o DNS corretamente.
- Veja [Resolução de Problemas de ligações de Ambiente de Trabalho Remoto a uma VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md) para obter mais informações.