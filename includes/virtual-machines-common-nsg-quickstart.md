Abrir uma porta ou criar um ponto final, para uma máquina virtual (VM) no Azure através da criação de um filtro de rede numa sub-rede ou interface de rede VM. Colocar estes filtros que controlam o tráfego de entrada e saído, num grupo de segurança de rede ligado para o recurso que recebe o tráfego.

Vamos utilizar um exemplo comum de tráfego da web na porta 80. Assim que tiver uma VM que está configurada para servir pedidos web em TCP padrão porta 80 (não se esqueça de iniciar os serviços adequados e abra quaisquer regras de firewall de SO da VM, bem como),:

1. Crie um grupo de segurança de rede.
2. Crie uma regra de entrada permitir o tráfego com:
   * o intervalo de portas de destino "80"
   * o intervalo de portas de origem "*" (permitir que qualquer porta de origem)
   * um valor de prioridade de menos 65,500 (para ser superior numa prioridade de que a predefinição catch-all regra de negação de entrada)
3. Associe o grupo de segurança de rede com a interface de rede VM ou sub-rede.

Pode criar configurações de rede complexas para proteger o seu ambiente utilizando grupos de segurança de rede e regras. O nosso exemplo utiliza apenas uma ou duas regras que permitam o tráfego HTTP ou de gestão remota. Para obter mais informações, consulte o seguinte ['Informação mais'](#more-information-on-network-security-groups) secção ou [que é um grupo de segurança de rede?](../articles/virtual-network/virtual-networks-nsg.md)

