### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Twilio
* Um número de telefone Twilio verificado que pode receber de SMS
* Um número de telefone Twilio verificado que pode enviar SMS

> [!NOTE]
> Se estiver a utilizar uma conta de avaliação do Twilio, só pode enviar SMS para **verificado** números de telefone.  
> 
> 

Antes de poder utilizar a sua conta do Twilio numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta do Twilio. Felizmente, pode fazê-facilmente na sua aplicação lógica no Portal do Azure. 

Eis os passos para autorizar a aplicação lógica para ligar à sua conta do Twilio:

1. Para criar uma ligação ao Twilio, no designer de aplicação lógica, selecione **Mostrar Microsoft APIs geridas** na lista pendente, em seguida, introduza *Twilio* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Se ainda não criou quaisquer ligações a Twilio antes, irá obter lhe fornecer as credenciais do Twilio. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados do sua conta Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Terá do **id de conta do Twilio** e **token de acesso do Twilio** do dashboard do Twilio, por isso, iniciar sessão na sua conta do Twilio agora para captar duas estas informações:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Aplicações Twilio e lógica utilizam diferentes nomes para identificar duas estas informações. Eis como tem de mapeá-los para a caixa de diálogo de aplicações lógicas:![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Selecione o **criar ligação** botão:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Repare a ligação foi criada e está agora livre para continuar com os outros passos da sua aplicação lógica:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

