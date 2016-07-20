## Implementar o modelo ARM com a função clique para implementar

Pode reutilizar um carregamento predefinido de modelos ARM para um repositório github mantido pela Microsoft e abri-lo à comunidade. Estes modelos podem ser implementados diretamente do github ou transferidos e modificados para atender às suas necessidades. Para implementar um modelo que cria uma VNet com duas sub-redes, siga os passos abaixo.

1. Num browser, navegue para [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Desloque-se para baixo na lista de modelos e clique em **101 vnet-two subnets**. Marque o ficheiro **README.md**, como mostrado abaixo.

    ![Ficheiro READEME.md no github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Clique em **Implementar no Azure**. Se necessário, introduza as suas credenciais de início de sessão do Azure. 
4. No painel **Parâmetros**, introduza os valores que pretende utilizar para criar a sua nova VNet e clique em **OK**. A ilustração abaixo mostra os valores para o nosso cenário.

    ![Parâmetros do modelo ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Clique em **Grupo de recursos** e selecione um grupo de recursos a qual deve adicionar a VNet ou clique em **Criar novo** para adicionar a VNet a um novo grupo de recursos. A ilustração abaixo mostra as definições do grupo de recursos para um novo grupo de recursos chamado **TestRG**.

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Se necessário, altere as definições da **Subscrição** e da **Localização** para a sua VNet.
6. Se não pretender ver a VNet como um mosaico no **Startboard**, desative **Afixar ao Startboard**.
5. Clique em **Termos legais**, leia os termos e clique em **Comprar** para aceitar. 
6. Clique em **Criar** para criar a VNet.

    ![Mosaico Submeter implementação no portal de pré-visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Uma vez efetuada a implementação, clique em **TestVNet** > **Todas as definições** > **Sub-redes** para ver as propriedades das sub-redes, como mostrado abaixo.

    ![Criar a VNet no portal de pré-visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)


<!--HONumber=Jun16_HO2-->


