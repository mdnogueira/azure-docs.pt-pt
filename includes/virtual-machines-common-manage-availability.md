## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Compreender os Reinícios da VM - manutenção vs. período de indisponibilidade
Existem três cenários que podem conduzir a máquina virtual no Azure a ser afetado: manutenção de hardware não planeada, tempo de inatividade inesperado e a manutenção planeada.

* Um **Evento de Manutenção de Hardware Não Planeada** ocorre quando a plataforma Azure prevê que o hardware ou um componente da plataforma associado a uma máquina física está prestes a falhar. Quando a plataforma prevê uma falha, emite um evento de manutenção de hardware não planeada para reduzir o impacto sobre as máquinas virtuais alojadas nesse hardware. O Azure utiliza a tecnologia de Migração em Direto para migrar as Máquinas Virtuais do hardware com falhas para uma máquina física em bom estado de funcionamento. A Migração em Direto é uma operação que visa conservar a VM e que apenas coloca a Máquina Virtual em pausa durante um curto espaço de tempo. A memória, os ficheiros abertos e as ligações de rede são mantidos, mas pode ocorrer uma redução do desempenho antes e/ou depois do evento. Nos casos em que não é possível utilizar a Migração em Direto, a VM sofre um Período de Indisponibilidade Inesperado, conforme descrito abaixo.


