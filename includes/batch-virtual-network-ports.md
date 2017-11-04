- A VNet tem de estar na mesma **região** e **subscrição** do Azure da conta do Batch.

- Para os conjuntos criados com uma configuração de máquina virtual, apenas são suportadas VNets baseadas no Azure Resource Manager. Para os conjuntos criados com uma configuração de serviços cloud, apenas são suportadas VNets clássicas. 
  
- Para utilizar uma VNets clássica, o principal de serviço `MicrosoftAzureBatch` tem de ter a função `Classic Virtual Machine Contributor` do Controlo de Acesso Baseado em Funções (RBAC) na VNet especificada. No entanto, para utilizar uma VNet baseada no Azure Resource Manager, não é necessária nenhuma configuração de permissão adicional.

- A sub-rede especificada para o conjunto deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o conjunto; ou seja, a soma de propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do conjunto. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o conjunto atribui parcialmente os nós de computação e ocorre um erro de redimensionamento. 

- A VNet tem de permitir a comunicação do serviço Batch para conseguir agendar tarefas nos nós de computação. Isto pode ser verificado ao confirmar se a VNet tem grupos de segurança de rede (NSGs) associados. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**. 

- Se a VNet especificada tiver Grupos de Segurança de Rede (NSGs) associados e/ou uma firewall, configure as portas de entrada e saída conforme mostrado nas seguintes tabelas:


  |    Porta(s) de Destino    |    Endereço IP de origem      |   Porta de origem    |    O Batch adiciona NSGs?    |    Necessário para a VM seja utilizável?    |    Ação do utilizador   |
  |---------------------------|---------------------------|----------------------------|----------------------------|-------------------------------------|-----------------------|
  |   <ul><li>Em agrupamentos criados com a configuração de máquina virtual: 29876, 29877</li><li>Nos conjuntos criados com a configuração de serviço cloud: 10100, 20100, 30100, 30100</li></ul>        |    Apenas endereços IP de função do serviço Batch | * ou 443 |    Sim. O Batch adiciona NSGs ao nível das interfaces de rede (NIC) anexadas a VMs. Estes NSGs permitem tráfego apenas de endereços IP da função do serviço Batch. Mesmo que abra estas portas a todo o tráfego da Internet, este será bloqueado na NIC. |    Sim  |  Não tem de especificar um NSG, porque o Batch só permite endereços IP do Batch. <br /><br /> No entanto, se especificar um NSG, certifique-se que estas portas estão abertas para tráfego de entrada. <br /><br /> Se especificar * como o IP de origem no NSG, o Batch continua a adicionar NSGs ao nível da NIC anexada às VMs. |
  |    3389 (Windows), 22 (Linux)               |    Máquinas de utilizador, utilizadas para fins de depuração, para que possa aceder remotamente à VM.    |   *  | Não                                    |    Não                    |    Adicione NSGs se quiser permitir o acesso remoto (RDP ou SSH) à VM.   |                                


  |    Porta(s) de Saída    |    Destino    |    O Batch adiciona NSGs?    |    Necessário para a VM seja utilizável?    |    Ação do utilizador    |
  |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
  |    443    |    Storage do Azure    |    Não    |    Sim    |    Se adicionar NSGs, confirme que esta porta está aberta ao tráfego de saída.    |

   Confirme também se o ponto final do Armazenamento do Azure pode ser resolvido por qualquer servidor DNS personalizado que sirva a sua VNet. Mais concretamente, devem ser resolvíveis os URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net`. 

   Se adicionar um Gestor de recursos com base em NSG, pode efetuar a utilização de [etiquetas de serviço](../articles/virtual-network/security-overview.md#service-tags) para selecionar os endereços IP de armazenamento para a região específico para ligações de saída. Tenha em atenção que os endereços IP de armazenamento tem de ser a mesma região que a sua conta do Batch e a VNet. Etiquetas de serviço estão atualmente em pré-visualização em regiões do Azure selecionadas.