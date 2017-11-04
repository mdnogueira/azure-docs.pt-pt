## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
1. Selecione a VM que pretende Reimplementar, em seguida, selecione o *Reimplementar* clique no botão no *definições* painel. Poderá ter de deslocar para baixo para ver o **suporte e resolução de problemas** secção que contém o botão 'Reimplementar' como no exemplo seguinte:
   
    ![Painel VM do Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Para confirmar a operação, selecione o *Reimplementar* botão:
   
    ![Volte a implementar um painel VM](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. O **estado** da VM é alterado para *atualização* como a VM se prepara para voltar a implementar, conforme mostrado no exemplo seguinte:
   
    ![Atualizar a VM](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. O **estado** muda para *inicial* como VM arranca num novo anfitrião do Azure, conforme mostrado no exemplo seguinte:
   
    ![A partir de VM](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Depois da VM termina o processo de arranque, o **estado** , em seguida, regressar ao *executar*, que indica que a VM tenha sido implementada novamente com êxito:
   
    ![Execução de VM](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