* É muito raro ocorrer um **Período de Indisponibilidade Inesperado** quando o hardware ou a infraestrutura física subjacente à máquina virtual sofre algum tipo de falha. Isto pode incluir falhas de rede local, falhas de disco local ou outras falhas estruturais. Quando é detetada uma falha de tal, migra automaticamente a plataforma do Azure (heals) a máquina virtual para uma bom estado de funcionamento máquina física no mesmo datacenter. Durante o procedimento de recuperação, as máquinas virtuais sofrem um período de indisponibilidade (reinício) e, em alguns casos, a perda da unidade temporária. O SO anexado e os discos de dados são sempre preservados. 

  Máquinas virtuais pode também tempo de inatividade na improvável eventualidade de uma falha ou desastre que afeta a todo o datacenter ou mesmo uma região de toda. Nestes cenários, o Azure oferece opções de proteção, incluindo [zonas de disponibilidade](../articles/availability-zones/az-overview.md) e [emparelhado regiões](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* Os **eventos de Manutenção Planeada** são atualizações periódicas levadas a cabo pela Microsoft na plataforma Azure subjacente para melhorar a fiabilidade, o desempenho e a segurança gerais da infraestrutura da plataforma em que as suas máquinas virtuais são executadas. A maior parte destas atualizações são realizadas sem exercer qualquer impacto nas suas Máquinas Virtuais ou Serviços Cloud (consulte [Manutenção de Conservação de VMs](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/preserving-maintenance)). Ainda que a plataforma Azure tente utilizar a Manutenção de Conservação de VMs em todas as ocasiões possíveis, há algumas situações raras em que estas atualizações precisam de um reinício da máquina virtual para aplicar as atualizações necessárias à infraestrutura subjacente. Neste caso, pode realizar a Manutenção Planeada do Azure com a operação de Reimplementação da Manutenção dando início à manutenção das respetivas VMs no intervalo de tempo adequado. Para obter mais informações, consulte [Manutenção Planeada para Máquinas Virtuais](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/planned-maintenance/).


Para reduzir o impacto do período de indisponibilidade devido a um ou mais destes eventos, recomendamos as seguintes melhores práticas de elevada disponibilidade para as máquinas virtuais:

* [Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]
* [Utilizar discos geridos para VMs num conjunto de disponibilidade]
* [Utilizar Eventos Agendados para responder de forma proativa a eventos com repercussões na VM] (https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configurar cada camada da aplicação em conjuntos de disponibilidade separados]
* [Combinar um Balanceador de Carga com conjuntos de disponibilidade]
* [Zonas de disponibilidade de utilização para proteger contra falhas de nível de centro de dados]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância
Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Esta configuração num centro de dados garante que, durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e cumpre 99,95% SLA do Azure. Para obter mais informações, veja [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evite deixar isolada uma máquina virtual de instância única num conjunto de disponibilidade. As VMs nesta configuração não se qualificam para uma garantia SLA e passam por um período de indisponibilidade durante eventos de manutenção planeada do Azure, exceto quando uma VM única está a utilizar [Armazenamento Premium do Azure](../articles/virtual-machines/windows/premium-storage.md). Para VMs únicas que utilizam o armazenamento premium, aplica-se o SLA do Azure.

A cada máquina virtual no seu conjunto de disponibilidade é atribuído um **domínio de atualização** e um **domínio de falha** pela plataforma Azure subjacente. Para um conjunto de disponibilidade especificado, cinco domínios de atualização não configuráveis pelo utilizador são atribuídos por predefinição (as implementações do Resource Manager podem então ser aumentadas para fornecer até 20 domínios de atualização), para indicar os grupos de máquinas virtuais e o hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Quando são configuradas mais de cinco máquinas virtuais num conjunto de disponibilidade único, a sexta máquina virtual é colocada no mesmo domínio de atualização da primeira máquina virtual, a sétima no mesmo domínio de atualização da segunda máquina virtual, e assim sucessivamente. A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez. Um domínio de atualização reiniciado dispõe de 30 minutos para realizar a recuperação antes de a manutenção ser iniciada num domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física. Por predefinição, as máquinas virtuais configuradas no seu conjunto de disponibilidade estão separadas através de até três domínios de falha para implementações do Resource Manager (dois domínios de falha para o Clássico). Embora a colocação de máquinas virtuais num conjunto de disponibilidade não proteja a sua aplicação de falhas específicas do sistema operativo ou da aplicação, limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

<!--Image reference-->
   ![Desenho conceptual da configuração do domínio de falha e do domínio de atualização](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Utilizar discos geridos para VMs num conjunto de disponibilidade
Se, neste momento, utiliza VMs com discos não geridos, recomendamos vivamente que [converta as VMs no Conjunto de Disponibilidade de modo a utilizarem Discos Geridos](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

Os [discos geridos](../articles/virtual-machines/windows/managed-disks-overview.md) proporcionam uma melhor fiabilidade para os Conjuntos de Disponibilidade ao garantir que os discos das VMs num Conjunto de Disponibilidade estão suficientemente isolados uns dos outros a fim de evitar pontos de falha únicos. Para tal, os discos são colocados automaticamente em clusters de armazenamento diferentes. Se um cluster de armazenamento falhar devido uma falha de hardware ou software, apenas irão falhar as instâncias de VM com discos nesses carimbos.

![Domínios de Falhas de Discos Geridos](./media/virtual-machines-common-manage-availability/md-fd.png)

> [!IMPORTANT]
> O número de domínios de falhas para os conjuntos de disponibilidade geridos varia consoante a região, dois ou três por região. A tabela seguinte mostra o número por região

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Se planeia utilizar as VMs com [discos não geridos](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks), siga as melhores práticas indicadas abaixo para as Contas de armazenamento nas quais os discos rígidos virtuais (VHDs) das VMs são armazenados como [blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Manter todos os discos (SO e dados) associados a uma VM na mesma conta de armazenamento**
2. **Rever os [limites](../articles/storage/common/storage-scalability-targets.md) do número de discos não geridos numa Conta de armazenamento**  antes de adicionar mais VHDs a uma conta de armazenamento
3. **Utilize uma conta de armazenamento separada para cada VM num Conjunto de Disponibilidade.** Não partilhe Contas de armazenamento com várias VMs no mesmo Conjunto de Disponibilidade. A partilha de contas de armazenamento entre VMs de Conjuntos de Disponibilidade diferentes é aceitável desde que as melhores práticas anteriores sejam seguidas

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configurar cada camada da aplicação em conjuntos de disponibilidade separados
Se as máquinas virtuais forem todas praticamente idênticas e servirem o mesmo fim para a sua aplicação, recomendamos que configure um conjunto de disponibilidade para cada camada da aplicação.  Se colocar duas camadas diferentes no mesmo conjunto de disponibilidade, todas as máquinas virtuais da mesma camada de aplicação podem ser reiniciadas ao mesmo tempo. Ao configurar, pelo menos, duas máquinas virtuais num conjunto de disponibilidade para cada camada, garante que, pelo menos, uma máquina virtual em cada camada está disponível.

Por exemplo, poderia colocar todas as máquinas virtuais no front-end da sua aplicação com o IIS, Apache e Nginx num único conjunto de disponibilidade. Certifique-se de que apenas as máquinas virtuais front-end são colocadas no mesmo conjunto de disponibilidade. Da mesma forma, certifique-se de que apenas as máquinas virtuais de camadas de dados são colocadas no seu próprio conjunto de disponibilidade, como as máquinas virtuais replicadas do SQL Server ou as máquinas virtuais MySQL.

<!--Image reference-->
   ![Camadas da aplicação](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinar um balanceador de carga com conjuntos de disponibilidade
Combine o [Balanceador de Carga do Azure](../articles/load-balancer/load-balancer-overview.md) com um conjunto de disponibilidade para obter a máxima resiliência da aplicação. O Balanceador de Carga do Azure distribui tráfego entre várias máquinas virtuais. Para as nossas máquinas virtuais de camada Standard, o Balanceador de Carga do Azure está incluído. Nem todas as camadas de máquina virtual incluem o Balanceador de Carga do Azure. Para mais informações sobre o balanceamento de carga de máquinas virtuais, veja [Balanceamento de Carga de máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se o balanceador de carga não estiver configurado para balancear tráfego em várias máquinas virtuais, então qualquer evento de manutenção planeada afeta apenas a máquina virtual que serve o tráfego, provocando uma indisponibilidade na camada da aplicação. Colocar várias máquinas virtuais da mesma camada no mesmo balanceador de carga e conjunto de disponibilidade permite que o tráfego seja servido continuamente por, pelo menos, uma instância.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Utilize as zonas de disponibilidade para proteger contra falhas de nível de centro de dados

[Zonas de disponibilidade](../articles/availability-zones/az-overview.md) (pré-visualização), uma alternativa à disponibilidade define, expanda o nível de controlo tem de manter a disponibilidade das aplicações e dados nas suas VMs. Uma zona de disponibilidade é uma zona fisicamente separada dentro de uma região do Azure. Existem três zonas de disponibilidade por região do Azure suportada. Cada zona de disponibilidade tem um distintos origem, rede e arrefecimento de energia e são logicamente separados de outros zonas de disponibilidade na região do Azure. Por arquitetar as suas soluções para utilizar VMs replicadas em zonas, pode proteger as aplicações e dados da perda de um centro de dados. Se uma zona for comprometida, em seguida, replicadas aplicações e dados estão disponíveis de imediato noutra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Saiba mais sobre a implementação de um [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM com uma zona de disponibilidade.


<!-- Link references -->
[Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurar cada camada da aplicação em conjuntos de disponibilidade separados]: #configure-each-application-tier-into-separate-availability-sets
[Combinar um Balanceador de Carga com conjuntos de disponibilidade]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Utilizar discos geridos para VMs num conjunto de disponibilidade]: #use-managed-disks-for-vms-in-an-availability-set
