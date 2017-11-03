#### <a name="to-install-mpio-on-the-host"></a>A instalação do MPIO no anfitrião
1. Abra o Gestor de servidor no anfitrião do Windows Server. Por predefinição, o Gestor de servidores inicia quando um membro do grupo Administradores inicia sessão num computador que está a executar o Windows Server 2012 R2 ou Windows Server 2012. Se o Gestor de servidor já não estiver aberto, clique em **Iniciar > Gestor de servidor**.
   
    ![Gestor de servidor](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Clique em **Gestor de servidor > Dashboard > Adicionar funções e funcionalidades**. Esta ação inicia o **para adicionar funções e funcionalidades** assistente.
   
    ![Assistente para adicionar funções e funcionalidades 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. No **para adicionar funções e funcionalidades** assistente, efetue o seguinte procedimento:
   
   * No **antes de começar** página, clique em **seguinte**.
   * No **selecionar tipo de instalação** página, aceite a predefinição de **baseada em funções ou baseada em funcionalidade** instalação. Clique em **Seguinte**.
     
       ![Assistente para adicionar funções e funcionalidades 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * No **servidor de destino selecione** página, escolha **selecione um servidor no agrupamento de servidores**. O servidor de anfitrião deve ser detetado automaticamente. Clique em **Seguinte**.
   * No **selecionar funções de servidor** página, clique em **seguinte**.
   * No **selecionar funcionalidades** página, selecione **Multipath i / o**e clique em **seguinte**.
     
       ![Assistente para adicionar funções e funcionalidades 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * No **confirmar seleções de instalação** página, confirme a seleção e, em seguida, selecione **reinicie o servidor de destino automaticamente se necessário**, conforme mostrado abaixo. Clique em **Instalar**.
     
       ![Assistente para adicionar funções e funcionalidades 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Será notificado quando a instalação estiver concluída. Clique em **Fechar** para fechar o assistente.
     
       ![Assistente para adicionar funções e funcionalidades 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

