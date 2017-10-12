### <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Conceder ao Mobile Engagement acesso à Chave de API do GCM
Para permitir que o Mobile Engagement envie notificações push em seu nome, tem de lhe conceder acesso à sua Chave de API. Para tal, deve configurar e introduzir a sua chave no portal do Mobile Engagement.

1. No Portal Clássico do Azure, verifique se está na aplicação que estamos a utilizar para este projeto e clique no botão **Iniciar** na parte inferior:
   
    ![](./media/mobile-engagement-android-send-push/engage-button.png)
2. Em seguida, clique na secção **Definições** -> **Push Nativo** para introduzir a Chave do GCM:
   
    ![](./media/mobile-engagement-android-send-push/engagement-portal.png)
3. Clique no ícone **Editar** à frente da **Chave de API** na secção **Definições do GCM**, como mostrado abaixo:
   
    ![](./media/mobile-engagement-android-send-push/native-push-settings.png)
4. No pop-up, cole a Chave de Servidor do GCM que obteve anteriormente e clique em **OK**.
   
    ![](./media/mobile-engagement-android-send-push/api-key.png)

## <a id="send"></a>Enviar uma notificação à aplicação
Vamos agora criar uma campanha de notificações push simples para enviar uma notificação push à nossa aplicação.

1. Navegue até ao separador **ALCANCE** no portal do Mobile Engagement.
2. Clique em **Novo anúncio** para criar a campanha de notificações push.
   
    ![](./media/mobile-engagement-android-send-push/new-announcement.png)
3. Configure o primeiro campo da campanha com os seguintes passos:
   
    ![](./media/mobile-engagement-android-send-push/campaign-first-params.png)
   
    a. Dê um nome à campanha.
   
    b. Selecione o **Tipo de entrega** como *Notificação do sistema -> Simples*: este é o tipo de notificação push Android simples que inclui um título e uma pequena linha de texto.
   
    c. Selecione **Tempo de entrega** como *Em qualquer altura* para permitir que a aplicação receba uma notificação quer esteja ou não em execução.
   
    d. No tipo de texto da notificação, escreva o **Título** que aparecerá em negrito no push.
   
    e. Em seguida, escreva a **Mensagem**
4. Desloque o ecrã para baixo e, na secção **Conteúdo**, selecione **Apenas notificação**.
   
    ![](./media/mobile-engagement-android-send-push/campaign-content.png)
5. Acabou de configurar a campanha mais básica possível. Agora, desloque-se para baixo novamente e clique no botão **Criar** para guardar a sua campanha.
6. Último passo: clique em **Ativar** para ativar a campanha para enviar notificações push.
   
    ![](./media/mobile-engagement-android-send-push/campaign-activate.png)

