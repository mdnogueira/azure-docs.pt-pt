## <a name="scenario"></a>Cenário
Este documento irá guiá-lo através de uma implementação que utiliza vários NICs nas VMs num cenário específico. Neste cenário, tem uma duas camadas IaaS carga de trabalho alojada no Azure. Cada camada está implementada na sua própria sub-rede numa rede virtual (VNet). A camada de front-end é composta por vários servidores web, agrupados num Balanceador de carga definido para elevada disponibilidade. A camada de back-end é composta por vários servidores de base de dados. Estes servidores de base de dados serão implementadas com dois NICs cada, um para acesso de base de dados, outra para gestão. O cenário também inclui grupos de segurança de rede (NSGs) para controlar o tráfego é permitido para cada sub-rede e NIC na implementação. A figura abaixo mostra a arquitetura básica deste cenário.  

![Cenário de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

