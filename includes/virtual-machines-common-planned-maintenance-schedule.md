

## <a name="multi-and-single-instance-vms"></a>VMs de instância única e várias
Muitos clientes em execução no Azure contagem-crítico que pode agendar quando as respetivas VMs são submetidos a manutenção planeada devido a período de indisponibilidade – cerca de 15 minutos - que ocorre durante a manutenção. Pode utilizar conjuntos de disponibilidade para ajudar a controlar quando VMs aprovisionadas receberem manutenção planeada.

Existem duas configurações possíveis para VMs em execução no Azure. VMs ou estão configuradas como múltiplas instâncias ou de instância única. Se as VMs estão num conjunto de disponibilidade, em seguida, estes estão configurados como várias instâncias. Tenha em atenção, mesmo único VMs podem ser implementadas num conjunto de disponibilidade, para que são tratadas como várias instâncias. Se não forem VMs num conjunto de disponibilidade, em seguida, estes estão configurados como instância única.  Para obter detalhes sobre conjuntos de disponibilidade, consulte [gerir a disponibilidade das máquinas virtuais Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [gerir a disponibilidade das máquinas virtuais Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Atualizações de manutenção planeada para VMs de instância única e várias instâncias acontecer em separado. Ao reconfigurar as suas VMs ser instância única (se estiverem várias instâncias) ou ser várias instâncias (se estiverem de instância única), pode controlar quando as respetivas VMs receberem a manutenção planeada. Consulte [planeada manutenção de máquinas virtuais do Linux do Azure](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [planeada manutenção de máquinas virtuais do Azure Windows](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter detalhes sobre a manutenção planeada para VMs do Azure.

## <a name="for-multi-instance-configuration"></a>Para a configuração de várias instâncias
Pode selecionar a hora de manutenção planeada afeta as VMs que são implementadas numa configuração de um conjunto de disponibilidade, removendo estas VMs de conjuntos de disponibilidade.

1. Uma mensagem de e-mail é enviada sete dias do calendário antes da manutenção planeada para as VMs numa configuração de várias instâncias. Os IDs de subscrição e os nomes das VMs de várias instâncias afetados estão incluídos no corpo da mensagem de e-mail.
2. Durante a esses sete dias, pode escolher o tempo que as instâncias são atualizadas, removendo as VMs de várias instâncias nessa região do seu conjunto de disponibilidade. Esta alteração na configuração faz com que um reinício, como a Máquina Virtual está a mover de um anfitrião físico, direcionado para a manutenção, para outro anfitrião físico que não está direcionada para manutenção.
3. Pode remover a VM do respetiva conjunto de disponibilidade no portal do Azure.

   1. No portal, selecione a VM para remover o conjunto de disponibilidade.  

   2. Em **definições**, clique em **do conjunto de disponibilidade**.

      ![Seleção de conjunto de disponibilidade](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. Na conjunto de disponibilidade menu pendente, selecione "Não faz parte de um conjunto de disponibilidade."

      ![Remover conjunto](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. Na parte superior, clique em **guardar**. Clique em **Sim** reconhecer que esta ação reinicia a VM.

   >[!TIP]
   >Pode reconfigurar a VM para várias instâncias mais tarde, selecionando um dos conjuntos de disponibilidade listadas.

4. VMs removidas conjuntos de disponibilidade são movidas para anfitriões de instância única e não são atualizadas durante a manutenção planeada para configurações de conjunto de disponibilidade.
5. Assim que a atualização para VMs do conjunto de disponibilidade estiver concluída (de acordo com a agenda descrita no e-mail original), deve adicionar as VMs com os respetivos conjuntos de disponibilidade. Tornar-se parte de um conjunto de disponibilidade reconfigura VMs como várias instâncias e resulta no reinício do sistema. Normalmente, depois de concluídas todas as atualizações de várias instâncias em todo o ambiente do Azure, a manutenção de instância única segue.

Remover uma VM a partir de um conjunto de disponibilidade pode também ser possível utilizar o Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Para a configuração de instância única
Pode selecionar a hora de manutenção planeada afeta a VMs numa configuração de instância única adicionando estas VMs em conjuntos de disponibilidade.

Passo-a-passo

1. Uma mensagem de e-mail é enviada sete dias do calendário antes da manutenção planeada para as VMs numa configuração de instância única. Os IDs de subscrição e os nomes das VMs de instância única afetados estão incluídos no corpo da mensagem de e-mail.
2. Durante a esses sete dias, pode escolher o tempo que a instância é reiniciado, adicionando as VMs de instância única para um conjunto de disponibilidade nessa mesma região. Esta alteração na configuração faz com que um reinício, como a Máquina Virtual está a mover de um anfitrião físico, direcionado para a manutenção, para outro anfitrião físico que não está direcionada para manutenção.
3. Siga as instruções para adicionar VMs existentes em conjuntos de disponibilidade com o portal do Azure e o Azure PowerShell. (Consulte o exemplo do PowerShell do Azure que se segue estes passos.)
4. Depois destas VMs são reconfigurados como várias instâncias, estes são excluídos a manutenção planeada para VMs de instância única.
5. Uma vez concluída a atualização VM de instância única (de acordo com a agenda do e-mail original), pode devolver as VMs para a instância única removendo as VMs dos respetivos conjuntos de disponibilidade.

A adição de uma VM para um conjunto também de disponibilidade pode ser conseguida com o Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
