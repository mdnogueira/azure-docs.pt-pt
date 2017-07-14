### Determinar o nome DNS da máquina virtual
<a id="determine-the-dns-name-of-the-virtual-machine" class="xliff"></a>
Para ligar ao Motor de Base de Dados do SQL Server a partir de outro computador, tem de saber qual é o nome DNS (Sistema de Nomes de Domínio) da máquina virtual. (Este é o nome que a Internet utiliza para identificar a máquina virtual. Pode utilizar o endereço IP, mas este pode mudar quando o Azure move recursos para redundância ou manutenção. O nome DNS será estável porque pode ser redirecionado para um novo endereço IP.)  

1. No Portal do Azure (ou no passo anterior), selecione **Máquinas virtuais (clássico)**.
2. Selecione a sua VM do SQL.
3. No painel **Máquina virtual**, copie o **nome DNS** da máquina virtual.
   
    ![Nome DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### Ligar ao Motor da Base de Dados a partir de outro computador
<a id="connect-to-the-database-engine-from-another-computer" class="xliff"></a>
1. Num computador ligado à Internet, abra o SQL Server Management Studio.
2. Na caixa de diálogo **Ligar ao Servidor** ou **Ligar ao Motor de Base de Dados**, na caixa **Nome do servidor**, introduza o nome DNS da máquina virtual (determinado na tarefa anterior) e um número de porta de ponto final público no formato *NomeDNS,númerodeporta* como, por exemplo, **mysqlvm.cloudapp.net,57500**.
   
    ![Ligar através do SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Se não se lembrar do número de porta de ponto final público que criou, pode encontrá-lo na área **Pontos finais** do painel **Máquina virtual**.
   
    ![Porta Pública](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.
4. Na caixa **Início de sessão**, escreva o nome de um início de sessão que tenha criado numa tarefa anterior.
5. Na caixa **Palavra-passe**, escreva a palavra-passe do início de sessão que criou na respetiva tarefa anterior.
6. Clique em **Ligar**.

