
1. Navegue até à [Consola do Google Cloud](https://console.developers.google.com/project) e inicie sessão com as credenciais da conta Google. 
2. Clique em **Criar Projeto**, escreva um nome de projeto e, em seguida, clique em **Criar**. Se solicitado, execute a Verificação por SMS e clique novamente em **Criar**.
   
    ![Criar novo projeto](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     Escreva o novo **Nome do projeto** e clique em **Criar projeto**.
3. Clique no botão **Utilitários e Mais** e, em seguida, em **Informações do Projeto**. Anote o **Número de Projeto**. Terá de definir este valor como a variável `SenderId` na aplicação cliente.
   
    ![Utilitários e muito mais](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. No dashboard do projeto, em **APIs Móveis**, clique em **Google Cloud Messaging**, na página seguinte, clique em **Ativar API** e aceite os termos de serviço. 
   
    ![Ativar o GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Ativar o GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. No dashboard do projeto, clique em **Credenciais** > **Criar Credencial** > **Chave de API**. 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. Em **Criar uma nova chave**, clique em **Chave de servidor**, escreva um nome para a chave e clique em **Criar**.
7. Anote o valor da **CHAVE DE API**.
   
    Vai utilizar este valor para permitir ao Azure a autenticação com o GCM e o envio de notificações push em nome da aplicação.

