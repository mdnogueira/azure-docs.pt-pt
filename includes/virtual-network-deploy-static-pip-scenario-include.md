## <a name="scenario"></a>Cenário
Este documento irá guiá-lo através de uma implementação que utiliza um endereço IP público estático atribuído a uma máquina virtual (VM). Neste cenário, terá de uma única VM com o seu próprio endereço IP público estático. A VM faz parte de uma sub-rede designada **front-end** e também tem um endereço IP privado estático (**192.168.1.101**) nessa sub-rede.

Poderá ter um endereço IP estático para servidores web que necessitam de ligações de SSL no qual o certificado SSL está ligado a um endereço IP. 

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Pode seguir os passos abaixo para implementar o ambiente apresentado na figura acima.

