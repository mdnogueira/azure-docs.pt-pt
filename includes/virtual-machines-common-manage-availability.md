## <a name="understand-planned-vs-unplanned-maintenance"></a>Compreender a manutenção planeada vs. não planeada
Existem dois tipos de eventos da plataforma Microsoft Azure que podem afetar a disponibilidade das máquinas virtuais: a manutenção planeada e a manutenção não planeada.

* Os **eventos de manutenção planeada** são atualizações periódicas feitas pela Microsoft na plataforma Azure subjacente para melhorar a fiabilidade, o desempenho e a segurança gerais da infraestrutura da plataforma na qual as suas máquinas virtuais são executadas. A maior parte destas atualizações é efetuada sem nenhum impacto nas máquinas virtuais ou serviços cloud. No entanto, existem instâncias em que estas atualizações requerem um reinício da máquina virtual para aplicar as atualizações necessárias à infraestrutura da plataforma.
* Os **eventos de manutenção não planeada** ocorrem quando o hardware ou a infraestrutura física subjacente à máquina virtual falharam de alguma forma. Isto pode incluir falhas de rede local, falhas de disco local ou outras falhas estruturais. Quando uma falha deste tipo é detetada, a plataforma Azure efetua automaticamente a migração da máquina virtual da máquina física em mau estado de funcionamento que está a alojar a máquina virtual para uma máquina física em bom estado de funcionamento. Estes eventos são raros, mas também podem provocar o reinício da máquina virtual.

Para reduzir o impacto do período de indisponibilidade devido a um ou mais destes eventos, recomendamos as seguintes melhores práticas de elevada disponibilidade para as máquinas virtuais:

