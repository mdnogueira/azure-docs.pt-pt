## Peering entre subscrições

Neste cenário, irá criar um peering entre duas VNets que pertencem a subscrições diferentes.

![cenário de cruzamento de subscrições](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

O VNet peering utiliza o Controlo de acesso baseado em funções (RBAC) para fins de autorização. Para o cenário de cruzamento de subscrições, primeiro tem de conceder permissão suficiente aos utilizadores que irão criar a ligação peering: NOTA: se o mesmo utilizador tiver privilégios em ambas as subscrições, pode ignorar os passos 1 a 4 abaixo. 


<!--HONumber=Aug16_HO1-->


