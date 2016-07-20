## Criar um Hub de Eventos

1. Inicie sessão no [Portal Clássico do Azure][] e clique em **NOVO** na parte inferior do ecrã.

2. Clique em **Serviços Aplicacionais**, em seguida, **Service Bus**, **Hub de Eventos** e, depois, **Criação Rápida**.

    ![][1]

3. Escreva um nome para o seu Hub de Eventos, selecione a região pretendida e, em seguida, clique em **Criar um novo Hub de Eventos**.

    ![][2]

4. Se não selecionou explicitamente um espaço de nomes existente de uma determinada região, o portal cria um espaço de nomes (normalmente ***nome do hub de eventos*-ns**). Clique nesse espaço de nomes (neste exemplo, **eventhub ns**).

    ![][3]

5. Na parte inferior da página, clique em **Informações de Ligação**. Clique no botão copiar (mostrado na figura seguinte) para copiar a cadeia de ligação **RootManageSharedAccessKey** para a área de transferência. Guarde esta cadeia de ligação para utilizar mais tarde no tutorial.

    ![][4]

O Hub de Eventos foi criado e tem as cadeias de ligação necessárias para enviar e receber eventos.

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png


<!--HONumber=Jun16_HO2-->


