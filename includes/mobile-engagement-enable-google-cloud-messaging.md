
###Criar um projeto do Google Cloud Messaging com a chave de API

>[AZURE.NOTE] Para concluir este procedimento, tem de ter uma conta do Google que tenha um endereço de correio eletrónico verificado. Para criar uma nova conta do Google, aceda a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue até à [Consola do Google na Nuvem](https://console.developers.google.com/project) e inicie sessão com as suas credenciais da conta do Google.

2. Aceda a **Todos os Projetos** e, em seguida, clique em **Criar Projeto**.

3. Introduza um **Nome do projeto** e clique em **Criar**

4. Depois de o projeto ser criado, certifique-se de que anota o **Número do projeto** que será um valor numérico extenso. Pode encontrá-lo na **secção IAM & Admin** nas **Definições** do seu Projeto e irá precisar dele posteriormente. 
 
    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. Iremos agora criar uma chave para a plataforma Google Cloud Messaging, que será utilizada pela nossa plataforma para enviar notificações para os dispositivos Android. Aceda à secção **Gestor de API** e clique em **Google Cloud Messaging** em **APIs Móveis**. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. Na página seguinte, clique no botão **Ativar**. O dashboard irá pedir-lhe para criar as credenciais. Clique no botão **Ir para as Credenciais**. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Selecione **Google Cloud Messaging** na primeira caixa pendente e **Servidor Web** na segunda. Em seguida, clique em **De que credenciais preciso?**

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. Na página **Adicionar credenciais ao projeto**, clique em **Criar chave de API**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Anote o valor da **CHAVE DE API**. Irá utilizar este valor de chave de API mais tarde para configurar a secção “Push Nativo”. Agora, clique em **Concluído**.



<!--HONumber=Aug16_HO1-->


