### <a name="prerequisites"></a>Pré-requisitos
* A [SendGrid](https://www.SendGrid.com/) conta 

Antes de poder utilizar a sua conta do SendGrid numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta do SendGrid. Felizmente, pode fazê-facilmente na sua aplicação lógica no Portal do Azure. 

Eis os passos para autorizar a aplicação lógica para ligar à sua conta do SendGrid:

1. Para criar uma ligação a SendGrid, no designer de aplicação lógica, selecione **Mostrar Microsoft APIs geridas** na lista pendente, em seguida, introduza *SendGrid* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![SendGrid passo 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Se ainda não criou quaisquer ligações a SendGrid antes, pode obter-á pedido para fornecer as credenciais do SendGrid. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados da sua conta SendGrid:  
   ![SendGrid passo 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Repare a ligação foi criada e está agora livre para continuar com os outros passos da sua aplicação lógica:  
   ![SendGrid passo 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

