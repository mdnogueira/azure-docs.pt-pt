### <a name="grant-access-to-your-push-certificate-to-mobile-engagement"></a>Conceder ao Certificado Push acesso ao Mobile Engagement
Para permitir que o Mobile Engagement envie Notificações Push em seu nome, tem de conceder acesso ao certificado. Para tal, deve configurar e introduzir o certificado no portal de Mobile Engagement. Não se esqueça de obter o certificado .p12 conforme explicado na [Documentação da Apple](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6).

1. Navegue até ao portal do Mobile Engagement. Confirme se está no portal correto e clique no botão **Iniciar** na parte inferior:
   
    ![](./media/mobile-engagement-ios-send-push/engage-button.png)
2. Clique na página **Definições** no Portal do Engagement. Aqui, clique na secção **Push Nativo** para carregar o certificado p12:
   
    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)
3. Selecione o p12, carregue-o e escreva a palavra-passe:
   
    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

## <a name="a-idsendasend-a-notification-to-your-app"></a><a id="send"></a>Enviar uma notificação à aplicação
Vamos agora criar uma campanha de Notificações Push simples para enviar uma notificação push à nossa aplicação:

1. Navegue até ao separador **Alcance** no portal do Mobile Engagement.
2. Clique em **Novo Anúncio** para criar a campanha push.
   
    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)
3. Configure os primeiros campos da campanha:
   
    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)
   
   * Forneça um **Nome** para a campanha. 
   * Selecione o **Tempo de entrega** como **Apenas fora da aplicação**: este é o tipo de notificação push simples da Apple que inclui algum texto.
   * No tipo de texto de notificação, escreva primeiro o **Título** que será a primeira linha no push.
   * Em seguida, escreva a **Mensagem** que será a segunda linha
4. Desloque o ecrã para baixo e, na secção Conteúdo, selecione **Apenas notificação**
   
    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)
5. Terminou a configuração da campanha mais básica. Desloque o ecrã novamente para baixo e clique no botão **Criar** para guardar a campanha de notificações push. 
6. Por fim, clique em **Ativar** para enviar a notificação push. 
   
    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)
7. Poderá receber a seguinte notificação do dispositivo iOS no centro de notificações:
   
    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)
8. Se tiver um Apple Watch emparelhado com este dispositivo iOS, verá a notificação no Apple Watch:
   
    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)



<!--HONumber=Nov16_HO2-->


