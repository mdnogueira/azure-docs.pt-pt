

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Selecione **Novo** > **Web + Móvel** > **Hub de Notificação**.
   
      ![Portal do Azure – criar hubs de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Na caixa **Hub de Notificação**, escreva um nome exclusivo. Selecione a **Região**, a **Subscrição** e o **Grupo de Recursos** (se já tiver um). 
   
    Se já tiver um espaço de nomes do service bus no qual quer criar o hub, faça o seguinte:

    a. Na área **Espaço de nomes**, selecione a ligação **Selecionar Existente**. 
   
    b. Selecione **Criar**.

    Se ainda não tiver um espaço de nomes do service bus, pode utilizar o nome predefinido, que é criado tendo por base o nome do hub (se esse nome do espaço de nomes estiver disponível).
   
      ![Portal do Azure – definir as propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

    Depois de criar o espaço de nomes e o hub de notificação, é aberto o portal do Azure. 
   
      ![Portal do Azure - página do portal do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)

4. Selecione **Definições** > **Políticas de Acesso**. Anote as duas cadeias de ligação que tem à sua disposição. Vai precisar destas para processar as notificações push mais tarde.
   
      ![Portal do Azure – cadeias de ligação do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

