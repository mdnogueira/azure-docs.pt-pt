1. No Gestor de clusters de ativação pós-falha, expanda **funções**e, em seguida, realce o grupo de disponibilidade.  

2. No **recursos** separador, faça duplo clique o nome do serviço de escuta e, em seguida, clique em **propriedades**.

3. Clique em de **dependências** separador. Se forem apresentados vários recursos, certifique-se de que os endereços IP têm ou não e, dependências.  

4. Clique em **OK**.

5. O nome do serviço de escuta com o botão direito e, em seguida, clique em **colocar Online**.

6. Depois do serviço de escuta está online, no **recursos** separador, clique no grupo de disponibilidade e, em seguida, clique em **propriedades**.
   
    ![Configurar o recurso do grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Criar uma dependência no recurso de nome de serviço de escuta (não o recursos nome de endereço IP) e, em seguida, clique em **OK**.
   
    ![Adicione a dependência no nome do serviço de escuta](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Iniciar o SQL Server Management Studio e, em seguida, ligue para a réplica primária.

9. Aceda a **AlwaysOn alta disponibilidade** > **grupos de disponibilidade** > **\<AvailabilityGroupName\>**   >  **Serviços de escuta do grupo de disponibilidade**.  
    O nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha deve ser apresentado.

10. O nome do serviço de escuta com o botão direito e, em seguida, clique em **propriedades**.

11. No **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade com o $EndpointPort que utilizou anteriormente (neste tutorial, 1433 era a predefinição) e, em seguida, clique em **OK**.

