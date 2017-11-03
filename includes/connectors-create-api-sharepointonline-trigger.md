Neste exemplo, posso irá mostrar-lhe como utilizar o **SharePoint Online - quando é criado um novo item** trigger para iniciar um fluxo de trabalho de aplicação lógica, quando é criado um novo item de uma lista do SharePoint Online.

> [!NOTE]
> Obter-á pedido para iniciar sessão na sua conta do SharePoint, se não tiver já criado uma *ligação* ao SharePoint Online.  
> 
> 

1. Introduza *sharepoint* na caixa de pesquisa no designer de aplicações lógicas, em seguida, selecione o **SharePoint Online - quando é criado um novo item** acionador.  
   ![Imagem de Acionador online do SharePoint](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. O **quando é criado um novo item** controlo é apresentado.  
   ![Imagem de Acionador online do SharePoint 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Selecione um **URL do Site**. Este é o site online do SharePoint que pretende monitorizar para novos itens acionar o fluxo de trabalho.  
   ![Imagem de Acionador online do SharePoint 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Selecione um **nome lista**. Esta é a lista no site do SharePoint Online que pretende monitorizar os novos itens que activarão o fluxo de trabalho.  
   ![Imagem de Acionador online do SharePoint 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Neste momento, a aplicação lógica foi configurada com um acionador que irá iniciar a execução dos outros acionadores e ações no fluxo de trabalho. Isto ocorrerá sempre que é criado um novo item na lista SharePoint Online que selecionou.  

