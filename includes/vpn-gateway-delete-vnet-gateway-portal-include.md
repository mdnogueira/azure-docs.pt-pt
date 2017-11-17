### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passo 1: Navegue para o gateway de rede virtual

1. No [portal do Azure](https://portal.azure.com), navegue para **todos os recursos**. 
2. Para abrir o painel de gateway de rede virtual, navegue para o gateway de rede virtual que pretende eliminar e clique no mesmo.

### <a name="step-2-delete-connections"></a>Passo 2: Ligações de eliminação

1. No painel de gateway da rede virtual, clique em **ligações** para ver todas as ligações para o gateway.
2. Clique em de **'...'** na linha do nome da ligação, em seguida, selecione **eliminar** na lista pendente.
3. Clique em **Sim** para confirmar que pretende eliminar a ligação. Se tiver várias ligações, elimine a cada ligação.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Passo 3: Elimine o gateway de rede virtual

Lembre-se de que se tiver uma configuração de P2S para esta VNet, além da sua configuração de S2S, eliminação do gateway de rede virtual será automaticamente desligue todos os clientes de P2S sem aviso.

1. No painel de gateway de rede virtual, clique em **descrição geral**.
2. No **descrição geral** painel, clique em **eliminar** para eliminar o gateway.
