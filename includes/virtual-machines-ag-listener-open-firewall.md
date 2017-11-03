Neste passo, cria uma regra de firewall para abrir a porta de pesquisa para o endpoint com balanceamento de carga (59999, conforme especificado anteriormente) e outra regra para abrir a porta de serviço de escuta do grupo de disponibilidade. Uma vez que criou o ponto final com balanceamento de carga em VMs que contêm as réplicas do grupo de disponibilidade, terá de abrir a porta de pesquisa e a serviço de escuta a porta nas respetivas VMs.

1. Em VMs que alojam as réplicas, iniciar **Firewall do Windows com segurança avançada**.

2. Clique com botão direito **regras de entrada**e, em seguida, clique em **nova regra**.

3. No **tipo de regra** página, selecione **porta**e, em seguida, clique em **seguinte**.

4. No **protocolo e portas** página, selecione **TCP**, tipo **59999** no **portas locais específicas** caixa e, em seguida, clique em  **Seguinte**.

5. No **ação** página, mantenha **permitir a ligação** selecionada e, em seguida, clique em **seguinte**.

6. No **perfil** página, aceite as predefinições e, em seguida, clique em **seguinte**.

7. No **nome** na página de **nome** texto caixa, especifique um nome de regra, tal como **sempre no serviço de escuta de sonda de porta**e, em seguida, clique em **concluir**.

8. Repita os passos anteriores para a porta de serviço de escuta do grupo de disponibilidade (tal como especificado anteriormente no parâmetro $EndpointPort do script) e, em seguida, especifique um nome de regra adequada, tais como **sempre no serviço de escuta de porta**.

