#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* A [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) conta 

Antes de utilizar a conta de Dynamics uma aplicação lógica, autorize a aplicação lógica para ligar à sua conta do CRM Online. Pode fazê-facilmente na sua aplicação lógica no portal do Azure. 

Autorize a aplicação lógica para ligar à sua conta CRM Online através dos seguintes passos:

1. Crie uma aplicação lógica. No designer Logic Apps, selecione **Mostrar Microsoft APIs geridas** na lista pendente lista e, em seguida, introduza "dynamics" na caixa de pesquisa. Selecione um dos acionadores ou ações:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Se ainda não criou anteriormente todas as ligações ao Dynamics, lhe for pedido para iniciar sessão com as suas credenciais de Dynamics:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Selecione **sessão**e introduza o nome de utilizador e palavra-passe. Selecione **sessão**. 
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados na sua conta do Dynamics. 
4. Tenha em atenção de que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

