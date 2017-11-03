

1. No Mac, inicie **acesso Keychain**. Na barra de navegação esquerdo, em **categoria**, abra **certificados My**. Localizar o certificado SSL que transferiu na secção anterior e indicar o respetivo conteúdo. Selecione apenas o certificado (não selecione a chave privada), e [exportá-lo](https://support.apple.com/kb/PH20122?locale=en_US).
2. No [portal do Azure](https://portal.azure.com/), clique em **Procurar tudo** > **serviços aplicacionais**e clique em seu back-end do Mobile Apps. Em **definições**, clique em **Push do serviço de aplicações**e, em seguida, clique em seu nome de hub de notificação. Aceda a **notificações push da Apple** > **carregar certificado**. Carregue o ficheiro. p12, selecionando o correto **modo** (dependendo se o certificado de cliente SSL, do anteriormente é produção ou sandbox). Guarde as alterações.

O serviço está agora configurado para trabalhar com as notificações push no iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
