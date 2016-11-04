## Como criar uma VNet no Portal do Azure
Para criar uma VNet com base no cenário acima com o Portal de Pré-Visualização do Azure, siga os passos abaixo.

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **NOVO** > **Redes** > **Rede virtual**, em seguida, clique em **Resource Manager** na lista **Selecione um modelo de implementação** lista e clique em **Criar**, como mostrado na imagem abaixo.
   
    ![Criar a VNet no Portal do Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)
3. No painel **Criar rede virtual**, configure as definições da VNet, como mostrado na imagem abaixo.
   
    ![Painel Criar rede virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)
4. Clique em **Grupo de recursos** e selecione um grupo de recursos a qual deve adicionar a VNet ou clique em **Criar novo** para adicionar a VNet a um novo grupo de recursos. A ilustração abaixo mostra as definições do grupo de recursos para um novo grupo de recursos chamado **TestRG**. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Azure Resource Manager](../articles/resource-group-overview.md#resource-groups).
   
    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)
5. Se necessário, altere as definições da **Subscrição** e da **Localização** para a sua VNet. 
6. Se não pretender ver a VNet como um mosaico no **Startboard**, desative **Afixar ao Startboard**. 
7. Clique em **Criar** e repare no mosaico com o nome **Criar Rede Virtual**, como mostrado na imagem abaixo.
   
    ![Mosaico Criar rede virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)
8. Aguarde que a VNet seja criada, em seguida, no painel **Rede virtual**, clique em **Todas as definições** > **Sub-redes** > **Adicionar**, como mostrado abaixo.
   
    ![Adicionar a sub-rede no Portal do Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)
9. Especifique as definições da sub-rede do *Back-end*, como mostrado abaixo, e clique em **OK**. 
   
    ![Definições da sub-rede](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)
10. Repare na lista de sub-redes, como mostrado na imagem abaixo.
    
    ![Lista de sub-redes na VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!--HONumber=Sep16_HO3-->


