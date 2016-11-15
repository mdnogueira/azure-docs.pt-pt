## <a name="create-an-event-hub"></a>Criar um Hub de Eventos
1. Inicie sessão no [Portal do Azure][Portal do Azure] e clique em **Novo** na parte superior esquerda do ecrã.
2. Clique em **Recolha e análise de dados**, em seguida, clique em **Hubs de Eventos**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)
3. No painel **Criar espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)
4. Após se certificar de que o espaço de nomes está disponível, selecione o escalão de preço (Básico ou Standard). Escolha também uma subscrição do Azure, um grupo de recursos e a localização na qual pretende criar o recurso. 
5. Clique em **Criar** para criar o espaço de nome.
6. Na lista de espaços de nomes dos Hubs de Eventos, clique no espaço de nome criado recentemente.      
   
    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)
7. No painel de espaço de nomes, clique em **Hubs de Eventos**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)
8. Na parte superior do painel, clique em **Adicionar Hub de Eventos**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)
9. Escreva um nome para o seu Hub de Eventos e, em seguida, clique em **Criar**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)
10. Na lista de Hubs de Eventos, clique no nome de Hub de eventos criado recentemente. 
    
     ![](./media/event-hubs-create-event-hub/create-event-hub6.png)
11. No painel de espaço de nomes (não no painel de Hub de Eventos específico), clique em **Políticas de acesso partilhado**, e, em seguida, clique em **RootManageSharedAccessKey**.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub7.png)
12. Clique no botão copiar para copiar a cadeia de ligação **RootManageSharedAccessKey** para a área de transferência. Guarde esta cadeia de ligação para utilizar mais tarde no tutorial.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

O Hub de Eventos foi criado e tem as cadeias de ligação necessárias para enviar e receber eventos.

[Portal do Azure]: https://portal.azure.com/

<!--HONumber=Nov16_HO2-->


