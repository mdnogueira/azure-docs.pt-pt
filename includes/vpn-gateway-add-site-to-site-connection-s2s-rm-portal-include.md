1. Navegue até ao painel e abra-o para o seu gateway de rede virtual. Existem múltiplas formas de efetuar este procedimento. No nosso exemplo, navegámos até ao gateway 'VNet1GW' acedendo a **TestVNet1 -> Descrição geral -> Dispositivos ligados -> VNet1GW**.
2. No painel VNet1GW, clique em **Ligações**. No topo do painel Ligações, clique em **+Adicionar** para abrir o painel **Adicionar ligação**.

    ![Criar uma ligação Site a Site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. No painel **Adicionar ligação**, preencha os valores para criar a ligação.

  - **Nome:** Atribua um nome à sua ligação. Usamos **VNet1toSite2** no nosso exemplo.
  - **Tipo de ligação:** Selecione **Site a site(IPSec)**.
  - **Gateway de rede virtual:** O valor é fixo porque está a ligar a partir deste gateway.
  - **Gateway de rede local:** Clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que pretende utilizar. No nosso exemplo, usamos **Site2**.
  - **Chave Partilhada:** Aqui, o valor tem de corresponder ao valor que está a utilizar para o seu dispositivo VPN local no local. No exemplo, utilizámos "abc123", mas pode e (deve) utilizar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor que especificou quando configurou o seu dispositivo VPN.
  - Os restantes valores para **Subscrição**, **Grupo de Recursos** e **Localização** são fixos.

4. Clique em **OK** para criar a ligação. Verá *A Criar Ligação* a piscar no ecrã.
5. A ligação é apresentada no painel **Ligações** do gateway de rede virtual. O estado será alterado de *Desconhecido* para *A ligar*e, em seguida, *Efetuado com êxito*.