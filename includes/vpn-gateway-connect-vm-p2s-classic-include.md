Pode ligar a uma VM que é implementada nos VNet criando uma Ligação de Ambiente de Trabalho Remoto para a VM. A melhor forma de verificar, inicialmente, que se pode ligar à VM é ligar-se utilizando o respetivo endereço IP privado, em vez do nome do computador. Dessa forma, está a testar a possibilidade de ligação, não se a resolução de nomes está corretamente configurada. 

1. Localizar o endereço IP privado para a sua VM. Pode encontrar o endereço IP privado de uma VM observando as propriedades da VM no portal do Azure ou utilizando o PowerShell.
2. Certifique-se de que está ligado ao VNet utilizando a ligação VPN Ponto a Site. 
3. Abrir a Ligação de Ambiente de Trabalho Remoto escrevendo "RDP" ou "Ligação de Ambiente de Trabalho Remoto" na caixa de pesquisa da barra de tarefas e, em seguida, selecione a Ligação de Ambiente de Trabalho Remoto. Também pode abrir a Ligação de Ambiente de Trabalho Remoto utilizando o comando "mstsc" no PowerShell. 
3. Na Ligação de Ambiente de Trabalho Remoto, introduza o endereço IP privado da VM. Pode clicar em "Mostrar Opções" para ajustar as definições adicionais e, em seguida, em ligar.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para resolver problemas de ligação RDP numa VM

Se estiver a ter problemas para estabelecer ligação a uma máquina virtual através da ligação VPN, existem algumas coisas que pode verificar. Para obter mais informações sobre a resolução de problemas, veja [Troubleshoot Remote Desktop connections to a VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)(Resolução de Problemas de ligações de Ambiente de Trabalho Remoto a uma VM).

- Certifique-se de que a ligação VPN é efetuada com êxito.
- Certifique-se de que está a ligar-se ao endereço IP privado da VM.
- Utilize “ipconfig” para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual se está a ligar. Se o endereço IP estiver no âmbito do intervalo de endereços da VNet a que se está a ligar, ou no âmbito do intervalo de endereços do seu VPNClientAddressPool, tal trata-se de um espaço de endereços sobreposto. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.
- Caso consiga ligar-se à VM utilizando o endereço IP privado, mas não o nome do computador, certifique-se de que configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nomes para VMs, consulte o artigo [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)(Resolução de Nomes para VMs).
- Certifique-se de que o pacote de configuração de clientes VPN gerado depois dos endereços IP do servidor DNS foi especificado para a VNet. Se atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.