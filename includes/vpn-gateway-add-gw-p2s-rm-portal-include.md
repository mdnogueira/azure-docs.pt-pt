1. No portal, do lado esquerdo, clique em **+** e escreva "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. Na página do **Gateway de rede virtual**, clique em **Criar** na parte inferior da página para abrir a página **Criar gateway de rede virtual**.
2. Na página **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

  ![Campos da página de criação do gateway de rede virtual](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "o painel ")
3. **Nome**: dê um nome ao gateway. Nomear um gateway não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
4. **Tipo de gateway**: selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**.
5. **Tipo de VPN**: selecione o tipo de VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
6. **SKU**: selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar.
7. **Localização**: ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde reside a rede virtual, a rede virtual não é apresentada na lista pendente "Escolher uma rede virtual".
8. Escolha a rede virtual à qual pretende adicionar um gateway. Clique em **Rede virtual** para abrir a página **Escolher uma rede virtual**. Selecione a VNet. Se não vir o VNet, confirme que o campo **Localização** aponta para a região na qual a rede virtual está localizada.
9. **Endereço IP público**: criar um objeto de endereço IP público ao qual será dinamicamente atribuído um endereço IP público. Clique em **Endereço IP público** para abrir a página **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir a página **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Clique em **OK** para guardar as alterações. O endereço IP é dinamicamente atribuído quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. No entanto, isto não significa que o endereço IP é alterado depois de ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.
10. **Subscrição**: verifique se está selecionada a subscrição correta.
11. **Grupo de recursos**: esta definição é determinada pela Rede Virtual que selecionar.
12. Não ajuste a **Localização** depois de especificar as definições anteriores.
13. Verifique as definições. Se pretender que o gateway apareça no dashboard, pode selecionar **Afixar ao dashboard** na parte inferior da página.
14. Clique em **Criar** para começar a criar o gateway. As definições são validadas e o gateway será implementado. A criação de um gateway pode demorar até 45 minutos.

Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual. O gateway aparece como um dispositivo ligado. Pode clicar no dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.