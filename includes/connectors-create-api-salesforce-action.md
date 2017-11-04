Agora que adicionou uma condição, a respetiva hora de fazer algo interessantes com os dados que são gerados pelo acionador. Siga estes passos para adicionar o **Salesforce - objeto Get** ação. Esta ação irá obter os dados de cada vez que é criada uma novo antecedência. Também irá adicionar uma segunda ação que irá utilizar os dados do Salesforce - Get uma ação de objeto para enviar uma mensagem de e-mail através do conector do Office 365.  

Para configurar esta ação, terá de fornecer as seguintes informações. Vai notar que é fácil de utilizar dados gerados pelo acionador como entrada para algumas das propriedades para o novo ficheiro:

| Criar a propriedade de ficheiro | Descrição |
| --- | --- |
| Tipo de objeto |Este é o tipo de objeto do Salesforce que está interessado. Os exemplos são oportunidades potenciais, a conta, etc. |
| ID de objeto |Representa um identificador para o objeto. |

1. Selecione **adicionar uma ação** ligação. Este é aberta a caixa de pesquisa, onde pode procurar qualquer ação pretende efetuar. Neste exemplo, Salesforce ações são úteis.      
   ![Imagem da ação Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Introduza *salesforce* para procurar ações relacionadas com o salesforce.
3. Selecione **Salesforce - objeto Get** como a ação a tomar.   **Tenha em atenção**: lhe-á para autorizar a aplicação lógica para aceder à sua conta do Salesforce, se não o fez, anteriormente.    
   ![Imagem da ação Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)    
4. O **Get objeto** controlar é aberto.  
5. Selecione *levar* como o tipo de objeto.
6. Selecione o **ID de objeto** controlo.
7. Selecione **...**  para expandir a lista de tokens que podem ser utilizadas como entrada para ações.       
   ![Imagem da ação Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Selecione **levar ID** controlar é aberto.   
   ![Imagem da ação Salesforce 4](./media/connectors-create-api-salesforce/action-4.png)     
9. Tenha em atenção que o token de ID levar está agora no controlo da ID de objeto, que indica que a ação de objeto Get irá procurar uma antecedência com um ID que é igual ao ID de fabrico de oportunidades potenciais que acionou esta aplicação lógica.  
   ![Imagem da ação Salesforce 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Guarde o seu trabalho. Já está, adicionou a ação de objeto Get a sua aplicação lógica. O controlo de objeto Get deve ter o seguinte aspeto:    
    ![Imagem da ação Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)  

Agora que adicionou uma ação para obter uma antecedência, poderá pretender fazer algo interessante com as oportunidades potenciais recentemente criado. Numa empresa, poderá pretender enviar um e-mail para notificar uma lista de distribuição que foi criada uma antecedência de novo. Vamos utilizar o conector do Office 365 para enviar um e-mail com algumas das informações relevantes do novo objeto de oportunidades potenciais na Salesforce.  

1. Selecione **adicionar uma ação** , em seguida, introduza *e-mail* no controlo da pesquisa. Este procedimento filtra as ações que estão relacionados com a enviar e receber correio eletrónico.  
2. Selecione o **Office 365 Outlook - envie um e-mail** item da lista. Se já não criou um *ligação* à sua conta Office 365, será solicitado para introduzir as suas credenciais do Office 365 a criá-la agora. Depois de terminar, o **enviar um e-mail** controlar é aberto.        
   ![Imagem da ação Salesforce 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Introduza o endereço de e-mail que pretende enviar correio eletrónico no **para** controlo.
4. No **requerente** controlar, introduza *levar nova criada* -, em seguida, selecione o *empresa* token. Este será apresentado o *empresa* campo a partir de fabrico nova criado no Salesforce.  
5. No **corpo** controlo, pode selecionar qualquer um dos tokens a partir do novo objeto de oportunidades potenciais e também pode introduzir qualquer texto que pretende apresentar no corpo da mensagem de e-mail. Eis um exemplo:  
   ![Imagem da ação Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)   
6. Guarde o fluxo de trabalho.  

E já está. A aplicação lógica está agora concluída.  

Agora, pode testar a sua aplicação lógica: Salesforce, criar uma novo antecedência que satisfaça a condição que criou.  Se seguiu totalmente esta passagem, acabou de criar uma antecedência com um endereço de correio eletrónico que contém *amazon.com* no mesmo. Após alguns segundos deve ser acionada a sua aplicação lógica e os resultados podem ter um aspeto semelhantes a isto:  
![Imagem da ação Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)  

