É importante compreender que existem duas formas de configurar um serviço de escuta do grupo de disponibilidade no Azure. As formas diferem no tipo de Balanceador de carga do Azure a utiliza quando criar o serviço de escuta. A tabela seguinte descreve as diferenças:

| Tipo de Balanceador de carga | Implementação | Utilize se: |
| --- | --- | --- |
| **Externo** |Utiliza o *endereço IP virtual público* do serviço de nuvem que aloja as máquinas virtuais (VMs). |Precisa de aceder ao serviço de escuta de fora da rede virtual, incluindo a partir da Internet. |
| **Interno** |Utiliza um *Balanceador de carga interno* com um endereço privado para o serviço de escuta. |Pode aceder ao serviço de escuta apenas a partir de dentro da mesma rede virtual. Este acesso inclui VPN site a site em cenários híbridos. |

> [!IMPORTANT]
> Para um serviço de escuta que utiliza pública VIP o serviço em nuvem (Balanceador de carga externo), desde que o cliente, serviço de escuta e bases de dados na mesma região do Azure, não será cobrado encargos associados à saída. Caso contrário, os dados devolvidos através do serviço de escuta são considerados saída e é-lhe cobrado às taxas de transferência de dados normal. 
> 
> 

Pode ser configurado um ILB apenas em redes virtuais com um âmbito regional. As redes virtuais existentes que tenham sido configuradas para um grupo de afinidade não é possível utilizar um ILB. Para obter mais informações, consulte [descrição geral do Balanceador de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

