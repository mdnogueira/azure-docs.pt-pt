1. Encontre o seu gateway de rede virtual.
2. Clique em **Ligações**. No topo do painel Ligações, clique em **+Adicionar** para abrir o painel **Adicionar ligação**.
   
    ![Criar uma ligação Site a Site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection.png)
3. No painel **Adicionar ligação** atribua um **Nome** à sua ligação. 
4. Para **Tipo de ligação**, selecione **Site a site(IPSec)**.
5. Para **Gateway de rede virtual**, o valor é fixo porque está a ligar a partir deste gateway.
6. Para **Gateway de rede local**, clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que pretende utilizar. 
7. Para **Chave Partilhada**, o valor aqui tem de corresponder ao valor que está a utilizar para o seu dispositivo VPN local no local. No exemplo, utilizámos "abc123", mas pode e (deve) utilizar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor que especificou quando configurou o seu dispositivo VPN.
8. Os restantes valores para **Subscrição**, **Grupo de Recursos** e **Localização** são fixos.
9. Clique em **OK** para criar a ligação. Verá *A Criar Ligação* a piscar no ecrã.
10. Quando a ligação estiver concluída, é apresentada no painel **Ligações** do gateway de rede virtual.
    
    ![Criar uma ligação Site a Site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connectionstatus450.png)

