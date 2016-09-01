1. No portal, aceda a **Novo** > **Redes** > **Gateway de rede virtual**. Esta ação irá abrir o painel **Criar gateway de rede virtual**.

    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. No campo **Nome** do painel **Criar gateway de rede virtual**, atribua um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto gateway que irá criar.

3. Ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Caso não o faça, a lista de VNets não irá apresentar a sua rede virtual.
 
4. Em seguida, escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir o painel **Escolher uma rede virtual**. Selecione a VNet. Para que a VNet seja apresentada na lista, já tem de ter uma sub-rede de gateway válida.

5. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir o painel **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir o painel **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Isto irá criar um objeto de endereço IP público ao qual será dinamicamente atribuído um endereço IP público. <br>Clique em **OK** para guardar as alterações.

5. Para **Tipo de gateway**, selecione o tipo de gateway especificado para a sua configuração.

6. Para **Tipo de VPN**, selecione o tipo de VPN especificado para a sua configuração.

7. Para **Subscrição**, verifique se está selecionada a subscrição correta.

8. O **Grupo de recursos**é determinado através da Rede Virtual que selecionou. 

9. Não ajuste a **Localização** depois de especificar as definições acima. 

10. Neste momento, o painel terá um aspeto semelhante ao gráfico no passo 1. Certifique-se de que as definições correspondem às definições da sua própria configuração. Pode selecionar **Afixar ao dashboard** na parte inferior do painel se pretender que o gateway apareça no dashboard.

11. Clique em **Criar** para começar a criar o gateway. As definições serão validadas e verá o mosaico "A implementar gateway de rede Virtual" no dashboard. A criação de um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.

    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado. Pode clicar no dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.






<!--HONumber=ago16_HO4-->


