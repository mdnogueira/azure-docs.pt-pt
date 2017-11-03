O serviço de escuta do grupo de disponibilidade é um nome de rede e endereços IP que o grupo de disponibilidade do SQL Server escuta na. Para criar o serviço de escuta do grupo de disponibilidade, efetue o seguinte:

1. <a name="getnet"></a>Obter o nome do recurso de rede de cluster.

    a. Utilize o RDP para ligar à máquina virtual do Azure que aloja a réplica primária. 

    b. Abra o Gestor de clusters de ativação pós-falha.

    c. Selecione o **redes** nós e tenha em atenção o nome de rede de cluster. Utilize este nome no `$ClusterNetworkName` variáveis do script do PowerShell. Na imagem seguinte é o nome de rede de cluster **rede de Cluster 1**:

   ![Nome de rede de cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Adicione o ponto de acesso de cliente.  
    O ponto de acesso de cliente é o nome de rede que as aplicações utilizam para ligar às bases de dados num grupo de disponibilidade. Crie o ponto de acesso de cliente no Gestor de clusters de ativação pós-falha.

    a. Expanda o nome do cluster e, em seguida, clique em **funções**.

    b. No **funções** painel, faça duplo clique o nome do grupo de disponibilidade e, em seguida, selecione **adicionar recursos** > **ponto de acesso de cliente**.

   ![Ponto de acesso de cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. No **nome** caixa, crie um nome para este novo serviço de escuta. 
   O nome para o novo serviço de escuta é o nome de rede que as aplicações utilizam para ligar às bases de dados no grupo de disponibilidade do SQL Server.
   
    d. Para concluir a criação do serviço de escuta, clique em **seguinte** duas vezes e, em seguida, clique em **concluir**. Não colocar o serviço de escuta ou recurso online neste momento.

3. <a name="congroup"></a>Configure o recurso IP para o grupo de disponibilidade.

    a. Clique em de **recursos** separador e, em seguida, expanda o ponto de acesso de cliente que criou.  
    O ponto de acesso de cliente está offline.

   ![Ponto de acesso de cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Faça duplo clique o recurso IP e, em seguida, clique em propriedades. Tome nota do nome do endereço IP e utilizam-na `$IPResourceName` variáveis do script do PowerShell.

    c. Em **endereço IP**, clique em **endereço IP estático**. Defina o endereço IP, como o mesmo endereço que utilizou quando definiu o endereço de Balanceador de carga no portal do Azure.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Faça com que o recurso do grupo de disponibilidade do SQL Server dependa o ponto de acesso de cliente.

    a. No Gestor de clusters de ativação pós-falha, clique em **funções**e, em seguida, clique em seu grupo de disponibilidade.

    b. No **recursos** separador em **outros recursos**, faça duplo clique o grupo de recursos de disponibilidade e, em seguida, clique em **propriedades**. 

    c. No separador de dependências, adicione o nome do recurso de ponto (o serviço de escuta) de acesso de cliente.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Clique em **OK**.

5. <a name="listname"></a>Marca o recurso de ponto de acesso de cliente dependente do endereço IP.

    a. No Gestor de clusters de ativação pós-falha, clique em **funções**e, em seguida, clique em seu grupo de disponibilidade. 

    b. No **recursos** separador, faça duplo clique o recurso de ponto de acesso de cliente em **nome do servidor**e, em seguida, clique em **propriedades**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Clique em de **dependências** separador. Certifique-se de que o endereço IP é uma dependência. Se não for, defina uma dependência no endereço IP. Se existirem vários recursos listados, certifique-se de que os endereços IP têm ou não e, dependências. Clique em **OK**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. O nome do serviço de escuta com o botão direito e, em seguida, clique em **colocar Online**. 

    >[!TIP]
    >Pode validar se as dependências estão corretamente configuradas. No Gestor de clusters de ativação pós-falha, aceda a funções, clique no grupo de disponibilidade, clique em **mais ações**e, em seguida, clique em **Mostrar relatório de dependências**. Quando as dependências se encontram corretamente configuradas, o grupo de disponibilidade está dependente o nome da rede e o nome da rede está dependente do endereço IP. 


6. <a name="setparam"></a>Defina os parâmetros de cluster no PowerShell.
    
    a. Copie o seguinte script do PowerShell para uma das suas instâncias do SQL Server. Atualize as variáveis para o seu ambiente.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Defina os parâmetros de cluster executando o script do PowerShell num de nós do cluster.  

    > [!NOTE]
    > Se as instâncias do SQL Server estão em diferentes regiões, terá de executar o script PowerShell duas vezes. A primeira vez, utilize o `$ILBIP` e `$ProbePort` da região do primeiro. Na segunda vez, utilize o `$ILBIP` e `$ProbePort` da região de segundo. O nome de rede de cluster e o nome de recurso IP do cluster são os mesmos. 
