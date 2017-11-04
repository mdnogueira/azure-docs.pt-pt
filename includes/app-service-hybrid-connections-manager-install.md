
1. No **ligações híbridas** painel, a ligação híbrida que acabou de criar, em seguida, clique a clique **a configuração do serviço de escuta**.
   
    ![Clique em configuração do serviço de escuta](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
2. O **propriedades da ligação híbrida** abre o painel. Em **Gestor de ligações híbridas no local**, escolha **transferir e configurar manualmente**, guardar o pacote transferido do HybridConnectionManager.msi e copie a cadeia de ligação de gateway.
   
    ![Clique aqui para instalar](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
3. A partir de uma linha de comandos de administrador, escreva o seguinte comando para iniciar o instalador:
   
        start HybridConnectionManager.msi
4. Após a execução do programa de instalação, clique em **não agora**, em seguida, procure a pasta de %ProgramFiles%\Microsoft\HybridConnectionManager, execute HCMConfigWizard.exe e clique em **Sim** no **conta de utilizador Controlo** caixa de diálogo.
5. Cole a cadeia de ligação híbrida que copiou anteriormente e clique em **OK**. 
   
    ![A instalar](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
6. Quando concluída a instalação, clique em **fechar**.
   
    ![Clique em Fechar](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
   
    No **ligações híbridas** painel, o **estado** coluna mostra agora **ligado**. 
   
    ![Estado ligado](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)

