1. No Portal do Azure, navegue até **Novo** **>** **Redes** **>** **Gateway de rede local**.

    ![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. No painel **Criar gateway de rede local**, especifique um **Nome** para o objeto do gateway de rede local.
 
3. Especifique um **Endereço IP** para o seu gateway. Este é o endereço IP do dispositivo VPN externo a que pretende ligar. Não pode estar protegido por NAT e tem de estar acessível ao Azure.

4. O **Espaço de Endereços** refere-se aos intervalos de endereços na rede local (normalmente, no local). Pode adicionar vários intervalos de espaço de endereços. Os intervalos que introduzir aqui não se podem sobrepor a nenhum dos intervalos de espaço de endereços que está a utilizar numa das redes virtuais que irão comunicar através do gateway.  Terá de coordenar com a sua configuração no local, bem como com os espaços de endereços da sua rede virtual do Azure.
 
5. Para **Subscrição**, verifique se é apresentada a subscrição correta.

6. Para **Grupo de Recursos**, selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado. Para criar um novo grupo de recursos, escreva o nome na caixa. Para selecionar um grupo de recursos já criado, clique em **Grupo de Recursos** para abrir o painel **Grupo de Recursos** e selecione o grupo de recursos que pretende utilizar.

7. Para **Localização**, se estiver a criar um novo gateway de rede local, pode utilizar a mesma localização do gateway da rede virtual. No entanto, não é obrigatório. O gateway da rede local pode estar numa localização diferente. 

8. Deixe “Afixar ao dashboard” selecionado se pretender encontrar este gateway de rede local facilmente no dashboard.

9. Clique em **Criar** para criar o gateway da rede local. Verá “A implementar gateway da rede local” no dashboard.

10. Quando o gateway da rede local tiver sido criado, será aberto no portal para que o possa ver.

    



<!--HONumber=Jun16_HO2-->


