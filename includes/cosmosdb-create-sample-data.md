Pode agora utilizar o Data Explorer para adicionar dados à sua coleção nova.

1. No Data Explorer, a base de dados nova aparece no painel Coleções. Expanda a base de dados **Itens**, expanda a coleção **ToDoList**, clique em **Documentos** e clique em **Documentos Novos**. 

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Agora, adicione alguns documentos novos à coleção com a estrutura seguinte, em que vai inserir valores exclusivos para o ID de cada documento e alterar as outras propriedades, conforme ache necessário. Agora, os documentos podem ter qualquer estrutura que queira criar, uma vez que o Azure Cosmos DB não impõe qualquer esquema aos seus dados.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     Pode utilizar agora consultas no Data Explorer para obter os seus dados. Por predefinição, o Data Explorer utiliza SELECT * FROM c para obter todos os documentos da coleção, mas pode alterar para SELECT * FROM c ORDER BY c.name ASC, de modo a devolver todos os documentos por ordem alfabética crescente da propriedade do nome. 
 
     Também pode utilizar o Data Explorer para criar procedimentos armazenados, UDFs e acionadores, para realizar lógica empresarial do lado do servidor. O Data Explorer expõe todos os acessos a dados programáticos incorporados que estão disponíveis nas APIs, mas disponibiliza acesso fácil aos seus dados no portal do Azure.