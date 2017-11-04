Neste passo, cria manualmente o serviço de escuta do grupo de disponibilidade no Gestor de clusters de ativação pós-falha e o SQL Server Management Studio.

1. Abra o Gestor de clusters de ativação pós-falha do nó que aloja a réplica primária.

2. Selecione o **redes** nó e, em seguida, tenha em atenção o nome de rede de cluster. Este nome é utilizado na variável $ClusterNetworkName do script do PowerShell.

3. Expanda o nome do cluster e, em seguida, clique em **funções**.

4. No **funções** painel, faça duplo clique o nome do grupo de disponibilidade e, em seguida, selecione **adicionar recursos** > **ponto de acesso de cliente**.
   
    ![Adicionar o ponto de acesso de cliente para o grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. No **nome** caixa, crie um nome para este novo serviço de escuta, clique em **seguinte** duas vezes e, em seguida, clique em **concluir**.  
    Não colocar o serviço de escuta ou recurso online neste momento.

6. Clique em de **recursos** separador e, em seguida, expanda o ponto de acesso de cliente que acabou de criar. 
    O recurso de endereço IP para cada rede de cluster do cluster é apresentado. Se se tratar de uma solução apenas de Azure, é apresentado apenas um recurso de endereço IP.

7. Efetue um dos seguintes procedimentos:
   
   * Para configurar uma solução híbrida:
     
        a. O recurso de endereço IP que corresponde à sua sub-rede no local com o botão direito e, em seguida, selecione **propriedades**. Tenha em atenção que o nome de endereço IP e o nome de rede.
   
        b. Selecione **endereço IP estático**, atribua um endereço IP não utilizado e, em seguida, clique em **OK**.
 
   * Para configurar uma solução apenas de Azure:

        a. Faça duplo clique no recurso de endereço IP que corresponde à sua sub-rede do Azure e, em seguida, selecione **propriedades**.
       
       > [!NOTE]
       > Se o serviço de escuta falhar posteriormente fique online devido a um endereço IP em conflito selecionado por DHCP, pode configurar um endereço IP estático válido nesta janela de propriedades.
       > 
       > 

       b. Da mesma **endereço IP** janela de propriedades, altere o **nome de endereço IP**.  
        Este nome é utilizado na variável $IPResourceName do script do PowerShell. Se a solução abranger várias redes virtuais do Azure, repita este passo para cada recurso IP.

