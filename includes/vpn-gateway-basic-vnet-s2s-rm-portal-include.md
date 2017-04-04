Para criar uma VNet no modelo de implementação do Gestor de Recursos com o Portal do Azure, siga os passos abaixo. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus. Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição Geral da Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Clique em **Novo**. No campo **Procurar no Marketplace**, escreva "Rede Virtual". Localize **Rede Virtual** na lista devolvida e clique para abrir o painel **Rede Virtual**.
3. Perto da parte inferior do painel Virtual Network, na lista **Selecionar um modelo de implementação**, selecione **Resource Manager** e clique em **Criar**.
4. No painel **Criar rede virtual**, configure as definições da VNet. Depois de preencher os campos, o sinal de exclamação vermelho muda para uma marca de verificação verde se os carateres introduzidos nos campos forem válidos.
5. O painel **Criar rede virtual** é semelhante ao exemplo seguinte. Poderá haver valores preenchidos automaticamente. Se for esse o caso, substitua-os pelos seus próprios valores.
   
    ![Painel Criar rede virtual](./media/vpn-gateway-basic-vnet-s2s-rm-portal-include/createvnet.png "Painel Criar rede virtual")
6. **Nome**: introduza o nome da Rede Virtual.
7. **Espaço de endereços**: introduza o espaço de endereços. Se tiver vários espaços de endereços para adicionar, adicione o primeiro. Pode adicionar os outros mais tarde, depois de criar a VNet. Certifique-se de que o espaço de endereço que especificou não se sobrepõe ao espaço de endereço para a sua localização no local.
8. **Nome da sub-rede**: adicione o nome e o intervalo de endereços da sub-rede. Pode adicionais outras sub-redes mais tarde, depois de criar a VNet.
9. **Subscrição**: verifique se a Subscrição listada é a correta. Pode utilizar o menu pendente para mudar de subscrição.
10. **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo ao escrever o nome do mesmo. Se estiver a criar um novo grupo, dê-lhe um nome de acordo com os valores de configuração planeados. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
11. **Localização**: selecione a localização da VNet. A localização determina onde é que os recursos que implementar nesta VNet vão residir.
12. Selecione **Afixar ao dashboard** se pretender conseguir localizar mais facilmente as suas VNet no dashboard e clique em **Criar**.
13. Depois de clicar em **Criar**, verá um mosaico no dashboard que irá refletir o progresso da VNet. O mosaico muda enquanto a VNet está a ser criada.