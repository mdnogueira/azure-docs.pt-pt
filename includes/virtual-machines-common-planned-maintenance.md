Azure executa periodicamente atualizações para melhorar a fiabilidade, desempenho e segurança da infraestrutura de anfitrião para máquinas virtuais. Estes intervalo de atualizações de componentes de software no ambiente de alojamento (como o sistema operativo, hipervisor e agentes vários implementados no anfitrião), a aplicação de patches atualizar componentes de rede, a desativação de hardware. A maioria destas atualizações são efetuadas sem qualquer impacto para as máquinas virtuais alojadas. No entanto, há casos em que as atualizações de ter um impacto:

- Se a manutenção não requer um reinício, o Azure utiliza migração no local para colocar em pausa a VM enquanto o anfitrião está atualizado.

- Se a manutenção requer um reinício, receberá um aviso quando a manutenção planeada. Nestes casos, é-irá também ser dada uma janela de tempo em que pode iniciar a manutenção por si, cada vez que funciona para si.

Esta página descreve a forma como o Microsoft Azure executa ambos os tipos de manutenção. Para mais informações sobre eventos não planeadas (falhas), consulte o artigo sobre como gerir a disponibilidade das máquinas virtuais para [Windows] (../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

As aplicações em execução numa máquina virtual podem recolher informações sobre as futuras atualizações ao utilizar o serviço de metadados do Azure para [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) ou [Linux] (../articles/virtual-machines/linux/instance-metadata-service.md).

Para obter informações "procedimentos" na gestão maintence planeada, consulte "Planeada de processamento de notificações de manutenção" para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="in-place-vm-migration"></a>Migração de VM no local

Quando as atualizações não necessitam de um reinício total, é utilizada uma migração em direto no local. Durante a atualização, a máquina virtual é interrompida durante cerca de 30 segundos, preservando a memória RAM, enquanto o ambiente de alojamento aplica-se as atualizações necessárias e correções de erros. A máquina virtual, em seguida, é retomada e o relógio da máquina virtual é automaticamente sincronizado.

Para VMs em conjuntos de disponibilidade, domínios de atualização são atualizado um de cada vez. Todas as VMs no domínio de uma atualização (UD) são colocada em pausa, atualizadas e, em seguida, foi retomadas antes da manutenção planeada passa para a seguinte UD.

Algumas aplicações podem ser afetadas por estes tipos de atualizações. As aplicações que executam em tempo real de processamento, como o suporte de dados de transmissão em fluxo ou transcodificação ou débito elevado cenários, de rede de eventos não podem ser concebidas para tolerar uma pausa segundo 30. <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>Exigir um reinício de manutenção

Quando as VMs têm de ser reiniciado para manutenção planeada, será notificado com antecedência. Manutenção planeada tem duas fases: a janela de self-service e uma janela de manutenção agendada.

O **self-service janela** permite-lhe iniciar manutenção nas suas VMs. Durante este tempo, pode consultar cada VM para ver o respetivo estado e verificar o resultado da sua última pedido de manutenção.

Quando inicia a manutenção de self-service, a VM é movida para um nó que já foi atualizado e, em seguida, for ligado-lo novamente. Porque a VM é reiniciado, o disco temporário é perdido e endereços IP dinâmicos associados à interface de rede virtual são atualizados.

Se iniciar manutenção self-service e existir um erro durante o processo, a operação está parada, a VM não é atualizada e também é removido da iteração de manutenção planeada. Irá ser contactado num horário posterior, com uma nova agenda e oferecido uma nova oportunidade de fazer a manutenção de self-service. 

Depois da janela de self-service, o **janela de manutenção agendada** começa. Durante este período de tempo, pode ainda de consulta para a janela de manutenção, mas já não será possível iniciar a manutenção de si próprio.

## <a name="availability-considerations-during-planned-maintenance"></a>Considerações de disponibilidade durante a manutenção planeada 

Se optar por Aguarde até que a janela de manutenção planeada, existem alguns aspetos a considerar para manter o availabilty mais elevado das suas VMs. 

### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure se encontra emparelhado com outro região dentro da mesma geografia, em conjunto que efetuam um par regional. Durante a manutenção planeada, o Azure irá atualizar apenas as VMs numa única região de um par de região. Por exemplo, ao atualizar as máquinas virtuais nos E.U.A. Centro-Norte, o Azure não atualiza quaisquer máquinas virtuais nos E.U.A. Centro-Sul ao mesmo tempo. No entanto, outras regiões, como a Europa do Norte, podem estar sob manutenção em simultâneo como os E.U.A. Leste. Compreender como funcionam os pares de região pode ajudar a distribuir melhor as suas VMs em regiões. Para obter mais informações, consulte [pares de região do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Quando implementar uma carga de trabalho em VMs do Azure, pode criar as VMs dentro de um conjunto de disponibilidade para fornecer elevada disponibilidade à sua aplicação. Isto assegura que durante a eventos de uma interrupção ou a manutenção, pelo menos uma máquina virtual está disponível.

Dentro de um conjunto de disponibilidade, VMs individuais são distribuídas por até 20 domínios de atualização (UDs). Durante a manutenção planeada, apenas um domínio com uma única atualização é afetado em qualquer momento. Lembre-se de que a ordem dos domínios de atualização que está a ser afetado não necessariamente acontecer sequencialmente. 

Conjuntos de dimensionamento de máquina virtual são um recurso de computação do Azure que lhe permite implementar e gerir um conjunto de VMs idênticas, como um único recurso. O conjunto de dimensionamento é implementado automaticamente entre domínios de atualização, como VMs num conjunto de disponibilidade. Tal como com conjuntos de disponibilidade, com conjuntos de dimensionamento de apenas um domínio com uma única atualização é afetado em qualquer momento.

Para obter mais informações sobre como configurar as máquinas virtuais de elevada disponibilidade, consulte o artigo sobre como gerir a disponibilidade das máquinas virtuais para o Windows (../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).