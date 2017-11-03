### <a name="prerequisites"></a>Pré-requisitos
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) conta  

Antes de poder utilizar a conta de SMTP numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta de SMTP. Felizmente, pode fazê-facilmente na sua aplicação lógica no Portal do Azure.  

Eis os passos para autorizar a aplicação lógica para ligar à sua conta de SMTP:  

1. Para criar uma ligação a SMTP, no designer de aplicação lógica, selecione **Mostrar Microsoft APIs geridas** na lista pendente, em seguida, introduza *SMTP* na caixa de pesquisa. Selecione o acionador ou ação que irá gostar a utilizar:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Se ainda não criou quaisquer ligações a SMTP antes, irá obter lhe fornecer as credenciais de SMTP. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados da sua conta SMTP:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Repare a ligação foi criada e está agora livre para continuar com os outros passos da sua aplicação lógica:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

