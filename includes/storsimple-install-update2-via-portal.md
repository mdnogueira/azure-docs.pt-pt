<!--author=alkohli last changed: 02/06/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização a partir do portal do Azure

1. Na página do serviço StorSimple, selecione o seu dispositivo. Navegue para **Dispositivos** > **Manutenção**.
2. Na parte inferior da página, clique em **Procurar Atualizações**. É criada uma tarefa para procurar as atualizações disponíveis. É notificado quando a tarefa for concluída com êxito.
3. Na secção **Atualizações de Software** da mesma página, estão disponíveis novas atualizações de software. Recomendamos que reveja as notas de versão antes de aplicar uma atualização ao seu dispositivo.
4. Na parte inferior da página, clique em **Instalar Atualizações**e, em seguida, em **OK**.
5. Na caixa de diálogo **Instalar atualizações**, certifique-se de que seguiu as recomendações e selecione **Compreendo o requisito acima e estou pronto para atualizar o meu dispositivo** e clique no botão de verificação.
   
    ![Mensagem de confirmação](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. Um conjunto de verificações de pré-requisitos é iniciado. Estas verificações incluem:
   
   * **Verificações do estado de funcionamento do controlador** para verificar se os dois controladores do dispositivo estão em bom estado de funcionamento e online.
   * **Verificações do estado de funcionamento do componente de hardware** para verificar se todos os componentes de hardware do seu dispositivo StorSimple estão em bom estado de funcionamento.
   * **Verificações de DATA 0** para verificar se DATA 0 está ativado no seu dispositivo. Se esta interface não estiver ativada, terá de ativá-la e, em seguida, tentar novamente.
   * **Verificações de DATA 2 e DATA 3**  para verificar se as interfaces de rede DATA 2 e DATA 3 não estão ativadas. Se estas interfaces estiverem ativadas, terá de desativá-las e então tentar atualizar o dispositivo. Esta verificação é executada apenas se estiver a atualizar a partir de um dispositivo com software GA. Os dispositivos que executem as versões 0.1, 0.2 ou 0.3 não precisam desta verificação.
   * **Verificação de gateway** em qualquer dispositivo que execute uma versão anterior à Atualização 1. Esta verificação é efetuada em todos os dispositivos com software da pré-atualização 1, mas falha nos dispositivos que têm um gateway configurado para uma interface de rede diferente de DATA 0.
     
     A atualização é aplicada se todas as verificações forem concluídas com êxito. Será notificado quando as verificações estiverem em curso.
     
     ![Notificação de pré-verificação](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     Segue-se um exemplo no qual as verificações falharam. Tem de verificar se os dois controladores do dispositivo estão em bom estado de funcionamento e online. Também é necessário verificar o estado de funcionamento dos componentes de hardware. Neste exemplo, os componentes Controlador 0 e Controlador 1 necessitam de atenção. Poderá ter de contactar o Suporte da Microsoft se não conseguir resolver estes problemas.
     
       ![Falha das verificações](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. Depois de as verificações serem concluídas com êxito, é criada uma tarefa de atualização. Será notificado quando a tarefa de atualização for criada com êxito.
   
    ![Criação de tarefa de atualização](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    A atualização é então aplicada ao seu dispositivo.
    
8. Para monitorizar o progresso da tarefa de atualização, clique em **Ver Tarefa**. Na página **Tarefas**, pode ver o progresso da atualização.
9. A atualização demora algumas horas a ser concluída. Selecione a tarefa de atualização e clique em **Detalhes** para ver os detalhes da tarefa em qualquer altura.
10. Depois de a tarefa estar concluída, navegue para a página **Manutenção** e desloque para baixo até **Atualizações de Software**.

