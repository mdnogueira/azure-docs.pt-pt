## <a name="a-namex-subapeering-across-subscriptions"></a><a name="x-sub"></a>Peering entre subscrições
Neste cenário, irá criar um peering entre duas VNets que existem em subscrições diferentes.

![cenário de cruzamento de subscrições](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

O VNet peering utiliza o controlo de acesso baseado em funções (RBAC) para fins de autorização. Para o cenário de cruzamento de subscrições, primeiro tem de conceder permissão suficiente aos utilizadores que irão criar a ligação peering.

> [!NOTE]
> Se o mesmo utilizador tiver o privilégio em ambas as subscrições, pode então ignorar os passos 1-4 que se seguem.
> 
> 



<!--HONumber=Feb17_HO1-->


