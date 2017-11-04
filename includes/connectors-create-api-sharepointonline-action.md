Agora que adicionou um acionador, a respetiva hora de fazer algo interessantes com os dados que são gerados pelo acionador. Siga estes passos para adicionar o **SharePoint Online - criar o ficheiro** ação. Esta ação irá criar um ficheiro no SharePoint Online cada vez que o novo acionador de item é acionado. 

Para configurar esta ação, terá de fornecer as seguintes informações. Como fornecer esta informação, irá reparar que é fácil de utilizar dados gerados pelo acionador como entrada para algumas das propriedades para o novo ficheiro:

| Criar a propriedade de ficheiro | Descrição |
| --- | --- |
| URL do site |Este é o URL do site do SharePoint Online onde pretende criar o novo ficheiro. Selecione o site da lista apresentada. |
| Caminho da pasta |Esta é a pasta (no URL do Site selecionado no passo anterior) onde irá ser colocado o novo ficheiro. Procure e selecione a pasta. |
| Nome de ficheiro |Este é o nome do ficheiro a ser criado. |
| Conteúdo do ficheiro |O conteúdo que será escrito no ficheiro. |

1. Selecione **+ novo passo** para adicionar a ação.  
   ![Imagem de ação online do SharePoint 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Selecione o **adicionar uma ação** ligação. Este é aberta a caixa de pesquisa, onde pode procurar qualquer ação pretende efetuar. Neste exemplo, as ações do SharePoint são de interesse.    
   ![Imagem de ação online do SharePoint 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Introduza *sharepoint* para procurar ações relacionadas com o SharePoint.
4. Selecione **SharePoint Online - criar o ficheiro** como a ação a tomar.   **Tenha em atenção**: lhe-á para autorizar a aplicação lógica para aceder à sua conta do SharePoint, se não tiver criado uma ligação ao SharePoint Online anteriormente.    
   ![Imagem de ação online do SharePoint 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. O **criar ficheiro** controlar é aberto.   
   ![Imagem de ação online do SharePoint 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Selecione **URL do Site** e procure para localizar o site onde pretende criar o ficheiro.     
   ![Imagem de ação online do SharePoint 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Selecione **caminho da pasta** e procura para localizar a pasta onde irá ser colocado o novo ficheiro.  
   ![Imagem de ação online do SharePoint 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Selecione o **nome de ficheiro** controlar e introduza o nome do ficheiro que pretende criar. Aqui, pode introduzir o nome de ficheiro diretamente ou pode utilizar qualquer uma das propriedades do acionador que criou anteriormente. Fazê-lo ao selecionar propriedades da lista de **saídas da quando é criado um novo item**. Esta lista é apresentar apenas depois de selecionar o **nome de ficheiro** controlo. Neste walkthough, posso selecionado ID (ID do novo item da lista) como o nome do ficheiro a ser criado pelo **SharePoint Online - criar o ficheiro** ação.    
   ![Imagem de ação online do SharePoint 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Selecione o **conteúdo do ficheiro** controlar e introduza o conteúdo que será escrito para o ficheiro que será criado. Para o conteúdo do ficheiro, tenha em atenção que pode utilizar qualquer uma das propriedades do acionador que criou anteriormente. Selecione simplesmente as propriedades da lista apresentada. Em alternativa, pode introduzir o **conteúdo do ficheiro** texto diretamente para o controlo. Neste exemplo, posso selecionado algumas propriedades e adicionar os espaços e um hífen entre cada propriedade.        
   ![Imagem de ação online do SharePoint 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Guardar as alterações ao seu fluxo de trabalho  
11. Parabéns, tem agora uma aplicação de lógica completamente funcional que é acionada quando é adicionado um novo item a uma lista do SharePoint Online. A aplicação, em seguida, irá criar um ficheiro, com algumas das propriedades do item de lista nova.  Agora pode testá-lo ao criar um novo item na lista do SharePoint. 

