## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Como criar uma VNet clássica no portal do Azure
Para criar uma VNet clássica com base no cenário acima, siga os passos abaixo.

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **novo** > **redes** > **rede Virtual**, tenha em atenção que o **selecionar um modelo de implementação** já lista mostra **clássico**e, em seguida, clique em **criar**, como mostrado na imagem abaixo.
   
    ![Criar a VNet no Portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. No **rede Virtual** painel, escreva o **nome** da VNet e, em seguida, clique em **espaço de endereços**. Configure as definições de espaço de endereço para a VNet e a primeira sub-rede, em seguida, clique em **OK**. A figura abaixo mostra as definições de bloco CIDR para o nosso cenário.
   
    ![Painel de espaço de endereço](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. Clique em **grupo de recursos** e selecione um grupo de recursos para adicionar a VNet ou clique em **criar novo grupo de recursos** para adicionar a VNet a um novo grupo de recursos. A ilustração abaixo mostra as definições do grupo de recursos para um novo grupo de recursos chamado **TestRG**. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Criar painel do grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. Se necessário, altere as definições da **Subscrição** e da **Localização** para a sua VNet. 
6. Se não pretender ver a VNet como um mosaico no **Startboard**, desative **Afixar ao Startboard**. 
7. Clique em **Criar** e repare no mosaico com o nome **Criar Rede Virtual**, como mostrado na imagem abaixo.
   
    ![Criar a VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. Aguarde que a VNet seja criada e quando for apresentada no mosaico abaixo, clique para adicionar mais sub-redes.
   
    ![Criar a VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. Deverá ver o **configuração** para a sua VNet, como mostrado abaixo. 
   
    ![Criar a VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. Clique em **sub-redes** > **adicionar**, em seguida, escreva um **nome** e especifique um **(bloco CIDR) do intervalo de endereços** para a sub-rede e, em seguida, Clique em **OK**. A figura abaixo mostra as definições para o nosso cenário atual.
    
    ![Criar a VNet no Portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

