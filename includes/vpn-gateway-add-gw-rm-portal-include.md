1. No portal, do lado esquerdo, clique em **+** e escreva "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. No painel do**Gateway de rede virtual**, clique em **Criar** na parte superior do painel. É aberto o painel **Criar gateway de rede virtual**.
2. No painel **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

    ![Create virtual network gateway blade fields](./media/vpn-gateway-add-gw-rm-portal-include/createvng.png "Create virtual network gateway blade fields")
3. **Nome**: dê um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
4. **Tipo de gateway**: selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**. 
5. **Tipo de VPN**: selecione o tipo de VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
6. **SKU**: selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar.
7. **Localização**: ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde reside a rede virtual, a rede virtual não será apresentada na lista pendente "Escolher uma rede virtual".
8. Escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir o painel **Escolher uma rede virtual**. Selecione a VNet. Se não vir o VNet, confirme que o campo **Localização** aponta para a região na qual a rede virtual está localizada.
9. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir o painel **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir o painel **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Este painel cria um objeto de endereço IP público ao qual um endereço IP público vai ser atribuído dinamicamente.<br>Clique em **OK** para guardar as alterações a este painel.
10. **Subscrição**: verifique se está selecionada a subscrição correta.
11. **Grupo de recursos**: esta definição é determinada pela Rede Virtual que selecionar. 
12. Não ajuste a **Localização** depois de especificar as definições anteriores.
13. Verifique as definições. Pode selecionar **Afixar ao dashboard** na parte inferior do painel se pretender que o gateway apareça no dashboard.
14. Clique em **Criar** para começar a criar o gateway. As definições serão validadas e verá o mosaico "A implementar gateway de rede Virtual" no dashboard. A criação de um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.
    
    ![Implementar gateway de rede Virtual](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Implementar gateway de rede Virtual")
15. Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado. Pode clicar no dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.

