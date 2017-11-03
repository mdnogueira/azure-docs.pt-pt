Esta passagem, irá aprender a utilizar o **Salesforce - quando é criado um objecto** trigger para iniciar um fluxo de trabalho de aplicação lógica, quando é criado um novo fabrico no Salesforce.

> [!NOTE]
> Obter-á pedido para iniciar sessão na sua conta do Salesforce, se não tiver já criado uma *ligação* Salesforce.  
> 
> 

1. Introduza *salesforce* na caixa de pesquisa no designer de aplicações lógicas, em seguida, selecione o **Salesforce - quando é criado um objecto** acionador.  
   ![Imagem de Acionador de Salesforce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. O **quando é criado um objecto** controlo é apresentado.  
   ![Imagem de Acionador de Salesforce 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Selecione o **tipo de objeto** , em seguida, selecione *levar* da lista de objetos. Neste passo, é com a indicação de que está a criar um acionador que irá notificar a sua aplicação lógica sempre que é criado um novo fabrico no Salesforce.   
   ![Imagem de Acionador de Salesforce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. E já está. Criou o acionador. No entanto, terá de criar pelo menos uma ação para tornar esta uma aplicação lógica válida.    
   ![Imagem de Acionador de Salesforce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

Neste momento, a aplicação lógica foi configurada com um acionador que começará a execução dos outros acionadores e ações no fluxo de trabalho é criado um novo item na sua Salesforce.  

