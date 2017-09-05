
1. Clique no botão **Serviços Aplicacionais**, selecione o back-end das Aplicações Móveis, selecione **Início Rápido**e, em seguida, selecione a plataforma de cliente (iOS, Android, Xamarin, Cordova).

    ![Portal do Azure com Início Rápido de Aplicações Móveis realçado][quickstart]

2. Se não estiver configurada uma ligação de base de dados, crie uma efetuando o seguinte procedimento:

    ![Portal do Azure com Aplicações Móveis Ligar a base de dados][connect]

    a. Crie uma base de dados SQL e o servidor novos.

    ![Portal do Azure com Aplicações Móveis criar base de dados e servidor novos][server]

    b. Aguarde pela conclusão bem-sucedida da ligação de dados.

    ![Notificação do portal do Azure de criação com êxito de ligação de dados][notification]

    c. A ligação de dados tem de ser bem-sucedida.

    ![Notificação do portal do Azure, "Já tem uma ligação de dados"][already-connection]

3. Em **2. Criar uma API de tabela**, selecione Node.js em **Linguagem do back-end**. 
 
4. Aceite a confirmação e, em seguida, selecione **Criar tabela TodoItem**.  
    Esta ação cria uma nova tabela de itens pendentes na base de dados. 

    >[!IMPORTANT]
    > Mudar de um back-end existente para Node.js substitui todos os conteúdos. Para criar um back-end de .NET em, veja [Trabalhar com o servidor SDK de back-end .NET para Aplicações Móveis][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
