
1. No [portal do Azure](https://portal.azure.com/), clique em **Procurar tudo** > **serviços aplicacionais**e clique em seu back-end do Mobile Apps. Em **definições**, clique em **Push do serviço de aplicações**e, em seguida, clique em seu nome de hub de notificação.
2. Aceda a **Windows (WNS)**, introduza o **a chave de segurança** (segredo do cliente) e **SID do pacote** obtido a partir do site dos Serviços Live e, em seguida, clique em **guardar** .

    ![Defina a chave do WNS no portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

O back-end está agora configurado para utilizar o WNS para enviar notificações push.
