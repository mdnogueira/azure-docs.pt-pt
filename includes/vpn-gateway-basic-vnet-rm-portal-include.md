Para criar uma VNet com o Portal do Azure, siga os passos abaixo. Tenha em atenção que as capturas de ecrã são fornecidas como exemplos. Não se esqueça de substituir os valores pelos seus. Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição Geral da Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.

2. Clique em **Novo** **>** **Redes** **>** **Virtual Network**.

    ![VNetBlade](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal650.png)

3. Perto da parte inferior do painel Virtual Network, na lista **Selecionar um modelo de implementação**, selecione **Resource Manager** e clique em **Criar**.


    ![Selecionar o Resource Manager](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png)

4. No painel **Criar rede virtual**, configure as definições da VNet. Neste painel, deverá adicionar o seu primeiro espaço de endereços e um único intervalo de endereços de sub-rede. Uma vez concluída a criação da VNet, pode voltar atrás e adicionar sub-redes e espaços de endereços adicionais. Esta é uma limitação atual do portal. Pode sempre voltar atrás para atualizar estes valores, editando as propriedades da VNet no portal ou com o PowerShell. Os valores que utilizar irão depender da configuração que pretende criar. Não se esqueça de consultar os seus valores de configuração planeados. 

    ![Painel Criar rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/createavnet250.png)

5. Verifique se a **Subscrição** está correta. Pode alterar as subscrições com a lista pendente.

6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo escrevendo um nome para o novo grupo de recursos. Se estiver a criar um novo grupo, dê-lhe um nome de acordo com os valores de configuração planeados. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](resource-group-overview.md#resource-groups).

7. Em seguida, selecione as definições **Localização** para a sua VNet. Tenha em atenção que a localização irá determinar onde irão residir os recursos que são implementados para esta VNet. Se desejar alterar esta definição posteriormente, terá de voltar a implementar os recursos.

8. Selecione **Afixar ao dashboard** se pretender conseguir localizar mais facilmente as suas VNet no dashboard e clique em **Criar**.
    
    ![Afixar ao dashboard](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png)


9. Depois de clicar em Criar, verá um mosaico no dashboard que irá refletir o progresso da VNet. O mosaico muda enquanto a VNet está a ser criada.

    ![Mosaico Criar rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png)

<!--HONumber=Sep16_HO3-->


