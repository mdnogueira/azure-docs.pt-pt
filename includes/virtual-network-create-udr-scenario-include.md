## <a name="scenario"></a>Cenário
Para ilustrar melhor como criar UDRs, este documento utilizará o cenário abaixo.

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-create-udr-scenario-include/figure1.png)

Neste cenário, irá criar um UDR para o *sub-rede de fim da frente* e UDR outro para o *da sub-rede Back end* , conforme descrito abaixo: 

* **UDR-front-end**. O front-end UDR será aplicada ao *front-end* sub-rede e contêm uma rota:    
  * **RouteToBackend**. Esta rota irá enviar todo o tráfego para a sub-rede de back-end para o **FW1** máquina virtual.
* **UDR-back-end**. O back-end UDR será aplicada ao *back-end* sub-rede e contêm uma rota:    
  * **RouteToFrontend**. Esta rota irá enviar todo o tráfego para a sub-rede do front-end para o **FW1** máquina virtual.

A combinação destas rotas irá garantir que todo o tráfego destinado a partir de uma sub-rede para outra será encaminhado para o **FW1** máquina virtual, que está a ser utilizada como uma aplicação virtual. Terá também de ativar o reencaminhamento IP para essa VM, certifique-se de que pode receber o tráfego destinado ao outras VMs.

