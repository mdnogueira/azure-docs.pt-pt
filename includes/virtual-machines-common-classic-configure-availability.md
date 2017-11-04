


Um conjunto de disponibilidade ajuda a manter as máquinas virtuais disponíveis durante o período de inatividade, tal como durante a manutenção. Colocar duas ou mais máquinas virtuais da mesma forma configuradas num conjunto de disponibilidade cria a redundância necessária para manter a disponibilidade de aplicações ou serviços que executa a máquina virtual. Para obter mais informações sobre como isto funciona, consulte [gerir a disponibilidade das máquinas virtuais][Manage the availability of virtual machines].

É uma melhor prática para utilizar conjuntos de disponibilidade e de pontos finais de balanceamento de carga para ajudar a garantir que a aplicação está sempre disponível e em execução com eficiência. Para obter detalhes sobre os pontos finais com balanceamento de carga, consulte [para serviços de infraestrutura do Azure de balanceamento de carga][Load balancing for Azure infrastructure services].

Pode adicionar as máquinas virtuais clássicas para um conjunto, utilizando uma das duas opções de disponibilidade:

* [Opção 1: Criar uma máquina virtual e um conjunto ao mesmo tempo de disponibilidade][Option 1: Create a virtual machine and an availability set at the same time]. Em seguida, adicione novas máquinas virtuais para o conjunto ao criar essas máquinas virtuais.
* [Opção 2: Adicionar uma máquina virtual existente para um conjunto de disponibilidade][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> No modelo clássico, as máquinas virtuais que pretende colocar no mesmo conjunto de disponibilidade tem de pertencer ao mesmo serviço em nuvem.
> 
> 

## <a id="createset"></a>Opção 1: criar uma máquina virtual e um conjunto ao mesmo tempo de disponibilidade
Pode utilizar o portal do Azure ou os comandos do PowerShell do Azure para efetuar este procedimento.

Para utilizar o portal do Azure:

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No hub menu, clique em **+ novo**e, em seguida, clique em **Máquina Virtual**.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)
3. Selecione a imagem de máquina virtual do Marketplace que pretende utilizar. Pode optar por criar uma máquina virtual Linux ou do Windows.
4. Para a máquina virtual selecionada, certifique-se de que o modelo de implementação está definido como **clássico** e, em seguida, clique em **criar**
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Introduza um nome da máquina virtual, o nome de utilizador e a palavra-passe (para máquinas do Windows) ou a chave pública SSH (para computadores Linux). 
6. Escolha o tamanho da VM e, em seguida, clique em **selecione** para continuar.
7. Escolha **configuração opcional > do conjunto de disponibilidade**, e selecione o conjunto de disponibilidade pretende adicionar a máquina virtual.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Reveja as definições de configuração. Quando tiver terminado, clique em **criar**.
9. Enquanto o Azure cria a máquina virtual, pode acompanhar o progresso em **máquinas virtuais** no hub menu.

Para utilizar comandos do PowerShell do Azure para criar uma máquina virtual do Azure e adicioná-lo a um conjunto de disponibilidade novo ou existente, consulte [utilizar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas no Windows](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"></a>Opção 2: adicionar uma máquina virtual existente para um conjunto de disponibilidade
No portal do Azure, pode adicionar clássicas das máquinas virtuais existentes para uma disponibilidade existente, definir ou crie um novo para os mesmos. (Tenha em atenção que as máquinas virtuais no mesmo conjunto de disponibilidade tem de pertencer ao mesmo serviço em nuvem.) Os passos são quase os mesmos. Com o Azure PowerShell, pode adicionar a máquina virtual a um conjunto de disponibilidade existente.

1. Se ainda não o fez, inicie sessão no [portal do Azure](https://portal.azure.com).
2. No Hub menu, clique em **máquinas virtuais (clássicas)**.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Na lista de máquinas virtuais, selecione o nome da máquina virtual que pretende adicionar ao conjunto.
4. Escolha **do conjunto de disponibilidade** da máquina virtual **definições**.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Selecione o conjunto de disponibilidade que pretende adicionar a máquina virtual. A máquina virtual tem de pertencer ao mesmo serviço em nuvem como o conjunto de disponibilidade.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Clique em **Guardar**.

Para utilizar comandos do PowerShell do Azure, abra uma sessão do PowerShell do Azure de nível de administrador e execute o seguinte comando. Para os marcadores de posição (tais como &lt;VmCloudServiceName&gt;), substituir tudo dentro de aspas, incluindo os < e > carateres, com os nomes corretos.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> A máquina virtual poderá ter de ser reiniciado para concluir a adicionar ao conjunto de disponibilidade.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

