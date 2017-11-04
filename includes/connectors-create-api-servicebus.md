### <a name="prerequisites"></a>Pré-requisitos
Tem de ter um [Service Bus](https://azure.microsoft.com/services/service-bus/) conta.  

Antes de poder utilizar a sua conta do Service Bus do Azure numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta de barramento de serviço. Felizmente, pode fazê-facilmente na sua aplicação lógica no portal do Azure.  

Eis os passos para autorizar a aplicação lógica para ligar à sua conta do Service Bus:  

1. Para criar uma ligação ao Service Bus, no designer de aplicação lógica, selecione **Mostrar Microsoft APIs geridas** na lista pendente. Em seguida, introduza **service bus** na caixa de pesquisa. Selecione o acionador ou a ação que pretende utilizar.  
    ![Imagem de ligação do Service Bus 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Se ainda não criou quaisquer ligações ao Service Bus antes, será solicitado para fornecer as credenciais do Service Bus. Estas credenciais são utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados da sua conta de Service Bus. O conector do Service Bus tem a cadeia de ligação para o espaço de nomes do Service Bus. Também requer **gerir** permissões. Uma boa forma de saber se a cadeia de ligação para o espaço de nomes ou uma entidade específica se contém a `EntityPath` parâmetro. Se existir, não é a cadeia de ligação correta para uma aplicação lógica.  
    ![Cadeia de ligação do Service Bus](./media/connectors-create-api-servicebus/connectionstring.png)
3. Após ter recebido a cadeia de ligação para o espaço de nomes, pode utilizá-lo para a ligação de API em Logic Apps.  
    ![Imagem de ligação do Service Bus 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. Tenha em atenção que a ligação foi criada e está agora livre para continuar com os outros passos da sua aplicação lógica.  
    ![Imagem de ligação do Service Bus 3](./media/connectors-create-api-servicebus/servicebus-3.png)   

