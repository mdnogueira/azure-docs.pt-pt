
1. Clique em **Serviços Aplicacionais** > selecione o back-end da sua Aplicação Móvel > clique em **Início rápido** > plataforma cliente (iOS, Android, Xamarin, Cordova).

![Portal do Azure com Início Rápido de Aplicações Móveis realçado][quickstart]

2. Se a ligação à base de dados não estiver configurada, tem de criar uma Ligação de Dados.

![Portal do Azure com Aplicações Móveis Ligar a DB][connect]

  * Crie a Base de Dados SQL e o servidor novos.

  ![Portal do Azure com Aplicações Móveis criar DB e servidor novos][server]

  * Aguarde pela conclusão bem-sucedida da ligação de dados.

  ![Portal do Azure com Aplicações Móveis notificação sobre criação de ligação de dados][notification]

  * A ligação de dados tem de ser bem-sucedida.

  ![Portal do Azure com Aplicações Móveis notificação sobre criação de ligação de dados][already-connection]

3. Em **2. Criar uma API de tabela**, selecione Node.js em **Linguagem do back-end**. Aceite a confirmação e clique em **Criar tabela TodoItem**. Esta ação cria uma nova tabela *TodoItem* na base de dados. Tenha em atenção que se alterar um back-end existente para o Node.js, todo o conteúdo será substituído! Para criar um back-end .NET como alternativa, [siga estas instruções][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
