## Encadeamento de Serviços – Trânsito através de VNet em modo de peering

Embora a utilização das rotas de sistema facilita o tráfego automaticamente para a implementação, há casos em que pretende controlar o encaminhamento de pacotes através de uma aplicação virtual.
Neste cenário, existem duas VNets numa subscrição, HubVNet e VNet1, como se descreve no diagrama abaixo. Implementa a Aplicação Virtual de Rede (NVA) na VNet HubVNet. Depois de estabelecer o VNet peering entre HubVNet e VNet1, pode configurar Rotas Definidas pelo Utilizador e especificar o salto seguinte para NVA na HubVNet.

![Trânsito NVA](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Por razões de simplicidade, parta do princípio de que todas as VNets aqui estão na mesma subscrição. No entanto, este exemplo também funciona para o cenário entre subscrições.

A propriedade-chave para ativar o encaminhamento do Trânsito é o parâmetro “Permitir Tráfego Reencaminhado”. Isto permite aceitar e enviar tráfego de/para a NVA na VNet em modo de peering.  


<!--HONumber=Sep16_HO3-->


