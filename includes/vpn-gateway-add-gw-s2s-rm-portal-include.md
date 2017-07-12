1. No lado esquerdo da página do portal, clique em **+** e escreva "Gateway de Rede Virtual" na pesquisa. Em **Resultados**, localize e clique em **Gateway de rede Virtual**.
2. Na parte inferior do painel Gateway de rede virtual, clique em **Criar**. É aberto o painel **Criar gateway de rede virtual**.

    ![Campos do painel Criar gateway de rede virtual](./media/vpn-gateway-add-gw-s2s-rm-portal-include/vnet_gw.png "Novo gateway")

3. No painel **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

  - **Nome**: dê um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
  - **Tipo de gateway**: selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**. 
  - **Tipo de VPN**: selecione o tipo de VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
  - **SKU**: selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
  - **Localização**: poderá ter de deslocar para ver a Localização. Ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde reside a rede virtual, a rede virtual não será apresentada no próximo passo da lista pendente "Escolher uma rede virtual".
  - **Rede virtual**: escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir o painel Escolher uma rede virtual. Selecione a VNet. Se não vir a VNet, confirme se o campo Localização aponta para a região na qual a rede virtual está localizada.
  - **Endereço IP público**: O painel 'Criar endereço IP público' cria um objeto de endereço IP público. O endereço IP público é dinamicamente atribuído quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

    - Primeiro, clique em **Endereço IP público** para abrir o painel 'Escolher endereço IP público' e clique em **+ Criar novos** para abrir o painel 'Criar endereço IP público'.
    - Em seguida, insira um **nome** para seu endereço IP público e clique em **OK** na parte inferior deste painel para guardar as suas alterações.

      ![Criar IP público](./media/vpn-gateway-add-gw-s2s-rm-portal-include/pip.png "Criar PIP")

4. Verifique as definições. Pode selecionar **Afixar ao dashboard** na parte inferior do painel se pretender que o gateway apareça no dashboard. 
5. Clique em **Criar** para começar a criar o gateway de VPN. As definições serão validadas e verá o mosaico "A implementar gateway de rede Virtual" no dashboard. A criação de um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.

Uma vez criado o gateway, visualize o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado. Pode clicar no dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.