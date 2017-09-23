* Em **Adicionar vCenter**, especifique um nome amigável para o anfitrião vSphere ou o servidor vCenter e, em seguida, especifique o endereço IP ou FQDN do servidor. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar os pedidos numa porta diferente. Selecione a conta que está a ligar vCenter do VMware ou ao servidor vSphere ESXi. Clique em **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Se estiver a adicionar o servidor VMware vCenter ou o anfitrião do VMware vSphere com uma conta que não tem privilégios de administrador no vCenter ou no servidor de anfitrião, certifique-se de que a conta tem estes privilégios ativados: Datacenter, Arquivo de Dados, Pasta, Anfitrião, Rede, Recurso, Máquina Virtual e Comutador Distribuído do vSphere. Além disso, o servidor VMware vCenter necessita do privilégio de vista de armazenamento ativado.
