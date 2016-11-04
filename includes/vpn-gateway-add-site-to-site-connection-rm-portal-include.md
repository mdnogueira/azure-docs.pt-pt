1. Localize o seu gateway de rede virtual e clique em **Todas as definições** para abrir o painel **Definições**.
2. No painel **Definições**, clique em **Ligações** e em **Adicionar** na parte superior do painel para abrir o painel **Adicionar ligação**.
   
    ![Criar uma ligação Site a Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)
3. No painel **Adicionar ligação** atribua um **Nome** à sua ligação. 
4. Para **Tipo de ligação**, selecione **Site a site(IPSec)**.
5. Para **Gateway de rede virtual**, o valor é fixo porque está a ligar a partir deste gateway.
6. Para **Gateway de rede local**, clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que pretende utilizar. 
7. Para **Chave Partilhada**, o valor aqui tem de corresponder ao valor que está a utilizar para o seu dispositivo VPN local. Se o seu dispositivo VPN na rede local não fornecer uma chave partilhada, pode criar uma e introduzi-la aqui e no dispositivo local. O mais importante é que ambos os valores correspondam.
8. Os restantes valores para **Subscrição**, **Grupo de Recursos** e **Localização** são fixos.
9. Clique em **OK** para criar a ligação. Verá *A Criar Ligação* a piscar no ecrã.
10. Quando a ligação estiver concluída, será apresentada painel **Ligações** do seu Gateway.
    
    ![Criar uma ligação Site a Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)

<!--HONumber=Sep16_HO3-->


