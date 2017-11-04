#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* A [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) conta 

Antes de poder utilizar a sua conta do OneDrive numa aplicação lógica, autorize a aplicação lógica para ligar à sua conta do OneDrive.  Pode fazê-facilmente na sua aplicação lógica no portal do Azure. 

Autorize a aplicação lógica para ligar à sua conta do OneDrive utilizando os seguintes passos:

1. Crie uma aplicação lógica. No designer Logic Apps, selecione **Mostrar Microsoft APIs geridas** na lista pendente lista e, em seguida, introduza "onedrive" na caixa de pesquisa. Selecione um dos acionadores ou ações:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se ainda não criou anteriormente todas as ligações para o OneDrive, lhe for pedido para iniciar sessão com as suas credenciais do OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **sessão**e introduza o nome de utilizador e palavra-passe. Selecione **sessão**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para ligar a e aceder aos dados na sua conta do OneDrive. 
4. Selecione **Sim** para autorizar a aplicação lógica para utilizar a sua conta do OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Tenha em atenção de que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

