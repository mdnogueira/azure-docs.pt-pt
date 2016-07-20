
A etapa final deste tutorial consiste em criar e executar a nova aplicação.

1. Navegue até à localização onde guardou os ficheiros comprimidos do projeto, expanda- os no computador e abra o ficheiro da solução no Visual Studio.

2. Prima a tecla **F5** para reconstruir o projeto e iniciar a aplicação.

3. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, em **Inserir um TodoItem** e, em seguida, clique em **Guardar**.

    Esta ação envia um pedido POST ao novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo servidor móvel e os dados são apresentados na segunda coluna na aplicação.

4. (Opcional) Numa solução universal do Windows, altere o projeto arranque predefinido para a outra aplicação e execute novamente a aplicação.

    Repare que os dados guardados no passo anterior são carregados do serviço móvel depois de a aplicação iniciar.
 
4. No [Portal Clássico do Azure](https://manage.windowsazure.com/), clique no separador **Dados** e na tabela **TodoItems**.

    Esta ação permite procurar os dados inseridos pela aplicação na tabela.

    ![](./media/mobile-services-javascript-backend-run-app/mobile-data-browse.png)


<!--HONumber=Jun16_HO2-->


