Existem várias razões quando não é possível iniciar ou ligar a uma aplicação em execução numa máquina virtual do Azure (VM). Por motivos incluem a aplicação não está em execução ou está a escutar as portas esperadas, a porta de escuta bloqueado ou redes regras correctamente transmissão de tráfego para a aplicação. Este artigo descreve uma abordagem methodical para localizar e corrigir o problema.

Se estiver a ter problemas em ligar à VM através de RDP ou SSH, consulte um dos seguintes artigos primeiro:

* [Resolver problemas de ligações de ambiente de trabalho remoto para um baseados em Windows Máquina Virtual do Azure](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)
* [Resolver problemas de ligações de Secure Shell (SSH) para uma máquina virtual do Azure baseados em Linux](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../articles/resource-manager-deployment-model.md). Este artigo inclui os dois modelos, mas a Microsoft recomenda que a maioria das implementações novas utilizem o modelo Resource Manager.

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [do MSDN Azure e os fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode também ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporta**.

## <a name="quick-start-troubleshooting-steps"></a>Passos de resolução de problemas de início rápido
Se tiver problemas de ligação a uma aplicação, tente os seguintes passos de resolução de problemas gerais. Depois de cada passo, tente ligar novamente a aplicação:

* Reinicie a máquina virtual
* Recrie o ponto final / regras de firewall / regras de segurança de grupo (NSG) de rede
  * [Modelo do Resource Manager - gerir grupos de segurança de rede](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
  * [Modelo de clássico - pontos finais de gerir os serviços Cloud](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
* Ligar a partir de uma localização diferente, tal como uma rede virtual do Azure diferente
* Volte a implementar a máquina virtual
  * [Reimplementar a VM do Windows](../articles/virtual-machines/windows/redeploy-to-new-node.md)
  * [Reimplementar a VM com Linux](../articles/virtual-machines/linux/redeploy-to-new-node.md)
* Recriar a máquina virtual

Para obter mais informações, consulte [resolução de problemas de Conetividade de ponto final (RDP/SSH/HTTP, falhas de etc.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Descrição geral de resolução de problemas detalhada
Existem quatro áreas principais para resolver problemas de acesso de uma aplicação que está a ser executado numa máquina virtual do Azure.

![resolver problemas de não é possível iniciar a aplicação](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. A aplicação em execução na máquina virtual do Azure.
   * A própria aplicação está a funcionar corretamente?
2. A máquina virtual do Azure.
   * É a própria VM a ser executado corretamente e responder a pedidos?
3. Pontos finais de rede do Azure.
   * Nuvem pontos finais de serviço para máquinas virtuais no modelo de implementação clássica.
   * Grupos de segurança de rede e regras NAT de entrada para máquinas virtuais no modelo de implementação do Resource Manager.
   * Pode tráfego fluxo dos utilizadores para a VM aplicação nas portas esperadas?
4. O dispositivo de limite de Internet.
   * São as regras de firewall no local impedir que o tráfego que flui corretamente?

Para computadores de cliente que estão a aceder a aplicação através de uma ligação de VPN ou ExpressRoute do site para site, as áreas principais que podem causar problemas são a aplicação e a máquina virtual do Azure.

Para determinar a origem do problema e a correção, siga estes passos.

## <a name="step-1-access-application-from-target-vm"></a>Passo 1: Aceder à aplicação a partir do destino VM
Tente aceder à aplicação com o programa de cliente adequado da VM no qual está a ser executado. Utilize o nome do anfitrião local, o endereço IP local ou o endereço de loopback (127.0.0.1).

![iniciar a aplicação diretamente a partir da VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Por exemplo, se a aplicação é um servidor web, abra um browser na VM e tente aceder a uma página web alojados na VM.

Se lhe pode aceder à aplicação, aceda a [passo 2](#step2).

Se não conseguir aceder a aplicação, verifique as seguintes definições:

* A aplicação está em execução na máquina virtual de destino.
* A aplicação está a escutar nas portas TCP e UDP esperadas.

No Windows e máquinas virtuais baseadas em Linux, utilize o **netstat - um** comando para mostrar as portas de escuta de Active Directory. Examine a saída para as portas esperadas no qual deve deve ainda ouvir a sua aplicação. Reinicie a aplicação ou configure-a para utilizar as portas esperadas, conforme necessário e tente aceder novamente a aplicação localmente.

## <a id="step2"></a>Passo 2: Aceder à aplicação a partir de outra VM na mesma rede virtual
Tente aceder à aplicação a partir de uma VM diferente, mas na mesma rede virtual, utilizando o nome do anfitrião da VM ou o Azure atribuído public, private ou fornecedor de endereço IP. Para máquinas virtuais criadas com o modelo de implementação clássica, não utilize o endereço IP público do serviço de nuvem.

![iniciar a aplicação de uma VM diferente](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Por exemplo, se a aplicação é um servidor web, tente aceder a uma página web a partir de um browser numa VM diferente na mesma rede virtual.

Se lhe pode aceder à aplicação, aceda a [passo 3](#step3).

Se não conseguir aceder a aplicação, verifique as seguintes definições:

* A firewall do anfitrião no destino VM está a permitir o tráfego de saída de resposta e pedido de entrada.
* Deteção de intrusões ou em execução numa VM de destino do software de monitorização de rede está a permitir o tráfego.
* Pontos finais de serviços em nuvem ou grupos de segurança de rede estão a permitir o tráfego:
  * [Modelo de clássico - pontos finais de gerir os serviços Cloud](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Modelo do Resource Manager - gerir grupos de segurança de rede](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
* Um componente separado em execução na sua VM com o caminho entre o teste de VM e a VM, como um balanceador de carga ou uma firewall, está a permitir o tráfego.

Numa máquina virtual baseado no Windows, utilize a Firewall do Windows com segurança avançada para determinar se as regras de firewall excluir o tráfego de entrada e saída da sua aplicação.

## <a id="step3"></a>Passo 3: Aceder à aplicação a partir de fora da rede virtual
Tente aceder à aplicação a partir de um computador fora da rede virtual da VM em que a aplicação é executada. Utilize uma rede diferente que o computador cliente original.

![iniciar a aplicação de um computador fora da rede virtual](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Por exemplo, se a aplicação é um servidor web, tente aceder à página web a partir de um browser em execução num computador que não está na rede virtual.

Se não conseguir aceder a aplicação, verifique as seguintes definições:

* Para VMs criadas com o modelo de implementação clássica:
  
  * Certifique-se de que a configuração de ponto final para a VM está a permitir o tráfego de entrada, especialmente, o protocolo (TCP ou UDP) e os números das portas públicas e privadas.
  * Certifique-se de que as listas de controlo de acesso (ACLs) no ponto final não estão a impedir tráfego de entrada da Internet.
  * Para obter mais informações, consulte [como conjunto de cópias de segurança de pontos finais para uma Máquina Virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Para VMs criadas com o modelo de implementação do Gestor de recursos:
  
  * Certifique-se de que a configuração da regra NAT entrada para a VM está a permitir o tráfego de entrada, especialmente, o protocolo (TCP ou UDP) e os números das portas públicas e privadas.
  * Certifique-se de que os grupos de segurança de rede estão a permitir o tráfego de saída de resposta e pedido de entrada.
  * Para obter mais informações, consulte o artigo [O que é um Grupo de Segurança de Rede (NSG)?](../articles/virtual-network/virtual-networks-nsg.md)

Se a máquina virtual ou o ponto final for um membro de um conjunto com balanceamento de carga:

* Certifique-se de que o protocolo de pesquisa (TCP ou UDP) e o número da porta estão corretos.
* Se o protocolo de pesquisa e a porta é diferente do protocolo do conjunto com balanceamento de carga e a porta:
  * Certifique-se de que a aplicação está a escutar o protocolo de pesquisa (TCP ou UDP) e o número de porta (utilizar **netstat – a** no destino VM).
  * Certifique-se de que a firewall do anfitrião no destino VM está a permitir o tráfego de resposta de sonda de saída e pedidos de sonda de entrada.

Se lhe pode aceder à aplicação, certifique-se de que o seu dispositivo de limite de Internet está a permitir:

* O aplicação de saída pedido tráfego do computador cliente para a máquina virtual do Azure.
* O tráfego de resposta de aplicação de entrada de máquina virtual do Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Passo 4 se não conseguir aceder a aplicação, utilize Certifique-se de IP para verificar as definições. 

Para obter mais informações, consulte [descrição geral da monitorização de rede do Azure](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Recursos adicionais
[Resolver problemas de ligações de ambiente de trabalho remoto para um baseados em Windows Máquina Virtual do Azure](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)

[Resolver problemas de ligações de Secure Shell (SSH) para uma máquina virtual do Azure baseados em Linux](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md)

