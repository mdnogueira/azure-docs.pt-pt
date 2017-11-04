1. No portal, em **Todos os recursos**, clique em **+Adicionar**. No **tudo** caixa de pesquisa do painel, escreva **gateway de rede Local**, em seguida, clique aqui para obter uma lista de recursos. Clique em **Gateway de rede Local** para abrir o painel e, em seguida, clique em **Criar** para abrir o painel **Criar gateway de rede local**.
   
    ![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)

2. No painel **Criar gateway de rede local**, especifique um **Nome** para o objeto do gateway de rede local. Se possível, utilize algo intuitiva, tal como **ClassicVNetLocal** ou **TestVNet1Local**. Isto torna mais fácil para identificar o gateway de rede local no portal.
3. Especifique um público válido **endereço IP** para o dispositivo VPN ou o gateway de rede virtual à qual pretende ligar.<br>**Se esta rede local representa uma localização no local:** especifique o endereço IP público do dispositivo VPN que pretende ligar. Não pode estar protegido por NAT e tem de estar acessível ao Azure.<br>**Se esta rede local representa a outra VNet:** especifique o endereço IP público que foi atribuído ao gateway de rede virtual para essa VNet.<br>**Se ainda não tiver o endereço IP:** pode constituem um endereço IP do marcador de posição válido e, em seguida, voltar atrás e modificar esta definição antes de ligar.
4. O **Espaço de endereços** refere-se aos intervalos de endereços da rede que esta rede local representa. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem intervalos outras redes aos quais se ligar.
5. Para **Subscrição**, verifique se é apresentada a subscrição correta.
6. Para **Grupo de Recursos**, selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
7. Para **localização**, selecione a localização na qual este recurso será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.
8. Clique em **Criar** para criar o gateway da rede local.

