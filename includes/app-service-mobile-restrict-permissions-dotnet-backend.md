
Por predefinição, APIs de um back-end de Mobile Apps pode ser invocadas anonimamente. Em seguida, precisa de restringir o acesso apenas a clientes autenticado.  

* **NODE.js novamente end (através do portal do Azure)** :  

    Nas suas definições de Mobile Apps, clique em **tabelas fácil** e selecione a tabela. Clique em **alterar permissões**, selecione **apenas o acesso autenticado** para todas as permissões e, em seguida, clique em **guardar**.
* **.NET back-end (c#)**:  

    No projeto de servidor, navegue para **controladores** > **TodoItemController.cs**. Adicionar o `[Authorize]` atributo para o **TodoItemController** classe, da seguinte forma. Para restringir o acesso apenas aos métodos específicos, também pode aplicar este atributo apenas para os métodos em vez da classe. Voltar a publicar o projeto de servidor.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end node.js (através de código Node.js)** :  

    Para exigir autenticação para acesso de tabela, adicione a seguinte linha para o script do Node.js:

        table.access = 'authenticated';

    Para obter mais detalhes, consulte [como: exigir autenticação para aceder a tabelas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para saber como transferir o projeto de código de início rápido do seu site, consulte [como: Transferir o projeto de código do início rápido do Node.js back-end utilizando o Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
