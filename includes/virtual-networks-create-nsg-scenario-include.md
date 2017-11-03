## <a name="scenario"></a>Cenário
Para ilustrar melhor como criar NSGs, este documento utilizará o cenário abaixo.

![Cenário de VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Neste cenário, irá criar um NSG para cada sub-rede no **TestVNet** rede virtual, conforme descrito abaixo: 

* **NSG-front-end**. O front-end NSG será aplicada ao *front-end* sub-rede e contêm duas regras:    
  * **regra de RDP**. Esta regra irá permitir tráfego RDP para a *front-end* sub-rede.
  * **regra de Web**. Esta regra irá permitir tráfego HTTP para o *front-end* sub-rede.
* **NSG-back-end**. O back-end NSG será aplicada ao *back-end* sub-rede e contêm duas regras:    
  * **regra de SQL**. Esta regra permite que o tráfego SQL apenas a partir de *front-end* sub-rede.
  * **regra de Web**. Esta regra negar internet todos os vinculado tráfego a partir do *back-end* sub-rede.

A combinação destas regras criar um cenário semelhante a rede de Perímetro, onde a sub-rede de back-end pode receber apenas tráfego de entrada para o SQL Server da sub-rede do front-end e não tem acesso à Internet, enquanto a sub-rede do front-end pode comunicar com a Internet e receber pedidos HTTP recebidos apenas.

