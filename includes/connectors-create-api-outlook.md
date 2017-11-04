## <a name="connect-to-outlookcom"></a>Ligar ao Outlook.com
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Outlook.com

Antes de poder utilizar a sua conta do Outlook.com numa aplicação lógica, tem de autorizar a aplicação lógica para ligar à sua conta do Outlook.com. Felizmente, pode fazê-facilmente na sua aplicação lógica no Portal do Azure. 

Eis os passos para autorizar a aplicação lógica para ligar à sua conta do Outlook.com:

1. Todas as Logic apps precisam de ser iniciados por um acionador para que, depois de criar a aplicação lógica, o estruturador abre-se e apresenta uma lista de aciona que pode utilizar para iniciar a sua aplicação lógica:
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Introduza "outlook" na caixa de pesquisa. Tenha em atenção de que a lista está filtrada para listar todos os acionadores com "Outlook" no nome:![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Selecione **novo e-mail do Office 365 Outlook -**.   
   Se ainda não criou quaisquer ligações a Outlook antes, irá obter lhe fornecer as credenciais do Outlook.com. Estas credenciais serão utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados da sua conta Outlook.com:![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Forneça as credenciais para o Outlook e iniciar sessão:![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   E já está. Agora que criou uma ligação para o Outlook. Esta ligação estará disponível para utilização em qualquer outra aplicação de lógica que criar.

