1. No portal, navegue para a rede virtual para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** da página da VNet, clique em **Sub-redes** para expandir a página Sub-redes.
3. Na página **Sub-redes**, clique em **+Sub-rede do gateway** na parte superior para abrir a página **Adicionar sub-rede**.

  ![Add the gateway subnet](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "Add the gateway subnet")
4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". O valor de GatewaySubnet é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração.

  ![Adicionar a subrede do gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "Adicionar a subrede do gateway")
5. Para criara sub-rede, clique em **OK** na parte inferior da página.