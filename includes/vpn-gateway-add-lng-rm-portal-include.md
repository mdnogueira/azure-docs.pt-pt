1. No portal, navegue até **Novo** > **Redes** > **Gateway de rede local**.
   
    ![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)
2. No painel **Criar gateway de rede local**, especifique um **Nome** para o objeto do gateway de rede local.
3. Especifique um **Endereço IP** público válido para o dispositivo VPN ou para o gateway de rede virtual ao qual pretende ligar.<br>Se esta rede local representar uma localização no local, este é o endereço IP público do dispositivo VPN ao qual pretende ligar. Não pode estar protegido por NAT e tem de estar acessível ao Azure.<br>Se esta rede local representar outra VNet, irá especificar o endereço IP público atribuído ao gateway de rede virtual para essa VNet.<br>
4. O **Espaço de endereços** refere-se aos intervalos de endereços da rede que esta rede local representa. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem aos intervalos de outras redes às quais pretende ligar.
5. Para **Subscrição**, verifique se é apresentada a subscrição correta.
6. Para **Grupo de Recursos**, selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
7. Para **Localização**, selecione a localização em que este objeto será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.
8. Clique em **Criar** para criar o gateway da rede local.

<!--HONumber=Sep16_HO3-->


