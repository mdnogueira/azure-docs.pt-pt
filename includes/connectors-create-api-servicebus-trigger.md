Eis como utilizar o **Service Bus - quando é recebida uma mensagem numa fila** trigger para iniciar um fluxo de trabalho de aplicação lógica, quando um novo item é enviado para uma fila do Service Bus.  

> [!NOTE]
> Será solicitado para iniciar sessão com a cadeia de ligação do Service Bus, se não tiver criado uma ligação ao Service Bus.  
> 
> 

1. Na caixa de pesquisa no designer de aplicações lógica, introduza **service bus**. Em seguida, selecione o **Service Bus - quando é recebida uma mensagem numa fila** acionador.  
   ![Imagem de Acionador de barramento de serviço 1](./media/connectors-create-api-servicebus/trigger-1.png)   
2. O **quando é recebida uma mensagem numa fila** é apresentada a caixa de diálogo.  
   ![Imagem de Acionador de barramento de serviço 2](./media/connectors-create-api-servicebus/trigger-2.png)   
3. Introduza o nome da fila do Service Bus que gostaria de acionamento para monitorizar.   
   ![Imagem de Acionador de Service Bus 3](./media/connectors-create-api-servicebus/trigger-3.png)   

Neste momento, a aplicação lógica foi configurada com um acionador. Quando é recebido um novo item na fila selecionada, o acionador irá iniciar a execução dos outros acionadores e ações no fluxo de trabalho.    

