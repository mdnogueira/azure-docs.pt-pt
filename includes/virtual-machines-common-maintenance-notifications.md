
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>VMs de vista agendadas para manutenção no portal

Assim que estiver agendada uma onda de manutenção planeada e as notificações são enviadas, pode observar a lista de máquinas virtuais que são afetadas por wave a manutenção futura. 

Pode utilizar o portal do Azure e procure VMs agendadas para manutenção.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel de navegação esquerdo, clique em **máquinas virtuais**.

3. No painel de máquinas virtuais, clique o **colunas** botão para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Manutenção** -mostra o estado de manutenção para a VM. Seguem-se os valores possíveis:
      
      | Valor | Descrição |
      |-------|-------------|
      | Começar agora | A VM está na janela de manutenção de self-service que permite-lhe iniciar a manutenção por si. Consulte abaixo sobre como iniciar manutenção a VM | 
      | Agendadas | A VM está agendada para manutenção sem qualquer opção para que possa iniciar manutenção. Pode saber da janela de manutenção, selecionando a janela de Auto-agendada nesta vista ou clicando na VM | 
      | Foi concluída | Iniciou com êxito e concluída a manutenção no VM. | 
      | Foi ignorada| Selecionou a iniciar manutenção sem sucesso. Azure cancelou a manutenção para a VM e será o reprogramar num horário posterior | 
      | Tente novamente mais tarde| Que selecionou para iniciar a manutenção e o Azure não conseguiu efetuar o seu pedido. Neste caso, pode tentar novamente dentro de um período posterior. | 
   
   **Manutenção profissional para Active Directory** -mostra a janela de tempo quando Self-pode iniciar manutenção nas suas VMs.
   
   **Manutenção agendada** -mostra a janela de tempo, quando o Azure irá reiniciar a VM para concluir a manutenção. 




## <a name="notification-and-alerts-in-the-portal"></a>Notificações e alertas no portal do

Azure comunica um agendamento para a manutenção planeada, enviando um e-mail para o grupo de proprietário e os coproprietários de subscrição. Pode adicionar destinatários adicionais e canais para esta comunicação através da criação de alertas de registo de atividade do Azure. Para obter mais informações, consulte [monitorizar a atividade subscrição com o registo de atividade do Azure] (../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Monitor**. 
3. No **Monitor - registo de atividade** painel, selecione **alertas**.
4. No **Monitor - alertas** painel, clique em **+ Adicionar alerta de registo de atividade**.
5. Preencha as informações no **Adicionar alerta de registo de atividade** e certifique-se de que define o seguinte página **critérios**: **tipo**: manutenção **estado**: Todos os (não definido Estado ativo ou resolvido) **nível**: todos os
    
Para obter mais informações sobre como configurar alertas de registo de atividade, consulte [criam alertas de registo de atividade](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Iniciar manutenção na VM a partir do portal

Ao observar os detalhes VM, poderá ver mais detalhes relacionados com a manutenção.  
Na parte superior da vista de detalhes de VM, será adicionado um novo friso de notificação se a VM está incluída numa onda de manutenção planeada. Além disso, é adicionada uma nova opção para iniciar manutenção sempre que possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planeada. A partir daí, irá conseguir **iniciar manutenção** na VM.

Assim que começar a manutenção, a máquina virtual será reiniciada e o estado de manutenção será atualizado para refletir o resultado dentro de alguns minutos.

Se em falta a janela de onde pode começar a manutenção, ainda será capaz de ver a janela quando a VM será reiniciada pelo Azure. 
