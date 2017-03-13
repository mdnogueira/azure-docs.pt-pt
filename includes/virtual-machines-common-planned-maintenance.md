

## <a name="memory-preserving-updates"></a>Atualizações para preservação de memória
Para uma classe de atualizações no Microsoft Azure, os clientes não podem visualizar eventuais impactos nas respetivas máquinas virtuais em execução. Muitas destas atualizações são componentes ou serviços que podem ser atualizados sem interferir com a instância em execução. Algumas destas atualizações são atualizações de infraestrutura da plataforma do sistema operativo do anfitrião, que podem ser aplicadas sem necessidade de um reinício total das máquinas virtuais.

Estas atualizações são efetuadas com tecnologia que permite a migração em direto no local, também designada por uma atualização para “preservação de memória”. Ao atualizar, a máquina virtual é colocada num estado “em pausa”, preservando a memória RAM, enquanto o sistema operativo subjacente do anfitrião recebe as atualizações e correções necessárias. A máquina virtual é retomada dentro de 30 segundos após ter sido colocada em pausa. Após retomar, o relógio da máquina virtual é sincronizado automaticamente.

Nem todas as atualizações podem ser implementadas com este mecanismo mas, dado o curto período de pausa, implementar atualizações desta forma reduz consideravelmente o impacto nas máquinas virtuais.

As atualizações de várias instâncias (para máquinas virtuais num conjunto de disponibilidade) são aplicadas num domínio de atualização de cada vez.  

## <a name="virtual-machine-configurations"></a>Configurações de máquina virtual
Existem dois tipos de configuração de máquinas virtuais: várias instâncias e instância única. Numa configuração de várias instâncias, as máquinas virtuais semelhantes são colocadas num conjunto de disponibilidade.

A configuração de várias instâncias fornece redundância entre máquinas físicas, energia e rede, e é recomendada para garantir a disponibilidade da aplicação. Todas as máquinas virtuais no conjunto de disponibilidade devem servir o mesmo fim para a aplicação.

