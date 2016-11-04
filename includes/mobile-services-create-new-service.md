

Siga estes passos para criar um novo serviço móvel.

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/). Na parte inferior do painel de navegação, clique em **+NOVO**. Expanda **Computação** e **Serviço Móvel** e clique em **Criar**.
   
   ![](./media/mobile-services-create-new-service/mobile-create.png)
   
   Será apresentada a caixa de diálogo **Criar um Serviço Móvel**.
2. Na caixa de diálogo **Criar um Serviço Móvel**, selecione **Criar uma Base de Dados SQL de 20 MB gratuita**, selecione tempo de execução **JavaScript** e escreva um nome de subdomínio para o novo serviço móvel na caixa de texto **URL**. Clique no botão de seta para a direita para ir para a página seguinte.
   
   ![](./media/mobile-services-create-new-service/mobile-create-page1.png)
   
   Será apresentada a página **Especificar definições da base de dados**.
   
   > [!NOTE]
   > Como parte deste tutorial, deverá criar uma nova instância da Base de Dados SQL e um servidor. Poderá reutilizar esta nova base de dados e administrá-la tal como faria com qualquer outra instância da Base de Dados SQL. Se já tiver uma base de dados na mesma região do novo serviço móvel, pode, em vez disso, escolher **Utilizar Base Dados existente** e selecionar essa base de dados. Não recomendamos a utilização de uma base de dados numa região diferente, devido a custos adicionais de largura de banda e latências maiores.
   > 
   > 
3. Em **Nome**, escreva o nome da nova base de dados e, em seguida, escreva o **Nome de início de sessão**, que é o nome início de sessão do administrador do novo servidor da Base de Dados SQL. Escreva e confirme a palavra-passe e clique no botão de verificação para concluir o processo.
   ![](./media/mobile-services-create-new-service/mobile-create-page2.png)

Acabou de criar um novo serviço móvel que pode ser utilizado pelas suas aplicações móveis.

<!--HONumber=Jun16_HO2-->


