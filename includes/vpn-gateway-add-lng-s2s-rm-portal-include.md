1. No portal, em **Todos os recursos**, clique em **+Adicionar**. 
2. Na caixa de pesquisa da página **Tudo**, escreva **Gateway de rede Local** e, em seguida, clique para procurar. Isto irá devolver uma lista. Clique em **Gateway de rede Local** para abrir a página e, em seguida, clique em **Criar** para abrir a página **Criar gateway de rede local**.

  ![criar gateway de rede local](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. Na página **Criar gateway de rede local**, especifique os valores para o objeto do gateway de rede local.

  - **Nome:** Especifique um nome para seu objeto de gateway de rede local.
  - **Endereço IP:** Este é o endereço IP público do dispositivo VPN a que pretende estabelecer ligação. Especifique um endereço IP público válido. O endereço IP não pode estar protegido por NAT e tem de estar acessível ao Azure. Se não tiver o endereço IP no momento, pode usar os valores mostrados na captura de ecrã, mas precisará voltar atrás e substituir o endereço IP do marcador de posição pelo endereço IP público do seu dispositivo VPN. Caso contrário, o Azure não será capaz de se ligar.
  - O **Espaço de endereços** refere-se aos intervalos de endereços da rede que esta rede local representa. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem aos intervalos de outras redes às quais pretende ligar. O Azure irá encaminhar o intervalo de endereços que especificou no endereço IP do dispositivo VPN no local. *Utilize os seus próprios valores aqui, não os valores apresentados na captura de ecrã*.
  - **Configurar definições de BGP:** utilize apenas quando configurar o BGP. Caso contrário, não selecione esta opção.
  - **Subscrição:** Verifique se é apresentada a subscrição correta.
  - **Grupo de Recursos:** Selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
  - **Localização:** Selecione a localização em que este objeto será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.

4. Quando tiver terminado de especificar os valores, clique em **Criar** na parte inferior da página para criar o gateway de rede local.