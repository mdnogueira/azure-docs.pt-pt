

![Máquinas virtuais no serviço autónomo em nuvem](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Se colocar as máquinas virtuais numa rede virtual, pode decidir quantos serviços em nuvem que pretende utilizar para conjuntos de disponibilidade e balanceamento de carga. Além disso, pode organizar as máquinas virtuais em sub-redes da mesma forma como o local de rede e ligar a rede virtual à sua rede no local. Eis um exemplo:

![Máquinas virtuais numa rede virtual](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Redes virtuais são a forma recomendada para ligar a máquinas virtuais no Azure. A melhor prática consiste em configurar cada camada da aplicação no serviço em nuvem separado. No entanto, poderá ter de combinar algumas máquinas virtuais de camadas de aplicação diferente no mesmo serviço em nuvem permanecer dentro do máximo de 200 serviços cloud por subscrição. Para rever esta e outros limites, consulte [subscrição do Azure e limites de serviço, Quotas e restrições](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Ligar as VMs numa rede virtual
Ligar máquinas virtuais numa rede virtual:

1. Criar a rede virtual no [portal do Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) e especifique 'implementação clássica'.
2. Crie o conjunto de serviços em nuvem para a implementação refletir o design para conjuntos de disponibilidade e o balanceamento de carga. No portal do Azure, clique em **novo > computação > serviço em nuvem** para cada serviço em nuvem.

  Como preencher os detalhes do serviço de nuvem, selecione a mesma _grupo de recursos_ utilizado com a rede virtual.

3. Para criar cada nova máquina virtual, clique em **novo > computação**, em seguida, selecione a imagem VM adequada do **aplicações em destaque**.

  Na VM **Noções básicas** painel, selecione a mesma _grupo de recursos_ utilizado com a rede virtual.

  ![Painel de noções básicas de VM ao utilizar uma VNet](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Como a preencher a VM **definições**, escolha o correto _serviço em nuvem_ ou _rede virtual_ para a VM.

  Azure irá selecionar outro item com base na sua seleção.

  ![Painel de definições de VM ao utilizar uma VNet](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Ligar as VMs num serviço autónomo em nuvem
Ligar máquinas virtuais no serviço autónomo em nuvem:

1. Criar serviço em nuvem a [portal do Azure](http://portal.azure.com). Clique em **novo > computação > serviço em nuvem**. Em alternativa, pode criar o serviço em nuvem para a sua implementação quando criar a sua primeira máquina virtual.
2. Quando criar as máquinas virtuais, selecione o mesmo grupo de recursos utilizado com o serviço em nuvem.

  ![Adicionar uma máquina virtual para um serviço em nuvem existente](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Como preencher os detalhes VM, escolha o nome do serviço em nuvem criado no primeiro passo.

  ![Selecionar um serviço em nuvem para uma máquina virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
