
O projeto de serviço móvel transferido permite-lhe executar o novo serviço móvel diretamente na máquina virtual ou no computador local. Este processo facilita a depuração do código do serviço ainda antes de o publicar no Azure.

Nesta secção, vai testar a nova aplicação com o serviço móvel a ser executado localmente.

1. Navegue até à localização onde guardou os ficheiros comprimidos do projeto, expanda- os no computador e abra o ficheiro da solução no Visual Studio.
2. No Explorador de Soluções do Visual Studio, clique com o botão direito do rato no projeto de serviço, clique em **Definir como Projeto de Arranque** e, em seguida, prima a tecla **F5** para criar o projeto e iniciar o serviço móvel localmente.
   
    ![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)
   
    Após iniciar o serviço móvel com êxito, é apresentada uma página Web.
3. Para testar a aplicação da loja, clique com o botão direito do rato no projeto de aplicação cliente, clique em **Definir como Projeto de Arranque** e, em seguida, prima a tecla **F5** para reconstruir o projeto e iniciar a aplicação.
   
    Esta ação inicia a aplicação, que estabelece ligação à instância do serviço móvel local.   
4. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, em **Inserir um TodoItem** e, em seguida, clique em **Guardar**.
   
    Esta ação envia um pedido POST ao novo serviço móvel local. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo servidor móvel e os dados são apresentados na segunda coluna na aplicação.

<!--HONumber=Sep16_HO3-->