Para obter mais informações sobre como configurar máquinas virtuais para elevada disponibilidade, veja [Gerir a disponibilidade das máquinas virtuais do Windows](../articles/virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Gerir a disponibilidade das máquinas virtuais do Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Por outro lado, uma configuração de instância única é utilizada para máquinas virtuais autónomas que não são colocadas num conjunto de disponibilidade. Estas máquinas virtuais não se qualificam para o contrato de nível de serviço (SLA), que requer a implementação de duas ou mais máquinas virtuais no mesmo conjunto de disponibilidade.

Para mais informações sobre SLAs, veja as secções “Serviços Cloud e Máquinas Virtuais” de [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="multi-instance-configuration-updates"></a>Atualizações de configuração de várias instâncias
Durante a manutenção planeada, a plataforma Azure atualiza primeiro o conjunto das máquinas virtuais que estão alojadas numa configuração de várias instâncias. A atualização dá origem a um reinício destas máquinas virtuais, com cerca de 15 minutos de indisponibilidade.

Uma atualização de configuração de várias instâncias parte do princípio que cada máquina virtual serve uma função semelhante às outras no conjunto de disponibilidade. Nesta definição, as máquinas virtuais são atualizadas de forma a preservar a disponibilidade ao longo do processo.

A cada máquina virtual num conjunto de disponibilidade é atribuído um domínio de atualização e um domínio de falha pela plataforma Azure subjacente. Cada domínio de atualização é um grupo de máquinas virtuais que irão ser reiniciadas na mesma janela de tempo. Cada domínio de falha é um grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física.


Para obter mais informações sobre domínios de atualização e domínios de falha, veja [Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância](../articles/virtual-machines/virtual-machines-windows-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Para manter a disponibilidade durante uma atualização, o Azure efetua a manutenção por domínio de atualização, atualizando um domínio de cada vez. A manutenção de um domínio de atualização consiste em encerrar cada máquina virtual no domínio, aplicar a atualização às máquinas do anfitrião e, em seguida, reiniciar as máquinas virtuais. Quando tiver concluído a manutenção do domínio, o Azure repete o processo com o domínio de atualização seguinte e continua com cada domínio, até que todos estejam atualizados.

A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez. Atualmente, o Azure oferece 1 semana de notificação prévia para a manutenção planeada de máquinas virtuais na configuração de várias instâncias.

Após o restauro de uma máquina virtual, segue-se um exemplo do que o seu Visualizador de Eventos do Windows poderá apresentar:

<!--Image reference-->
![][image2]


Utilize o visualizador para comunicar as máquinas virtuais que estão configuradas numa configuração de várias instâncias através do portal do Azure, do Azure PowerShell ou da CLI do Azure. Por exemplo, com o portal do Azure, pode adicionar o _Conjunto de Disponibilidade_ à caixa de diálogo **máquinas virtuais (clássico)**. As máquinas virtuais que comunicam o mesmo conjunto de disponibilidade fazem parte de uma configuração de várias instâncias. No exemplo seguinte, a configuração de várias instâncias consiste em máquinas virtuais SQLContoso01 e SQLContoso02.

<!--Image reference-->
  ![Vista (clássica) de máquinas virtuais a partir do portal do Azure][image4]

## <a name="single-instance-configuration-updates"></a>Atualizações de configuração de instância única
Depois de as atualizações de configuração de várias instâncias estarem concluídas, o Azure efetua atualizações de configuração de instância única. Estas atualizações também dão origem a reinícios nas máquinas virtuais que não estão em execução nos conjuntos de disponibilidade.

> [!NOTE]
> Se um conjunto de disponibilidade tiver apenas uma instância de máquina virtual em execução, a plataforma Azure trata-a como uma atualização da configuração de várias instâncias.
>

A manutenção numa configuração de instância única consiste em encerrar cada máquina virtual em execução num computador anfitrião, atualizar a máquina do anfitrião e, em seguida, reiniciar as máquinas virtuais. A manutenção requer cerca de 15 minutos de indisponibilidade. O evento de manutenção planeada é executado em todas as máquinas virtuais numa região, numa janela de manutenção única.


Os eventos de manutenção planeada têm impacto na disponibilidade da aplicação para configurações de instância única. O Azure oferece&1; semana de notificação prévia para a manutenção planeada de máquinas virtuais em configurações de instância única.

## <a name="email-notification"></a>Notificação por e-mail
Apenas no caso de configurações de máquinas virtuais de instância única e várias instâncias, o Azure envia um alerta por e-mail da futura manutenção planeada (com uma semana de antecedência). Esta mensagem é enviada para as contas de e-mail de administrador e coadministrador da subscrição. Segue-se um exemplo deste tipo de alerta:

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>Pares de região

Ao executar a manutenção, o Azure atualiza apenas as instâncias de Máquina Virtual numa única região do respetivo par. Por exemplo, ao atualizar as máquinas virtuais nos E.U.A. Centro-Norte, o Azure não atualiza quaisquer máquinas virtuais nos E.U.A. Centro-Sul ao mesmo tempo. Isto será agendado num momento separado, ao ativar a ativação pós-falha ou com balanceamento de carga entre regiões. No entanto, outras regiões, como a Europa do Norte, podem estar sob manutenção em simultâneo como os E.U.A. Leste.

Consulte a tabela seguinte para pares da região atual:

| Região 1 | Região 2 |
|:--- | ---:|
| EUA Leste |EUA Oeste |
| EUA Leste 2 |EUA Central |
| EUA Centro-Norte |EUA Centro-Sul |
| EUA Centro-Oeste |EUA Oeste 2 |
| Leste do Canadá |Canadá Central |
| Sul do Brasil |EUA Centro-Sul |
| Gov (US) - Iowa |Gov (US) - Virginia |
| US DoD Leste |US DoD Centro |
| Europa do Norte |Europa Ocidental |
| Reino Unido Oeste |Reino Unido Sul |
| Alemanha Central |Alemanha Nordeste |
| Sudeste Asiático |Ásia Oriental |
| Sudeste da Austrália |Leste da Austrália |
| Índia Central |Índia do Sul |
| Índia Ocidental |Índia do Sul |
| Leste do Japão |Oeste do Japão |
| Coreia Central |Coreia do Sul |
| Leste da China |Norte da China |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
