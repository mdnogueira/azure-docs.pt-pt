1. No portal, navegue para a rede virtual para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** do painel da VNet, clique em **Sub-redes** para expandir o painel Sub-redes.
3. No painel **Sub-redes**, clique em **+Sub-rede do gateway** na parte superior. Esta ação irá abrir o painel **Adicionar sub-rede**.

  ![Add the gateway subnet](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/add-gw-subnet.png "Add the gateway subnet")
4. O **Nome** da sub-rede será preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a subrede como GatewaySubnet. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração.

  ![Adicionar a subrede do gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnetip.png "Adicionar a subrede do gateway")
5. Clique em **OK** na parte inferior do painel para criar a sub-rede.