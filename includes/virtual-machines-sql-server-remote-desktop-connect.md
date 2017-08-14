Utilize os seguintes passos para ligar à máquina virtual do SQL Server com o Ambiente de Trabalho Remoto:

1. Depois de a máquina virtual do Azure ser criada e estar em execução, clique no ícone Máquinas Virtuais no portal do Azure para ver as VMs.

1. Clique nas reticências, **...** , da nova VM.

1. Clique em **Ligar**.

   ![Ligar à VM no portal](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Abra o ficheiro **RDP** transferido pelo browser para a VM.

1. A Ligação ao Ambiente de Trabalho Remoto notifica-o de que não é possível identificar o publicador desta ligação remota. Clique em **Ligar** para continuar.

1. Na caixa de diálogo **Segurança do Windows**, clique em **Utilizar uma conta diferente**. Poderá ter de clicar em **Mais opções** para ver isto. Especifique o nome de utilizador e a palavra-passe que configurou quando criou a VM. Tem de adicionar uma barra invertida antes do nome de utilizador.

   ![Autenticação de ambiente de trabalho remoto](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.