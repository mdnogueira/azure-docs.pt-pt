1. No portal, aceda a **Novo** e a **Redes**. Selecione **Gateway de rede virtual** na lista.

    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. No campo **Nome** do painel **Criar gateway de rede virtual**, dê um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto gateway.
 
3. Em seguida, escolha a rede virtual onde pretende implementar este gateway. Clique na seta para abrir o painel **Escolher uma rede virtual** e clique na VNet. Para que a VNet seja apresentada na lista, já tem de ter uma sub-rede de gateway válida.

4. Escolha um endereço IP público. Clique na seta para abrir o painel **Escolher endereço IP público**. Em seguida, clique em **Criar Novo** para abrir o painel **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Tenha em atenção que não estamos a pedir um endereço IP. O endereço IP será atribuído dinamicamente. É, sim, o nome do objeto do endereço IP ao qual o endereço será atribuído. Clique em **OK** para guardar as alterações.

5. Para **Tipo de gateway**, selecione o tipo de gateway especificado para a sua configuração.

6. Para **Tipo de VPN**, selecione o tipo de VPN especificado para a sua configuração.

7. Para **Subscrição**, verifique se está selecionada a subscrição correta.

8. Para **Grupo de Recursos**, o grupo de recursos é determinado através da Rede Virtual que selecionou.

9. Para **Localização**, verifique se está visível a localização onde estão o seu Grupo de Recursos e a VNet.

10. Pode selecionar **Afixar ao dashboard** se pretender que o gateway apareça no dashboard. Clique em **Criar** para começar a criar o gateway. Verá o mosaico “A implementar gateway de Rede virtual” no dashboard. A criação de um gateway pode demorar até 45 minutos. Há muito a decorrer em segundo plano. Poderá ter de atualizar a página do portal para ver o estado concluído.

    
    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado. Pode clicar no dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.






<!--HONumber=Jun16_HO2-->


