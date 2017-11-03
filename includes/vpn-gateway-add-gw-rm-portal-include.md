1. No portal, do lado esquerdo, clique em **+** e escreva "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. Na página do **Gateway de rede virtual**, clique em **Criar** na parte inferior da página para abrir a página **Criar gateway de rede virtual**.
2. Na página **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

  ![Campos da página de criação do gateway de rede virtual](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "o painel ")
3. No **criar gateway de rede virtual** página, especifique os valores para o gateway de rede virtual.

  - **Nome**: dê um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
  - **Tipo de gateway**: selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**. 
  - **Tipo de VPN**: selecione o tipo de VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
  - **SKU**: selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
  - **Localização**: poderá ter de deslocar para ver a Localização. Ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde um reside a rede virtual, quando seleciona uma rede virtual no próximo passo, não serão apresentados na lista pendente.
  - **Rede virtual**: escolha a rede virtual à qual pretende adicionar este gateway. Clique em **rede Virtual** para abrir a página 'Escolher uma rede virtual'. Selecione a VNet. Se não vir a VNet, confirme se o campo Localização aponta para a região na qual a rede virtual está localizada.
  - **Intervalo de endereços de sub-rede de gateway**: apenas será apresentada esta definição se não tiver anteriormente criado uma sub-rede de gateway da rede virtual. Se uma sub-rede de gateway válida que criou anteriormente, esta definição não serão apresentados.
  - **Configuração de IP primeiro**: A página 'Escolher endereço IP público' cria um objeto de endereço IP público que obtém associado para o gateway VPN. O endereço IP público é dinamicamente atribuído a este objeto, quando é criado o gateway VPN. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

    - Em primeiro lugar, clique em **configuração de IP do gateway criar** para abrir a página 'Escolher endereço IP público', em seguida, clique em **+ criar nova** para abrir a página 'Criar endereço IP público'.
    - Em seguida, introduza um **nome** para o seu endereço IP público. Deixe o SKU como **básico** a menos que exista um motivo específico para alterá-la para outra coisa, em seguida, clique em **OK** na parte inferior desta página para guardar as alterações.

      ![Criar IP público](./media/vpn-gateway-add-gw-s2s-rm-portal-include/gwip.png "Criar PIP")

4. Verifique as definições. Pode selecionar **afixar ao dashboard** na parte inferior da página, se pretender que o gateway apareça no dashboard. 
5. Clique em **Criar** para começar a criar o gateway de VPN. As definições são validadas e verá "gateway de rede Virtual da implementação" mosaico no dashboard. A criação de um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.

Uma vez criado o gateway, visualize o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado. Pode clicar no dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.
