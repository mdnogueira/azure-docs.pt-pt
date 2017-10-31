1. Clicar em **Ligar** cria e transfere um ficheiro do protocolo RDP (ficheiro .rdp). Clique em **Abrir** para utilizar este ficheiro.
2. Recebe um aviso a informar que o ficheiro `.rdp` é de um publicador desconhecido. É uma situação normal. Na janela Ambiente de Trabalho Remoto, clique em **Ligar** para continuar.
   
    ![Captura de ecrã de um aviso sobre um publicador desconhecido.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Escreva as credenciais para uma conta da máquina virtual e clique em **OK**.
   
     **Conta local** – trata-se normalmente do nome de utilizador e da palavra-passe da conta local que especificou quando criou a máquina virtual. Neste caso, o domínio é o nome da máquina virtual e é introduzido como *nomedavm*&#92;*nomedeutilizador*.  
   
    **VM associada ao domínio** – se a VM pertence a um domínio, introduza o nome de utilizador no formato *Domínio*&#92;*Nomedeutilizador*. A conta também tem de estar no grupo Administradores ou terem sido concedidos privilégios de acesso remoto à VM.
   
    **Controlador de domínio** – se a VM for um controlador de domínio, escreva o nome de utilizador e a palavra-passe de uma conta de administrador para esse domínio.
4. Clique em **Sim** para confirmar a identidade da máquina virtual e concluir o início de sessão.
   
   ![Captura de ecrã que mostra uma mensagem sobre a confirmação da identidade da VM.](./media/virtual-machines-log-on-win-server/cert-warning.png)