* [Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]
* [Configurar cada camada da aplicação em conjuntos de disponibilidade separados]
* [Combinar um Balanceador de Carga com conjuntos de disponibilidade]
* [Utilizar várias contas de armazenamento para cada conjunto de disponibilidade]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância
Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Esta configuração garante que, durante um evento de manutenção planeada ou não planeada, está disponível, pelo menos, uma máquina virtual que cumpre 99,95% do SLA do Azure. Para obter mais informações, veja [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evite deixar isolada uma máquina virtual de instância única num conjunto de disponibilidade. As VMs nesta configuração não se qualificam para uma garantia SLA e passam por um período de indisponibilidade durante eventos de manutenção planeada do Azure, exceto quando uma VM única está a utilizar [Armazenamento Premium do Azure](../articles/storage/storage-premium-storage.md). Para VMs únicas que utilizam o armazenamento premium, aplica-se o SLA do Azure.

A cada máquina virtual no seu conjunto de disponibilidade é atribuído um **domínio de atualização** e um **domínio de falha** pela plataforma Azure subjacente. Para um conjunto de disponibilidade especificado, cinco domínios de atualização não configuráveis pelo utilizador são atribuídos por predefinição (as implementações do Resource Manager podem então ser aumentadas para fornecer até 20 domínios de atualização), para indicar os grupos de máquinas virtuais e o hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Quando são configuradas mais de cinco máquinas virtuais num conjunto de disponibilidade único, a sexta máquina virtual é colocada no mesmo domínio de atualização da primeira máquina virtual, a sétima no mesmo domínio de atualização da segunda máquina virtual, e assim sucessivamente. A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez.

Os domínios de falha definem o grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física. Por predefinição, as máquinas virtuais configuradas no seu conjunto de disponibilidade estão separadas através de até três domínios de falha para implementações do Resource Manager (dois domínios de falha para o Clássico). Embora a colocação de máquinas virtuais num conjunto de disponibilidade não proteja a sua aplicação de falhas específicas do sistema operativo ou da aplicação, limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

<!--Image reference-->
   ![Desenho conceptual da configuração do domínio de falha e do domínio de atualização](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains-and-availability-sets"></a>Domínios de falha dos Managed Disks e conjuntos de disponibilidade
Para as VMs que utilizam os [Managed Disks do Azure](../articles/storage/storage-faq-for-disks.md), as VMs são alinhadas com domínios de falha de discos geridos ao utilizar um conjunto de disponibilidade gerido. Este alinhamento garante que todos os discos geridos ligados a uma VM estão dentro do mesmo domínio de falha do disco gerido. Apenas as VMs com discos geridos podem ser criadas num conjunto de disponibilidade gerido. O número de domínios de falha do disco gerido varia por região - dois ou três domínios de falha do disco gerido por região.


## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configurar cada camada da aplicação em conjuntos de disponibilidade separados
Se as máquinas virtuais forem todas praticamente idênticas e servirem o mesmo fim para a sua aplicação, recomendamos que configure um conjunto de disponibilidade para cada camada da aplicação.  Se colocar duas camadas diferentes no mesmo conjunto de disponibilidade, todas as máquinas virtuais da mesma camada de aplicação podem ser reiniciadas ao mesmo tempo. Ao configurar, pelo menos, duas máquinas virtuais num conjunto de disponibilidade para cada camada, garante que, pelo menos, uma máquina virtual em cada camada está disponível.

Por exemplo, pode incluir todas as máquinas virtuais no front-end da sua aplicação com o IIS, Apache, Nginx num único conjunto de disponibilidade. Certifique-se de que apenas as máquinas virtuais front-end são colocadas no mesmo conjunto de disponibilidade. Da mesma forma, certifique-se de que apenas máquinas virtuais de camadas de dados são colocadas no seu próprio conjunto de disponibilidade, como máquinas virtuais replicadas do SQL Server ou máquinas virtuais MySQL.

<!--Image reference-->
   ![Camadas da aplicação](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinar um balanceador de carga com conjuntos de disponibilidade
Combine o [Balanceador de Carga do Azure](../articles/load-balancer/load-balancer-overview.md) com um conjunto de disponibilidade para obter a máxima resiliência da aplicação. O Balanceador de Carga do Azure distribui tráfego entre várias máquinas virtuais. Para as nossas máquinas virtuais de camada Standard, o Balanceador de Carga do Azure está incluído. Nem todas as camadas de máquina virtual incluem o Balanceador de Carga do Azure. Para mais informações sobre o balanceamento de carga de máquinas virtuais, veja [Balanceamento de Carga de máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se o balanceador de carga não estiver configurado para balancear tráfego em várias máquinas virtuais, então qualquer evento de manutenção planeada afeta apenas a máquina virtual que serve o tráfego, provocando uma indisponibilidade na camada da aplicação. Colocar várias máquinas virtuais da mesma camada no mesmo balanceador de carga e conjunto de disponibilidade permite que o tráfego seja servido continuamente por, pelo menos, uma instância.

## <a name="use-multiple-storage-accounts-for-each-availability-set"></a>Utilizar várias contas de armazenamento para cada conjunto de disponibilidade
Se utilizar Managed Disks do Azure, pode ignorar as seguintes orientações. Os Managed Disks do Azure fornecem, inerentemente, elevada disponibilidade e redundância, uma vez que os discos são armazenados em domínios de falha que se alinham com os seus conjuntos de disponibilidade de VMs. Para obter mais informações,veja [Azure Managed Disks overview (Descrição geral dos Managed Disks do Azure)](../articles/storage/storage-managed-disks-overview.md).

Se estiver a utilizar discos não geridos, deve seguir as melhores práticas existentes relativas às contas de armazenamento utilizadas pelos discos rígidos virtuais (VHDs) dentro da VM. Cada disco (VHD) é um blob de páginas numa conta de Armazenamento do Azure. É importante certificar-se de que existe redundância e isolamento entre as contas de armazenamento, para proporcionar elevada disponibilidade às VMs no Conjunto de Disponibilidade.

1. **Manter todos os discos (SO e dados) associados a uma VM na mesma conta de armazenamento**
2. Os **limites da [conta de armazenamento](../articles/storage/storage-scalability-targets.md) devem ser considerados** ao adicionar mais VHDs a uma conta de armazenamento
3. **Utilize uma conta de armazenamento separada para cada VM num Conjunto de Disponibilidade.** As várias VMs no mesmo conjunto de disponibilidade NÃO podem partilhar contas de armazenamento. É aceitável que as VMs em diferentes Conjuntos de Disponibilidade partilhem contas de armazenamento, desde que as melhores práticas anteriores sejam seguidas

<!-- Link references -->
[Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurar cada camada da aplicação em conjuntos de disponibilidade separados]: #configure-each-application-tier-into-separate-availability-sets
[Combinar um Balanceador de Carga com conjuntos de disponibilidade]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Utilizar várias contas de armazenamento para cada conjunto de disponibilidade]: #use-multiple-storage-accounts-for-each-availability-set



<!--HONumber=Feb17_HO2-->


