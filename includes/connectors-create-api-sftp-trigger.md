Vamos adicionar um acionador.

1. Introduza *sftp* na caixa de pesquisa no designer de aplicações lógicas, em seguida, selecione o **SFTP - quando um ficheiro é adicionado ou modificado** acionador   
   ![Imagem de Acionador SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. O **quando um ficheiro é adicionado ou modificado** controlo abre-se  
   ![Imagem de Acionador SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. Selecione o **...**  localizado no lado direito de controlo. Esta ação abre o controlo de selecionador de pasta  
   ![Imagem de Acionador SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
4. Selecione o **SFTP** para selecionar a pasta de raiz como a pasta para monitorizar os ficheiros novos ou modificados. Tenha em atenção a pasta de raiz é apresentada no **pasta** controlo.  
   ![Imagem de Acionador SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

Neste momento, a aplicação lógica foi configurada com um acionador que irá iniciar a execução dos outros acionadores e ações no fluxo de trabalho quando um ficheiro é modificado ou criado na pasta SFTP específica. 

> [!NOTE]
> Para uma aplicação lógica que seja funcional, tem de conter pelo menos um acionador e uma ação. Siga os passos na secção seguinte para adicionar uma ação.  
> 
> 

